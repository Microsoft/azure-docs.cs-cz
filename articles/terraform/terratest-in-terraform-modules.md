---
title: Výuka – testování modulů Terraform v Azure pomocí Terratestu
description: Zjistěte, jak pomocí Terratestu testovat moduly Terraformu.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370793"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Kurz: Testování modulů Terraform v Azure pomocí Terratestu

> [!NOTE]
> Ukázkový kód v tomto článku nefunguje s verzí 0.12 (a větší).

Moduly Azure Terraform můžete použít k vytvoření opakovaně použitelných, kompozitelných a testovatelných součástí. Terraform moduly začlenit zapouzdření, které je užitečné při implementaci infrastruktury jako procesy kódu.

Při vytváření modulů Terraform je důležité implementovat zajištění kvality. Bohužel je k dispozici omezená dokumentace, která vysvětluje, jak vytvářet testy částí a integrační testy v modulech Terraform. Tento kurz představuje testovací infrastrukturu a osvědčené postupy, které jsme přijali při [výstavbě našich modulů Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Podívali jsme se na všechny nejoblíbenější testovací infrastruktury a vybrali [terratest](https://github.com/gruntwork-io/terratest) pro testování našich modulů Terraform. Terratest se implementuje jako knihovna Go. Terratest poskytuje kolekci pomocných funkcí a vzorů pro běžné úlohy testování infrastruktury, jako je vytváření požadavků HTTP a použití SSH pro přístup k určitému virtuálnímu počítači. Následující seznam popisuje některé z hlavních výhod použití Terratest:

- **Poskytuje pohodlné pomocníky pro kontrolu infrastruktury**. Tato funkce je užitečná v případě, že chcete ověřit skutečnou infrastrukturu ve skutečném prostředí.
- **Struktura složek je jasně uspořádaná**. Vaše testovací případy jsou uspořádány jasně a postupujte podle [standardní struktury složek modulu Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Všechny testovací případy jsou zapsány v Go**. Většina vývojářů, kteří používají Terraform, jsou vývojáři Go. Pokud jste vývojář Go, nemusíte se učit jiný programovací jazyk, abyste mohli používat Terratest. Také pouze závislosti, které jsou požadovány pro spuštění testovacích případů v Terratest jsou Go a Terraform.
- **Infrastruktura je vysoce rozšiřitelná**. Můžete rozšířit další funkce nad Terratest, včetně funkcí specifických pro Azure.

## <a name="prerequisites"></a>Požadavky

Tento praktický článek je nezávislý na platformě. Můžete spustit příklady kódu, které používáme v tomto článku na Windows, Linux nebo MacOS. 

Než začnete, nainstalujte následující software:

- **Go programovací jazyk**: Terraform testovací případy jsou napsány v [Go](https://golang.org/dl/).
- **dep:**[dep](https://github.com/golang/dep#installation) je nástroj pro správu závislostí pro Go.
- **Azure CLI**: [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) je nástroj příkazového řádku, který můžete použít ke správě prostředků Azure. (Terraform podporuje ověřování pro Azure prostřednictvím instančního objektu nebo [prostřednictvím azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **máj**: Používáme [már spustitelný](https://github.com/magefile/mage/releases) ukázat vám, jak zjednodušit provoz Terratest případy. 

## <a name="create-a-static-webpage-module"></a>Vytvoření modulu statického webu

V tomto kurzu vytvoříte modul Terraform, který zřídí statickou webovou stránku nahráním jednoho souboru HTML do objektu blob úložiště Azure. Tento modul poskytuje uživatelům z celého světa přístup k webové stránce prostřednictvím adresy URL, které modul vrátí.

> [!NOTE]
> Vytvořte všechny soubory, které jsou popsány v této části v umístění [GOPATH.](https://github.com/golang/go/wiki/SettingGOPATH)

Nejprve vytvořte novou `staticwebpage` složku s `src` názvem pod složkou GoPath. Celková struktura složek tohoto kurzu je uveden v následujícím příkladu. Soubory označené hvězdičkou `(*)` jsou primárním fokusem v této části.

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

Modul statické webové stránky přijímá tři vstupy. Vstupy jsou deklarovány v `./variables.tf`:

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

Jak jsme již zmínili dříve v článku, tento modul také `./outputs.tf`výstupy URL, která je deklarována v :

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

Hlavní logika modulu zřizovat čtyři zdroje:
- **skupina prostředků**: Název skupiny `website_name` prostředků je `-staging-rg`vstup připojený aplikací .
- **účet úložiště**: Název účtu úložiště `website_name` je vstup `data001`připojený . Chcete-li dodržovat omezení názvu účtu úložiště, modul odebere všechny speciální znaky a používá malá písmena v názvu účtu celého úložiště.
- **kontejner s pevným**názvem `wwwroot` : Kontejner je pojmenován a je vytvořen v účtu úložiště.
- **jeden soubor HTML**: Soubor HTML `html_path` je přečten `wwwroot/index.html`ze vstupu a odeslán do aplikace .

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

Terratest je určen pro integrační testy. Za tímto účelem Terratest zásobuje skutečné zdroje v reálném prostředí. V některých případech integrace testovací úlohy může stát mimořádně velké, zejména v případě, že máte velký počet prostředků k zajištění. Logika, která převádí názvy účtů úložiště, na které odkazujeme v předchozí části, je dobrým příkladem. 

Ale my opravdu nemusíme zajištit žádné zdroje. Chceme pouze zajistit, aby logika převodu názvů byla správná. Díky flexibilitě Terratestu můžeme použít jednotkové testy. Testy částí jsou místní spuštěné testovací případy (i když je vyžadován přístup k internetu). Případy testování `terraform init` částí `terraform plan` spustit a příkazy `terraform plan` analyzovat výstup a vyhledejte hodnoty atributů porovnat.

Zbývající část této části popisuje, jak používáme Terratest k implementaci testování částí a ujistěte se, že logika použitá k převodu názvů účtů úložiště je správná. Máme zájem pouze o soubory označené hvězdičkou `(*)`.

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

Nejprve použijeme prázdný soubor `./test/fixtures/storage-account-name/empty.html` HTML pojmenovaný jako zástupný symbol.

Soubor `./test/fixtures/storage-account-name/main.tf` je rámec testovacího případu. Přijímá jeden vstup `website_name`, který je také vstupem jednotkových testů. Logika je uvedena zde:

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

Hlavní složkou je implementace jednotkových `./test/storage_account_name_unit_test.go`testů v .

Přejít vývojáři pravděpodobně všimnete, že testování částí odpovídá podpisu klasické `*testing.T`funkce Go test přijetím argument typu .

V těle testování částí máme celkem pět případů, které `testCases` jsou`key` definovány `value` v proměnné (jako vstup a podle očekávaného výstupu). Pro každý testovací případ jednotky `terraform init` nejprve spustíme`./test/fixtures/storage-account-name/`a zacílíme na složku testovacího přípravku ( ). 

`terraform plan` Dále příkaz, který používá konkrétní vstup testovacího `website_name` případu `tfOptions`(podívejte se na `./test/fixtures/storage-account-name/terraform.tfplan` definici v ) uloží výsledek do (není uveden v celkové struktuře složek).

Tento výsledek souboru je analyzována na strukturu čitelnou pro kód pomocí oficiálního analyzátoru plánu Terraform.

Nyní hledáme atributy, které nás zajímají (v tomto `name` případě `azurerm_storage_account`) a porovnáme výsledky s očekávaným výstupem:

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

Chcete-li spustit testy částí, proveďte na příkazovém řádku následující kroky:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

Tradiční výsledek testu Go se vrátí asi za minutu.

### <a name="integration-test"></a>Integrační test

Na rozdíl od testování částí musí testy integrace zřídit prostředky do reálného prostředí pro komplexní perspektivu. Terratest dělá dobrou práci s tímto druhem úkolu. 

Mezi osvědčené postupy pro moduly `examples` Terraform patří instalace složky. Složka `examples` obsahuje některé ukázky od konce. Chcete-li se vyhnout práci s reálnými daty, proč tyto vzorky neotestovat jako integrační testy? V této části se zaměříme na tři soubory, `(*)` které jsou označeny hvězdičkou v následující struktuře složek:

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

Začněme se vzorky. Ve `./examples/` složce `hello-world/` je vytvořena nová ukázková složka s názvem. Zde poskytujeme jednoduchou stránku HTML, `./examples/hello-world/index.html`která má být nahrána: .

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

Vzorek `./examples/hello-world/main.tf` Terraform je podobný tomu, který je uveden v jednotkové zkoušce. Je tu jeden významný rozdíl: ukázka také vytiskne adresu URL `homepage`nahraného HTML jako webovou stránku s názvem .

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

V testovacím souboru `./test/hello_world_example_test.go`integrace opět používáme funkce Terratest a klasické go test .

Na rozdíl od testů částí integrační testy vytvořit skutečné prostředky v Azure. To je důvod, proč musíte být opatrní, aby se zabránilo pojmenování konflikty. (Věnujte zvláštní pozornost některým globálně jedinečným názvům, jako jsou názvy účtů úložiště.) Proto prvním krokem logiky testování je generovat `websiteName` randomizované `UniqueId()` pomocí funkce poskytované Terratest. Tato funkce generuje náhodný název, který má malá písmena, velká písmena nebo čísla. `tfOptions`provede všechny terraformské příkazy, které cílí na `./examples/hello-world/` složku. Zajišťuje také, `website_name` že je nastavena `websiteName`na randomizované .

Následně se postupně spustí příkazy `terraform init`, `terraform apply` a `terraform output`. Používáme jinou pomocnou funkci , `HttpGetWithCustomValidation()`kterou poskytuje Terratest. Pomocí pomocné funkce se ujistěte, že html `homepage` je odeslánna do `terraform output`výstupní adresy URL, která je vrácena . Porovnáme stavový kód `200` HTTP GET a vyhledáme některá klíčová slova v obsahu HTML. Nakonec se s využitím funkce `defer` jazyka Go zajistí spuštění příkazu `terraform destroy`.

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

Chcete-li spustit integrační testy, proveďte na příkazovém řádku následující kroky:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

Tradiční výsledek testu Go se vrátí asi za dvě minuty. Můžete také spustit testy částí a integrační testy provedením těchto příkazů:

```shell
go fmt
go test
```

Testy integrace trvat mnohem déle než testování částí (dvě minuty pro jeden případ integrace ve srovnání s jednu minutu pro pět případů jednotky). Ale je to vaše rozhodnutí, zda použít testy částí nebo integrační testy ve scénáři. Obvykle dáváme přednost použití testů částí pro komplexní logiku pomocí funkcí Terraform HCL. Obvykle používáme integrační testy pro komplexní perspektivu uživatele.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Zjednodušení spouštění případů Terraformu s využitím nástroje mage 
Spuštění testovacích případů v prostředí Azure Cloud Shell vyžaduje provádění různých příkazů v různých adresářích. Abychom tento proces zefektivnili, zavádíme do našeho projektu systém sestavení. V této části používáme go sestavení systému, mágo, pro práci.

Jediná věc, kterou mág vyžaduje, je `magefile.go` v kořenovém adresáři projektu (označeno `(+)` v následujícím příkladu):

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

Zde je příklad `./magefile.go`. V tomto scénáři sestavení, napsanév Go, implementujeme pět kroků sestavení:
- `Clean`: Krok odebere všechny generované a dočasné soubory, které jsou generovány během spuštění testu.
- `Format`: Krok `terraform fmt` se `go fmt` spustí a naformátovat základ kódu.
- `Unit`: Krok spustí všechny testy částí (pomocí `TestUT_*`konvence `./test/` názvu funkce) pod složkou.
- `Integration`: Krok je `Unit`podobný , ale místo testování částí provádí`TestIT_*`integrační testy ( ).
- `Full`: Krok `Clean`běží `Format` `Unit`, `Integration` , a v pořadí.

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

K provedení úplné testovací sady můžete použít následující příkazy. Kód je podobný spuštěným krokům, které jsme použili v předchozí části. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Poslední příkazový řádek můžete nahradit dalšími kroky máku. Můžete například použít `mage unit` `mage clean`nebo . Je vhodné vložit `dep` příkazy a `az login` do mágínu. Tady kód neukazujeme. 

S mástem můžete také sdílet kroky pomocí systému balíčků Go. V takovém případě můžete zjednodušit soubory mágů ve všech modulech odkazem pouze`mg.Deps()`na společnou implementaci a deklarováním závislostí ( ).

**Volitelné: Nastavení proměnných prostředí hlavního uživatele služby pro spuštění testů přijetí**
 
Místo provádění `az login` před testy můžete dokončit ověřování Azure nastavením proměnných prostředí hlavního nastavení služby. Terraform publikuje [seznam názvů proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Jsou vyžadovány pouze první čtyři proměnné prostředí.) Terraform také zveřejňuje podrobné pokyny, které vysvětlují, jak [získat hodnotu těchto proměnných prostředí](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Terratest GitHub stránku](https://github.com/gruntwork-io/terratest).