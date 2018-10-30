---
title: Testování modulů Terraformu v Azure pomocí Terratestu
description: Zjistěte, jak pomocí Terratestu testovat moduly Terraformu.
services: terraform
ms.service: terraform
keywords: terraform, devops, storage account, azure, terratest, unit test, integration test
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638703"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Testování modulů Terraformu v Azure pomocí Terratestu

Moduly Terraformu slouží k vytváření znovupoužitelných, sestavitelných a testovatelných komponent. Začleňují zapouzdření do světa infrastruktury jako kódu.

Stejně jako u ostatních softwarových komponent hraje důležitou roli v modulech Terraformu kontrola kvality. Bohužel je k dispozici málo dokumentace vysvětlující, jak v modulech Terraformu vytvářet testy jednotek a integrační testy. Tento kurz představuje testovací infrastrukturu a osvědčené postupy, které jsme využili při vytváření našich [modulů Terraformu pro Azure](https://registry.terraform.io/browse?provider=azurerm).

Po zvážení všech nejoblíbenějších testovacích infrastruktur jsme se rozhodli využít [Terratest](https://github.com/gruntwork-io/terratest). Terratest se implementuje jako knihovna Go. Poskytuje kolekci pomocných funkcí a vzorů pro běžné úlohy testování infrastruktury, jako je provádění požadavků HTTP a připojení ke konkrétnímu virtuálnímu počítači přes SSH. Mezi hlavní výhody Terratestu patří:

- **Poskytuje pohodlné pomocné rutiny pro kontrolu infrastruktury.** Tato funkce je užitečná v případě, že chcete ověřit skutečnou infrastrukturu ve skutečném prostředí.
- **Struktura složek je jasně uspořádaná.** Testovací případy budou jasně uspořádané a budou odpovídat [standardní struktuře složek modulu Terraformu](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Všechny testovací případy jsou napsané v Go**. Vzhledem k tomu, že většina vývojářů pro Terraform již vyvíjí v jazyce Go, použitím Terraformu se eliminuje potřeba učit se další programovací jazyk. Kromě toho jsou jediné závislosti potřebné ke spouštění testovacích případů v Terratestu jazyk Go a Terraform.
- **Tato infrastruktura je vysoce rozšiřitelná.** Terratest je možné snadno rozšířit o další funkce, například funkce specifické pro Azure.

## <a name="prerequisites"></a>Požadavky

Tato praktická příručka je nezávislá na platformě a můžete ji využít v systému Windows, Linux nebo MacOS. Než budete pokračovat, nainstalujte následující software:

- **Programovací jazyk Go:** Testovací případy Terraformu se píší v [Go](https://golang.org/dl/).
- **dep:** [dep](https://github.com/golang/dep#installation) je nástroj pro správu závislostí pro Go.
- **Azure CLI:** [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nástroj pro příkazový řádek pro správu prostředků Azure. (Terraform podporuje ověřování v Azure prostřednictvím instančního objektu nebo [přes Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage:** Pomocí [spustitelného souboru mage](https://github.com/magefile/mage/releases) si ukážeme, jak zjednodušit spouštění testů Terraformu. 

## <a name="create-a-static-webpage-module"></a>Vytvoření modulu statického webu

V tomto kurzu vytvoříte modul Terraformu, který zřídí statický web nahráním jednoho souboru HTML do objektu blob úložiště Azure. Tento modul umožní přístup k tomuto webu uživatelům po celém světě prostřednictvím adresy URL, kterou vrací.

> [!NOTE]
> Všechny soubory popsané v této části by se měly vytvořit ve vaší cestě [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Nejprve ve složce `src` ve vaší cestě GoPath vytvořte novou složku `staticwebpage`. Celková struktura složek tohoto kurzu je znázorněná níže. (Tato část se zaměřuje hlavně na soubory označené hvězdičkou `(*)`.)

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

Modul statického webu přijímá tři vstupy, které se deklarují v souboru `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Jak je uvedeno výše, výstupem tohoto modulu bude také adresa URL deklarovaná v souboru `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Tím se dostáváme k hlavní logice tohoto modulu. Modul zřídí celkem čtyři prostředky:
- Skupina prostředků, jejíž název se skládá ze vstupu `website_name` a připojeného řetězce `-staging-rg`.
- Účet úložiště, jehož název se skládá ze vstupu `website_name` a připojeného řetězce `data001`. Za účelem přizpůsobení se omezením názvu účtu úložiště však modul odebere všechny speciální znaky a celý název převede na malá písmena.
- Kontejner s pevným názvem `wwwroot` vytvořený ve výše uvedeném účtu úložiště.
- Jeden soubor HTML načtený ze vstupu `html_path` a nahraný do umístění `wwwroot/index.html`.

Logika modulu statického webu se implementuje v souboru `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Test jednotek

Terratest je tradičně nástroj navržený pro integrační testy, to znamená, že zřídí skutečné prostředky ve skutečném prostředí. Takové úlohy se někdy můžou stát mimořádně velké, zejména v případě, že potřebujete zřídit velké množství prostředků. Dobrým příkladem je logika převodu názvu účtu úložiště popsaná v předchozí části – ve skutečnosti nepotřebujeme zřídit žádné prostředky, jenom se chceme ujistit, že je logika převodu názvu správná.

Díky flexibilitě Terratestu se to dá snadno provést pomocí testů jednotek. Testy jednotek jsou místně spuštěné testovací případy (i když se stále vyžaduje přístup k internetu), které spouštějí příkazy `terraform init` a `terraform plan`, parsují výstup příkazu `terraform plan` a hledají v něm hodnoty atributů k porovnání.

Zbytek této části popisuje, jak pomocí Terratestu implementujeme test jednotek, abychom se ujistili, že je logika převodu názvu účtu úložiště správná. Budou nás zajímat pouze soubory označené hvězdičkou `(*)`.

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

Zaprvé, prázdný soubor HTML `./test/fixtures/storage-account-name/empty.html` je pouze zástupný.

Soubor `./test/fixtures/storage-account-name/main.tf` obsahuje kostru testovacího případu. Přijímá jeden vstup `website_name`, což je také vstup testů jednotek. Tady je vidět jeho logika:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

A konečně, hlavní komponentou je implementace testů jednotek: `./test/storage_account_name_unit_test.go`

Pokud vyvíjíte v Go, všimnete si, že odpovídá podpisu klasické testovací funkce Go, protože přijímá argument typu `*testing.T`.

V těle testu jednotek máme celkem pět případů definovaných v proměnné `testCases` (klíčem je vstup a hodnotou je očekávaný výstup). Pro každý testovací případ nejprve spustíme příkaz `terraform init`, jehož cílem je složka testovacího přípravku (`./test/fixtures/storage-account-name/`). 

Následně příkaz `terraform plan` s konkrétním testovacím případem jako vstupem (prohlédněte si definici `website_name` v části `tfOptions`) uloží výsledek do souboru `./test/fixtures/storage-account-name/terraform.tfplan` (který není uvedený v celkové struktuře složek).

Výsledný soubor se pak pomocí oficiálního analyzátoru plánů Terraformu parsuje do kódově čitelné struktury.

Teď vyhledáme atributy, které nás zajímají (v tomto případě `name` pro`azurerm_storage_account`), a porovnáme je s očekávaným výstupem.

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
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

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

Pokud chcete testy jednotek spustit, na příkazovém řádku budete muset provést následující kroky.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Přibližně po jedné minutě se zobrazí tradiční výsledek testu Go.

### <a name="integration-test"></a>Integrační test

Na rozdíl od testů jednotek se u integračních testů vždy vyžaduje zřizování prostředků ve skutečném prostředí. Terratest si s těmito úlohami dokáže dobře poradit. Vzhledem k tomu, že osvědčený postup pro moduly Terraformu doporučuje také použití složky `examples` obsahující několik kompletních ukázek, proč v rámci integračních testů neotestovat tyto ukázky? V této části se zaměříme na tři soubory označené hvězdičkou `(*)`.

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

Nejprve začneme s ukázkami. Ve složce `./examples/` se vytvoří nová ukázková složka `hello-world/`. Tady je jednoduchá stránka HTML, kterou nahrajete do souboru `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Ukázka Terraformu `./examples/hello-world/main.tf` se podobá ukázce testu jednotek pouze s jedním velkým rozdílem: vypíše také adresu URL nahraného souboru HTML s názvem `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

V souboru integračního testu `./test/hello_world_example_test.go` se opět zobrazí testovací funkce Terraformu a klasická testovací funkce Go.

Na rozdíl od testů jednotek vytvoří integrační testy skutečné prostředky v Azure, proto musíte být opatrní, abyste se vyhnuli konfliktům názvů. (Věnujte zvláštní pozornost některým globálně jedinečným názvům, jako je například název účtu úložiště). Prvním krokem logiky testování je proto vygenerování náhodné hodnoty `websiteName` pomocí funkce `UniqueId()`, kterou poskytuje Terratest. Tato funkce vygeneruje náhodný název obsahující malá písmena, velká písmena nebo číslice. `tfOptions` provádí všechny příkazy Terraformu, jejichž cílem je složka `./examples/hello-world/`, a také zajišťuje nastavení `website_name` na náhodnou hodnotu `websiteName`.

Následně se postupně spustí příkazy `terraform init`, `terraform apply` a `terraform output`. Pomocí další pomocné funkce `HttpGetWithCustomValidation()`, kterou poskytuje Terratest, se ujistíme, že se soubor HTML nahrál na výstupní adresu URL `homepage` vrácenou příkazem `terraform output`. Provedeme to porovnáním stavového kódu HTTP Get s hodnotou `200` a vyhledáním několika klíčových slov v obsahu HTML. Nakonec se s využitím funkce `defer` jazyka Go zajistí spuštění příkazu `terraform destroy`.

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

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Pokud chcete integrační testy spustit, na příkazovém řádku budete muset provést následující kroky.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Přibližně po dvou minutách se zobrazí tradiční výsledek testu Go. Testy jednotek i integrační testy můžete samozřejmě spustit i spuštěním následujících příkazů:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Jak je vidět, integrační testy trvají mnohem déle než testy jednotek (jeden případ integrace trvá dvě minuty, zatímco pět případů jednotek trvá jednu minutu). Stále je ale na vás, kdy použít testy jednotek a kdy využít integrační testy. Testy jednotek obvykle doporučujeme použít v případě komplexní logiky využívající funkce Terraform HCL, zatímco integrační test doporučujeme provést z pohledu uživatele.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Zjednodušení spouštění případů Terraformu s využitím nástroje mage 

Jak jste viděli, spouštění testovacích případů v prostředí není snadné, protože musíte přecházet mezi různými adresáři a spouštět různé příkazy. Z tohoto důvodu do našeho projektu začleníme systém sestavení. V této části k tomu použijeme systém sestavení jazyka Go mage.

Jediné, co nástroj mage vyžaduje, je soubor `magefile.go` v kořenovém adresáři projektu (na následujícím obrázku je označený symbolem plus `(+)`).

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

Tady je příklad souboru `./magefile.go`. V tomto skriptu sestavení napsaném v Go jsme implementovali pět kroků sestavení:
- `Clean`: Tento krok odeberete všechny vygenerované a dočasné soubory vytvořené během provádění testů.
- `Format`: Tento krok spuštěním příkazů `terraform fmt` a `go fmt` zformátuje váš základ kódu.
- `Unit`: Tento krok spustí všechny testy jednotek (řídící se konvencí názvů funkcí `TestUT_*`) ve složce `./test/`.
- `Integration`: Funguje podobně jako `Unit`, ale místo testů jednotek spustí integrační testy (`TestIT_*`).
- `Full`: Tento krok postupně spustí kroky `Clean`, `Format`, `Unit', and `Integration`.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
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

// A build step that removes temporary build/test files
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

Podobně jako spuštěním výše uvedených kroků můžete kompletní sadu testů spustit pomocí následujících příkazů:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

Poslední příkazový řádek můžete nahradit za jakékoli kroky nástroje mage, například `mage unit` nebo `mage clean`. Teď si možná myslíte, že je to pořád hodně příkazových řádků, ale příkazy `dep` i `az login` můžete vložit do souboru magefile. Tento kód zde však ukazovat nebudeme. Používání nástroje mage můžete posunout na další úroveň sdílením kroků pomocí systému balíčků Go. Díky tomu budete moct zjednodušit soubory magefile napříč všemi moduly tím, že budete pouze odkazovat na společnou implementaci a deklarovat závislosti (`mg.Deps()`).

> [!NOTE]
> **Možnost: Nastavení proměnných prostředí instančního objektu pro spouštění akceptačních testů**
> 
> Místo spouštění příkazu `az login` před jednotlivými testy můžete zajistit ověřování v Azure nastavením proměnných prostředí instančního objektu. Terraform publikuje [seznam názvů proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Povinné jsou pouze první čtyři z těchto proměnných prostředí.) Terraform publikuje také podrobné pokyny vysvětlující, jak [získat hodnoty těchto proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Další kroky

Další informace o Terratestu najdete na [jeho stránce na GitHubu](https://github.com/gruntwork-io/terratest). Několik užitečných informací o nástroji mage můžete najít na [jeho stránce na GitHubu](https://github.com/magefile/mage) nebo [jeho domovské stránce](https://magefile.org/).
