---
title: Kurz – testování modulů Terraformu v Azure pomocí Terratest
description: Zjistěte, jak pomocí Terratestu testovat moduly Terraformu.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 41f7f9c00f626cf622ea781f01da6db1f46cd805
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158958"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Kurz: testování modulů Terraformu v Azure pomocí Terratest

> [!NOTE]
> Vzorový kód v tomto článku nepracuje s verzí 0,12 (a vyšší).

Moduly Azure Terraformu můžete použít k vytvoření opakovaně použitelných, sestavitelných a testovatelné komponent. Terraformu moduly zahrnují zapouzdření, které je užitečné při implementaci infrastruktury jako procesů kódu.

Je důležité implementovat zabezpečování kvality při vytváření Terraformu modulů. K dispozici je bohužel omezená dokumentace, která vysvětluje, jak vytvářet testy jednotek a testy integrace v Terraformuch modulech. V tomto kurzu se seznámíte s infrastrukturou testování a osvědčenými postupy, které jsme přijali, když jsme vytvořili naše [moduly Azure terraformu](https://registry.terraform.io/browse?provider=azurerm).

Prohledali jsme všechny nejoblíbenější testovací infrastruktury a zvolili [Terratest](https://github.com/gruntwork-io/terratest) pro testování našich terraformu modulů. Terratest se implementuje jako knihovna Go. Terratest poskytuje kolekci pomocných funkcí a vzorů pro běžné úlohy testování infrastruktury, jako je vytváření požadavků HTTP a používání SSH pro přístup k určitému virtuálnímu počítači. Následující seznam popisuje některé z hlavních výhod používání Terratest:

- **Poskytuje pohodlným pomocníkům pro kontrolu infrastruktury**. Tato funkce je užitečná v případě, že chcete ověřit skutečnou infrastrukturu ve skutečném prostředí.
- **Struktura složek je jasně uspořádaná**. Vaše testovací případy jsou uspořádány jasně a řídí [standardní strukturu složek modulu terraformu](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Všechny testovací případy jsou zapsané v cestách**. Většina vývojářů, kteří používají Terraformu, patří vývojářům. Pokud jste vývojář, nemusíte se učit s jiným programovacím jazykem, abyste mohli používat Terratest. Také jediné závislosti, které jsou požadovány pro spuštění testovacích případů v Terratest, jsou Terraformu.
- **Infrastruktura je vysoce rozšiřitelná**. Další funkce můžete roztáhnout nad Terratest, včetně funkcí specifických pro Azure.

## <a name="prerequisites"></a>Požadavky

Tento praktický článek je nezávislý na platformě. Příklady kódu, které používáme v tomto článku, můžete spustit v systému Windows, Linux nebo MacOS. 

Než začnete, nainstalujte následující software:

- **Jazyk programovacího jazyka**: terraformu testové případy jsou zapsané v [cestách](https://golang.org/dl/).
- **dep:** [dep](https://github.com/golang/dep#installation) je nástroj pro správu závislostí pro Go.
- **Azure CLI**: [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) je nástroj příkazového řádku, který můžete použít ke správě prostředků Azure. (Terraformu podporuje ověřování v Azure prostřednictvím instančního objektu nebo [prostřednictvím rozhraní příkazového řádku Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html))
- **Mage**: používáme [spustitelný soubor Mage](https://github.com/magefile/mage/releases) k tomu, abychom vám ukázali, jak zjednodušit spuštění Terratest případů. 

## <a name="create-a-static-webpage-module"></a>Vytvoření modulu statického webu

V tomto kurzu vytvoříte modul Terraformu, který zřídí statickou webovou stránku, a to nahráním jednoho souboru HTML do objektu blob Azure Storage. Tento modul poskytuje uživatelům přístup k webové stránce z celého světa prostřednictvím adresy URL, kterou modul vrací.

> [!NOTE]
> Vytvořte všechny soubory popsané v této části v umístění [gopath tak](https://github.com/golang/go/wiki/SettingGOPATH) .

Nejprve ve složce Gopath tak `src` vytvořte novou složku s názvem `staticwebpage`. V následujícím příkladu je uvedena celková struktura složek tohoto kurzu. Soubory označené hvězdičkou `(*)` jsou hlavním fokusem v této části.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Modul statických webových stránek přijímá tři vstupy. Vstupy jsou deklarovány v `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Jak jsme se už dozvěděli v tomto článku, tento modul také vypíše adresu URL, která je deklarována v `./outputs.tf`:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

Hlavní logika modulu zřídí čtyři prostředky:
- **Skupina prostředků**: název skupiny prostředků je vstup `website_name` připojen pomocí `-staging-rg`.
- **účet úložiště**: název účtu úložiště je `website_name`m vstupem připojeným pomocí `data001`. Aby se v souladu s omezeními názvů účtu úložiště, modul odstraní všechny speciální znaky a v celém názvu účtu úložiště použije malá písmena.
- **kontejner s pevným názvem**: kontejner je pojmenován `wwwroot` a je vytvořen v účtu úložiště.
- **jeden soubor HTML**: soubor HTML se načte z `html_path` vstupu a nahraje do `wwwroot/index.html`.

Logika modulu statického webu se implementuje v souboru `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Test jednotek

Terratest je navržen pro integrační testy. Pro tento účel Terratest zřídí reálné prostředky v reálném prostředí. V některých případech se může stát, že úlohy integračních testů budou velmi velké, zejména pokud máte velký počet prostředků ke zřízení. Dobrým příkladem je logika, která převede názvy účtů úložiště, na které odkazujeme v předchozí části. 

Ale opravdu nepotřebujeme zřídit žádné prostředky. Chceme mít jistotu, že logika převodu názvů je správná. Díky flexibilitě Terratest můžeme použít jednotkové testy. Testy jednotek jsou místní běžící testovací případy (i když je vyžadován přístup k Internetu). Testovací případy jednotek provádějí `terraform init` a `terraform plan` příkazy k analýze výstupu `terraform plan` a hledání hodnot atributů, které se mají porovnat.

Zbytek této části popisuje, jak používáme Terratest k implementaci testu jednotek, abyste se ujistili, že logika používaná k převodu názvů účtů úložiště je správná. Zajímá Vás pouze soubory označené hvězdičkou `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Nejprve použijeme prázdný soubor HTML s názvem `./test/fixtures/storage-account-name/empty.html` jako zástupný symbol.

Soubor `./test/fixtures/storage-account-name/main.tf` je rámec testovacího případu. Přijímá jednu vstupní `website_name`, což je také vstup testů jednotek. Tady je zobrazená logika:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

Hlavní součást je implementace testování částí v `./test/storage_account_name_unit_test.go`.

V případě vývojářů je pravděpodobné, že test jednotky odpovídá Signature funkce klasického testu, přijímá argument typu `*testing.T`.

V těle testu jednotek máme celkem pět případů, které jsou definovány v proměnné `testCases` (`key` jako vstup a `value` jako očekávaný výstup). Pro každý testovací případ jednotky nejprve spustíte `terraform init` a cílíte na složku test přípravek (`./test/fixtures/storage-account-name/`). 

Dále `terraform plan` příkaz, který používá konkrétní vstup testovacího případu (podívejte se na definici `website_name` v `tfOptions`) uloží výsledek do `./test/fixtures/storage-account-name/terraform.tfplan` (není uvedeno v celkové struktuře složek).

Tento soubor výsledku se analyzuje do struktury čitelné kódem pomocí oficiálního analyzátoru plánu Terraformu.

Teď se podíváme na atributy, které vás zajímají (v tomto případě `name` `azurerm_storage_account`), a porovnejte výsledky s očekávaným výstupem:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Chcete-li spustit testy jednotek, proveďte následující kroky na příkazovém řádku:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Výsledek testu tradičního přechodu se vrátí přibližně minutu.

### <a name="integration-test"></a>Integrační test

Na rozdíl od testování částí musí testy integrace zřídit prostředky do reálného prostředí pro ucelenou perspektivu. Terratest je dobrým úkolem s tímto druhem úlohy. 

Mezi osvědčené postupy pro Terraformu moduly patří instalace složky `examples`. Složka `examples` obsahuje několik komplexních ukázek. Chcete-li se vyhnout práci s reálnými daty, netestujte tyto ukázky jako integrační testy? V této části se zaměříme na tři soubory označené hvězdičkou `(*)` v následující struktuře složek:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Pojďme začít s ukázkami. Ve složce `./examples/` se vytvoří nová ukázková složka s názvem `hello-world/`. Tady poskytujeme jednoduchou stránku HTML, která se má nahrát: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Vzorový `./examples/hello-world/main.tf` Terraformu je podobný jako ten, který je zobrazený v testu jednotek. Existuje jeden značný rozdíl: Ukázka také vytiskne adresu URL nahraného HTML jako webovou stránku s názvem `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

V `./test/hello_world_example_test.go`souboru integračního testu používáme znovu funkce Terratest a klasických testů.

Na rozdíl od jednotkových testů vytváří integrační testy v Azure skutečné prostředky. To je důvod, proč musíte být opatrní, abyste se vyhnuli konfliktům při pojmenovávání. (Věnujte zvláštní pozornost některým globálně jedinečným názvům, jako jsou názvy účtů úložiště.) Proto první krok testovací logiky je generování náhodného `websiteName` pomocí funkce `UniqueId()`, kterou poskytuje Terratest. Tato funkce generuje náhodný název, který obsahuje malá písmena, Velká písmena nebo číslice. `tfOptions` provede všechny příkazy Terraformu cílené na složku `./examples/hello-world/`. Také se ujistěte, že je `website_name` nastaveno na náhodný `websiteName`.

Následně se postupně spustí příkazy `terraform init`, `terraform apply` a `terraform output`. Používáme další pomocnou funkci, `HttpGetWithCustomValidation()`, která je poskytována Terratest. Pomocná funkce se používá k zajištění, že se do výstupního `homepage` URL, který vrací `terraform output`, nahraje kód HTML. Porovnáváme stavový kód HTTP s `200` a hledáme některá klíčová slova v obsahu HTML. Nakonec se s využitím funkce `terraform destroy` jazyka Go zajistí spuštění příkazu `defer`.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Chcete-li spustit testy integrace, proveďte následující kroky na příkazovém řádku:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Výsledek testu tradičního přechodu se vrátí přibližně po dobu dvou minut. Můžete také spustit testy jednotek i integrační testy spuštěním těchto příkazů:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Testy integrace mají mnohem déle než testy jednotek (dvě minuty pro jeden případ integrace v porovnání s jednou minutou pro pět jednotek). Ale jedná se o vaše rozhodnutí, jestli chcete ve scénáři použít testy jednotek nebo testy integrace. Obvykle doporučujeme používat testy jednotek pro komplexní logiku pomocí funkcí Terraformu HCL. Pro koncovou perspektivu uživatele obvykle používáme testy Integration.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Zjednodušení spouštění případů Terraformu s využitím nástroje mage 
Spuštění testovacích případů v Azure Cloud Shell vyžaduje spuštění různých příkazů v různých adresářích. Abychom tento proces mohli zefektivnit, zavádíme v našem projektu systém sestavení. V této části používáme pro úlohu systém sestavení na cestách, Mage.

Jediná věc požadovaná v Mage je `magefile.go` v kořenovém adresáři vašeho projektu (označená `(+)` v následujícím příkladu):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Tady je příklad `./magefile.go`. V tomto skriptu sestavení, který je napsán v cestách, implementujeme pět kroků sestavení:
- `Clean`: krok odebere všechny generované a dočasné soubory, které jsou generovány během spuštění testu.
- `Format`: krok se spouští `terraform fmt` a `go fmt` k naformátování základu kódu.
- `Unit`: krok spustí všechny testy jednotek (pomocí `TestUT_*`konvence názvu funkce) ve složce `./test/`.
- `Integration`: krok je podobný jako `Unit`, ale místo testování částí provádí integrační testy (`TestIT_*`).
- `Full`: krok se spouští `Clean`, `Format`, `Unit`a `Integration` v uvedeném pořadí.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

K provedení úplné sady testů můžete použít následující příkazy. Kód je podobný spuštěným postupům, které jsme použili v předchozí části. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Poslední příkazový řádek můžete nahradit dalšími kroky Mage. Můžete například použít `mage unit` nebo `mage clean`. Je vhodné vkládat `dep` příkazy a `az login` v magefile. Kód zde nezobrazujeme. 

Pomocí Mage můžete také nasdílet kroky pomocí systému balíčku nástroje. V takovém případě je možné zjednodušit magefiles napříč všemi moduly tím, že odkazují jenom na běžnou implementaci a deklarujete závislosti (`mg.Deps()`).

**Volitelné: Nastavte proměnné prostředí instančního objektu tak, aby se spouštěly testy přijetí.**
 
Místo spouštění `az login` před testy můžete dokončit ověřování Azure tím, že nastavíte proměnné prostředí objektu služby. Terraformu publikuje [seznam názvů proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Vyžaduje se jenom první čtyři z těchto proměnných prostředí.) Terraformu také zveřejňuje podrobné pokyny, které vysvětlují, jak [získat hodnotu těchto proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Stránka GitHubu Terratest](https://github.com/gruntwork-io/terratest)