---
title: Moduly Terraformu v Azure můžete testovat pomocí Terratest
description: Zjistěte, jak pomocí Terratestu testovat moduly Terraformu.
services: terraform
ms.service: terraform
keywords: terraform, devops, storage account, azure, terratest, unit test, integration test
author: JunyiYi
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 8ef4e9917623f43e5c9900150deb22d62169c836
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555961"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Moduly Terraformu v Azure můžete testovat pomocí Terratest

Můžete použít moduly Terraformu pro Azure k vytvoření opakovaně použitelné, sestavitelný a možností intenzivního testování součástí. Moduly Terraformu začlenit zapouzdření, které jsou užitečné při implementaci infrastruktury jako kódu procesy.

Je důležité při vytváření modulů Terraformu implementovat kontroly kvality. Bohužel omezené dokumentace je k dispozici vysvětlují, jak si můžete vytvořit testy jednotky a integrace v modulů Terraformu. Tento kurz představuje testovací infrastruktury a osvědčené postupy, které jsme přijali, když jsme vytvořili naši [moduly Terraformu pro Azure](https://registry.terraform.io/browse?provider=azurerm).

Jsme se podívali na vše nejoblíbenější testování infrastruktury a vyberte možnost [Terratest](https://github.com/gruntwork-io/terratest) má použít pro testování naše moduly Terraformu. Terratest se implementuje jako knihovna Go. Terratest poskytuje kolekce pomocných funkcí a vzory pro běžnou infrastrukturu testování úkoly, jako je zasílání požadavků HTTP a přístup k určitému virtuálnímu počítači pomocí SSH. Následující seznam popisuje některé hlavní výhody používání Terratest:

- **Poskytuje pohodlné pomocné rutiny ke kontrole infrastruktury**. Tato funkce je užitečná v případě, že chcete ověřit skutečnou infrastrukturu ve skutečném prostředí.
- **Struktura složky je uspořádaný jasně**. Testovací případy jsou uspořádány jasně a postupujte podle [standardní strukturu složek modulů Terraformu](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Všechny testovací případy jsou napsané v cestách**. Většina vývojářů, kteří používají Terraformu jsou vývojáře v Go. Pokud jste vývojář, Go, není nutné další použití Terratest jiném programovacím jazyce. Navíc jsou jenom závislosti, které jsou požadovány pro spuštění testovacích případů v Terratest Go a Terraformu.
- **Infrastruktuře je velmi dobře rozšiřitelná**. Můžete rozšířit další funkce nad rámec Terratest, včetně funkce specifické pro Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek praktické je nezávislá na platformě. Příklady kódu, které používáme v tomto článku můžete spustit na Windows, Linux nebo MacOS. 

Než začnete, nainstalujte následující software:

- **Přejděte programovací jazyk**: Terraform testovací případy jsou napsané v [Přejít](https://golang.org/dl/).
- **dep:** [dep](https://github.com/golang/dep#installation) je nástroj pro správu závislostí pro Go.
- **Azure CLI**: [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nástroj příkazového řádku můžete použít ke správě prostředků Azure. (Terraformu podporuje ověřování pomocí instančního objektu v Azure nebo [prostřednictvím rozhraní příkazového řádku Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **Obrázek**: Používáme [spustitelné bitové kopii](https://github.com/magefile/mage/releases) až vám ukážeme, jak můžete zjednodušit spuštěné Terratest případy. 

## <a name="create-a-static-webpage-module"></a>Vytvoření modulu statického webu

V tomto kurzu vytvoříte modul Terraform, který zřídí statickou webovou stránku tak, že nahrajete soubor HTML do objektu blob Azure Storage. Tento modul poskytuje uživatelům z celého světa přístup na webovou stránku prostřednictvím adresy URL, která vrací modulu.

> [!NOTE]
> Vytvořte všechny soubory, které jsou popsané v této části v rámci vaší [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) umístění.

Nejprve vytvořte novou složku s názvem `staticwebpage` pod GoPath `src` složky. Celková struktura složek tohoto kurzu je uvedeno v následujícím příkladu. Soubory označené hvězdičkou `(*)` jsou primární fokus v této části.

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

Statická webová stránka modul přijímá třemi vstupy. Vstupy jsou deklarovány v `./variables.tf`:

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

Jak už jsme zmínili výše v tomto článku, tento modul vypíše adresu URL, která je deklarována v `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Hlavní logika modulu zřizuje čtyři prostředky:
- **Skupina prostředků**: Název skupiny prostředků je `website_name` vstup doplněno `-staging-rg`.
- **Účet úložiště**: Název účtu úložiště je `website_name` vstup doplněno `data001`. Dodržovat omezení název účtu úložiště, modul odebere všechny speciální znaky a používá malá písmena v názvu účtu celého úložiště.
- **Oprava název kontejneru**: Kontejner má název `wwwroot` a je vytvořen v účtu úložiště.
- **jeden soubor HTML**: Je pro čtení ze souboru HTML `html_path` vstup a nahráli do `wwwroot/index.html`.

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

Terratest je určená pro testy integrace. Pro tento účel zřídí Terratest skutečné prostředky ve skutečných prostředí. Integrace testovací úlohy v některých případech může být mimořádně velkou, zejména v případě, že máte velké množství prostředků ke zřízení. Logika, která převede názvy účtů úložiště, které označujeme v předchozí části je typickým příkladem. 

Ale nemusíme skutečně zřiďte všechny prostředky. Chceme ověřte správnost pojmenování způsob převodu. Díky flexibilitě Terratest můžeme pomocí testů jednotek. Testování jednotek je místní spouštění testovacích případů (i když se vyžaduje přístup k Internetu). Spustit testovací případy jednotek `terraform init` a `terraform plan` příkazy parsovat výstup `terraform plan` a vyhledejte hodnoty atributů pro porovnání.

Zbývající část popisuje, jak používáme Terratest provádět testování částí, abyste měli jistotu, že je správný logikou používanou k převodu názvy účtů úložiště. Nás zajímá jenom v souborech s hvězdičkou `(*)`.

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

Nejprve používáme prázdný HTML soubor s názvem `./test/fixtures/storage-account-name/empty.html` jako zástupný symbol.

Soubor `./test/fixtures/storage-account-name/main.tf` je rámec testovacího případu. Přijímá jeden vstup, `website_name`, což je také vstup jednotkové testy. Logika je znázorněna zde:

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

Hlavní součásti je provádění testů jednotek v `./test/storage_account_name_unit_test.go`.

Přejděte vývojáři pravděpodobně si všimnout, že Jednotkový test shoduje se signaturou classic testovací funkce přejít tak, že přijímá argument typu `*testing.T`.

V těle test jednotky, budeme mít celkem pět případů, které jsou definovány v proměnné `testCases` (`key` jako vstup a `value` jako očekávaný výstup). Pro každý testovací případ jednotka nejdřív provozujeme `terraform init` a cílit na složce testovací přípravek (`./test/fixtures/storage-account-name/`). 

Další, `terraform plan` příkaz, který používá vstupu pro konkrétní testovací případ (podívejte se na `website_name` definice v `tfOptions`) ukládá výsledek, který má `./test/fixtures/storage-account-name/terraform.tfplan` (které nejsou uvedené v celková struktura složek).

Tento soubor s výsledky je analyzován kód čitelné struktury pomocí oficiální plán analyzátoru Terraformu.

Teď se podíváme pro atributy, které nás zajímá (v tomto případě `name` z `azurerm_storage_account`) a porovnávat výsledky s očekávaný výstup:

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

Tradiční výsledek testu Go vrátí přibližně minutu.

### <a name="integration-test"></a>Integrační test

Na rozdíl od testování částí musí testy integrace zřizovat prostředky do reálného prostředí pro perspektivu začátku do konce. Terratest odvádí dobrou práci, tento typ úlohy. 

Osvědčené postupy pro moduly Terraformu zahrnovat instalaci `examples` složky. `examples` Složka obsahuje některé ukázky začátku do konce. Vyhněte se práce s reálná data, případně proč bezpečná není testovacích tyto ukázky jako integrační testy? V této části se zaměříme na tři soubory, které jsou označeny hvězdičkou `(*)` ve struktuře následující složky:

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

Začněme s ukázkami. Nové ukázkové složky s názvem `hello-world/` se vytvoří v `./examples/` složky. Tady, zajišťuje jednoduché stránky HTML k odeslání: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample webpage to demonstrate Terratest.</p>
</body>
</html>
```

Ukázka Terraformu `./examples/hello-world/main.tf` je podobný tomu vidíte v testu jednotek. Je jedním z největších rozdílů: Ukázka také vytiskne URL nahrané HTML jako webovou stránku s názvem `homepage`.

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

Používáme Terratest a classic přejděte testování funkcí znovu v integraci soubor testu `./test/hello_world_example_test.go`.

Na rozdíl od testování částí vytvořte testy integrace skutečných prostředků v Azure. To je důvod, proč potřebujete mít na paměti, aby předešel konfliktům. (Věnujte zvláštní pozornost některá globálně jedinečných názvů jako názvy účtů úložiště.) Prvním krokem testovací logiku tedy generovat náhodnými `websiteName` pomocí `UniqueId()` funkce poskytované Terratest. Tato funkce generuje náhodný název, který obsahuje malá písmena, velká písmena nebo číslice. `tfOptions` Díky všechny příkazy Terraformu, které se zaměřují `./examples/hello-world/` složky. Je také zajišťuje, že `website_name` je nastaveno náhodného `websiteName`.

Následně se postupně spustí příkazy `terraform init`, `terraform apply` a `terraform output`. Můžeme použít další pomocná funkce, `HttpGetWithCustomValidation()`, která poskytuje Terratest. Abyste měli jistotu, že HTML se nahraje do výstupu používáme pomocnou funkci `homepage` adresu URL, která je vrácena `terraform output`. Porovnáme stavový kód HTTP GET s `200` a hledat klíčová slova v kódu HTML obsahu. Nakonec se s využitím funkce `defer` jazyka Go zajistí spuštění příkazu `terraform destroy`.

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

Ke spuštění testů integrace, proveďte následující kroky na příkazovém řádku:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Tradiční výsledek testu Go vrátí během dvou minut. Testování částí a integrační testy můžete spustit také spuštěním těchto příkazů:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Integrační testy trvat déle než testování částí (dvě minuty pro jeden případ integrace porovnání na jednu minutu pro pět případy jednotek). Ale je rozhodnout, zda se má použití jednotkových testů nebo integrační testy ve scénáři. Obvykle nám dávají přednost používání testování částí pro komplexní logiku pomocí Terraformu HCL funkcí. Obvykle používáme integrační testy pro perspektivu začátku do konce uživatele.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Zjednodušení spouštění případů Terraformu s využitím nástroje mage 

Spouštění testovacích případů ve službě Azure Cloud Shell není snadný úkol. Budete muset přejít k různým adresářům a spouštět různé příkazy. Abyste se vyhnuli použití Cloud Shell, v našem projektu zavedeme systém sestavení. V této části jsme systém sestavení Go, Ndex bitové kopie, použít pro úlohu.

Jediné, co vyžaduje Ndex bitové kopie je `magefile.go` v kořenovém adresáři vašeho projektu (označené `(+)` v následujícím příkladu):

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

Tady je příklad `./magefile.go`. V tento skript sestavení, napsané v cestách můžeme implementovat pět kroků sestavení:
- `Clean`: V kroku odebere všechny generované a dočasné soubory, které jsou generovány během provádění testů.
- `Format`: Je krok spuštěn `terraform fmt` a `go fmt` k formátování vašeho základu kódu.
- `Unit`: V kroku spustí všechny testy jednotek (s použitím konvence název funkce `TestUT_*`) v části `./test/` složky.
- `Integration`: Krok je podobný `Unit`, ale místo testování částí se provede integrační testy (`TestIT_*`).
- `Full`: Je krok spuštěn `Clean`, `Format`, `Unit`, a `Integration` postupně.

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

Můžete použít následující příkazy k provedení úplné testovací sady. Kód je podobný spuštěné kroky, které jsme použili v předchozí části. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Poslední příkazového řádku můžete nahradit mage další kroky. Například můžete použít `mage unit` nebo `mage clean`. Je vhodné vložit `dep` příkazy a `az login` v magefile. Nezobrazovat jsme sem kód. 

S Ndex bitové kopie může také sdílet kroky pomocí systému balíček Go. V takovém případě můžete zjednodušit magefiles přes všechny moduly odkazující na běžnou implementaci a deklarační popisovač závislosti (`mg.Deps()`).

**Volitelné: Nastavení služby instančního objektu prostředí proměnných pro spouštění testů přijetí**
 
Místo spouštění `az login` před testy, můžete dokončit ověřování Azure nastavením proměnné prostředí instančního objektu služby. Publikuje Terraform [seznam názvů proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Povinné jsou pouze první čtyři z těchto proměnných prostředí.) Terraform, publikuje také podrobné pokyny, které vysvětlují, jak [získat hodnotu z těchto proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Další postup

* Další informace o Terratest, najdete v článku [stránku Githubu Terratest](https://github.com/gruntwork-io/terratest).
* Informace o bitové kopii, najdete v článku [stránku Githubu mage](https://github.com/magefile/mage) a [mage webu](https://magefile.org/).
