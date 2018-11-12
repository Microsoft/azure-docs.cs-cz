---
title: Nejčastější dotazy k Azure DevTest Labs | Dokumentace Microsoftu
description: Najděte odpovědi na běžné dotazy týkající se Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: b5ad6321a41c84928cbc6f8c51c4f5fe3567410f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262025"
---
# <a name="azure-devtest-labs-faq"></a>Nejčastější dotazy k Azure DevTest Labs
Získejte odpovědi na některé nejběžnější otázky o Azure DevTest Labs.

**Obecné**
## <a name="what-if-my-question-isnt-answered-here"></a>Co když můj dotaz zde nenalezl?
Pokud zde není uveden váš dotaz, dejte nám vědět, abychom mohli pomoct najít odpověď.

* Odešlete dotaz na konci tyto Nejčastější dotazy. Spolupracujte s týmem Azure Cache a ostatních členů komunity o tomto článku.
* K dosažení širší cílovou skupinu, odeslat dotaz na [fóra Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Spolupracujte s týmem Azure DevTest Labs a ostatní členové komunity.
* Pro žádosti o funkce odeslání žádosti a nápady, jak ho [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Proč je vhodné použít Azure DevTest Labs?
Azure DevTest Labs můžete uložit týmu čas i peníze. Vývojáři mohou vytvářet vlastní prostředí pomocí několika různých základů. Také použitím artefakty kvůli rychlému nasazení a konfigurace aplikací. Pomocí vlastních imagí a vzorců, můžete uložit jako šablony virtuálních počítačů (VM) a snadno reprodukovat napříč týmem. DevTest Labs také nabízí několik Konfigurovatelné zásady tohoto testovacího prostředí, které správcům umožňuje omezit Mrhání prostředky a spravovat prostředí týmu. Tyto zásady patří automatické vypnutí, prahové hodnoty nákladů, maximální virtuálních počítačů na uživatele a maximální velikost virtuálního počítače. Podrobnější vysvětlení DevTest Labs, najdete v článku [přehled](devtest-lab-overview.md) nebo [úvodní video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Co znamená "bez obav samoobslužné služby"?
Bez obav samoobslužné funkce znamená, že vývojářům a testerům vytvářet vlastní prostředí podle potřeby. Správci mají zabezpečení vědomím, že DevTest Labs pomáhá minimalizovat plýtvání a řízení nákladů. Správci mohou určit, jaké velikosti virtuálních počítačů jsou povolené a maximální počet virtuálních počítačů, a virtuální počítače jsou při spuštění a vypnutí. DevTest Labs také umožňuje snadno monitorovat náklady a nastavení výstrah můžete zůstat vědět, jak se používají prostředky testovacího prostředí.

## <a name="how-can-i-use-devtest-labs"></a>Použití DevTest Labs
DevTest Labs je užitečné, kdykoli mají vývoj nebo testovací prostředí a chcete rychle reprodukovat nebo spravovat pomocí zásad ušetření nákladů.

Tady je několik scénářů, které naši zákazníci využívat DevTest Labs pro:

* Správa vývoje a testovacích prostředí na jednom místě. Používání zásad ke snížení nákladů a vytváření vlastních imagí sdílet sestavení napříč týmem.
* Vývoj aplikace s využitím vlastní Image pro uložení stavu disku v průběhu fází vývoje.
* Sledování nákladů ve vztahu k pokroku.
* Vytvoření velkokapacitního testovací prostředí pro testování assurance kvality.
* Použít artefakty a vzorce snadno nakonfigurovat a reprodukovat aplikace v různých prostředích.
* Distribuce virtuální počítače pro hackathony (vývoj nebo testování týmovém) a potom snadno zrušit jejich zřízení až událost skončí.

## <a name="how-am-i-billed-for-devtest-labs"></a>Jak se fakturují DevTest Labs?
DevTest Labs je bezplatná služba. Vytváření testovacích prostředí a konfiguraci zásad, šablon a artefaktů ve službě DevTest Labs je bezplatná. Platíte jenom za prostředky Azure používané v laboratořích, jako jsou virtuální počítače, účty úložiště a virtuální sítě. Další informace o nákladech prostředky testovacího prostředí naleznete v tématu [ceny Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Zabezpečení**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Co jsou úrovně zabezpečení ve službě DevTest Labs?
Zabezpečení přístupu se určuje podle [řízení přístupu na základě Role (RBAC)](../role-based-access-control/built-in-roles.md). Informace o tom, jak funguje přístup, pomáhá další rozdíly mezi oprávnění, role a obor, jak jsou definovány pomocí RBAC.

* **Oprávnění**: oprávnění je definované přístup k určité akce. Oprávnění může být například přístup pro čtení pro všechny virtuální počítače.
* **Role**: role je sadu oprávnění, které mohou být seskupeny a přiřazená uživateli. Například uživatel s rolí vlastník předplatného má přístup ke všem prostředkům v rámci předplatného.
* **Obor**: obor je úroveň v hierarchii k prostředku Azure. Obor může být například skupinu prostředků, jednoho testovacího prostředí nebo celé předplatné.

V rámci oboru DevTest Labs existují dva typy rolí, které definují uživatelská oprávnění:

* **Vlastník testovacího prostředí**: vlastník testovacího prostředí má přístup ke všem prostředkům v testovacím prostředí. Vlastník testovacího prostředí můžete upravovat zásady, čtení a zápis pro všechny virtuální počítače, změnit virtuální síť a podobně.
* **Uživatele testovacího prostředí**: uživatele testovacího prostředí můžete zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě. Ale uživatele testovacího prostředí nelze změnit zásady nebo všechny virtuální počítače, které byly vytvořené jinými uživateli. 

Můžete také vytvořit vlastní role ve službě DevTest Labs. Zjistěte, jak vytvořit vlastní role ve službě DevTest Labs, najdete v článku [udělení uživatelských oprávnění na určitém laboratorním zásady](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Protože obory jsou hierarchické, když má uživatel oprávnění v určitém rozsahu, uživateli je udělen těchto oprávnění v každé nižší úrovni oboru v oboru. Například pokud uživatel je přiřazena role vlastníka předplatného, má uživatel přístup ke všem prostředkům v rámci předplatného. Tyto prostředky zahrnují všechny virtuální počítače, všechny virtuální sítě a všechny testovací prostředí. Vlastník předplatného automaticky dědí role vlastník testovacího prostředí. Ale opak není true. Vlastník testovacího prostředí má přístup k testovacím prostředí, které je obor nižší než úroveň předplatného. Vlastník testovacího prostředí proto neuvidí virtuálních počítačů, virtuálních sítí nebo jiné prostředky, které jsou mimo testovacího prostředí.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Jak vytvořit roli, kterou chcete povolit uživatelům provádět konkrétní úlohy?
Komplexní článek o tom, jak vytvořit vlastní role a přiřadit oprávnění k roli najdete v části [udělení uživatelských oprávnění na určitém laboratorním zásady](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Tady je příklad skriptu, který vytvoří roli *Advanced uživatel služby DevTest Labs*, který má oprávnění ke spuštění a zastavení všech virtuálních počítačů v testovacím prostředí:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**CI/CD integrace a automatizace**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrovat DevTest Labs se Moje sadou nástrojů CI/CD?
Pokud používáte Azure DevOps, můžete použít [rozšíření DevTest Labs úloh](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) automatizace kanálu pro vydávání verzí ve službě DevTest Labs. Úlohy, které vám pomůžou s touto příponou patří:

* Vytvoření a nasazení virtuálního počítače automaticky. Také můžete nakonfigurovat virtuální počítač s nejnovější sestavení pomocí úloh Azure File Copy nebo prostředí PowerShell Azure DevOps služby.
* Automaticky zachytávat stav virtuálního počítače po testování pro reprodukci chyby na stejný virtuální počítač pro další zkoumání.
* Pokud už je nepotřebujete, odstraňte virtuální počítač na konci procesu vydávání verzí.

Následující příspěvky nabídka pokyny a informace o použití rozšíření Azure DevOps služby:

* [DevTest Labs a rozšíření Azure DevOps](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Nasazení nového virtuálního počítače v existující testovací prostředí DevTest Labs ze služeb Azure DevOps](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Pomocí nástroje Azure DevOps služby release management pro průběžné nasazení na DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pro další kontinuální integrace (CI) / průběžné doručování (CD) sady nástrojů, které můžete dosáhnout stejné scénáře podle nasazení [šablon Azure Resource Manageru](https://aka.ms/dtlquickstarttemplate) pomocí [rutin prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) a [Sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Můžete také použít [rozhraní REST API pro DevTest Labs](https://aka.ms/dtlrestapis) integrovat sady nástrojů.  


**Virtual Machines**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Proč se nezobrazují virtuální počítače v okně virtuální počítače, který se zobrazuje ve službě DevTest Labs?
Při vytváření virtuálního počítače ve službě DevTest Labs, budete mít oprávnění pro přístup k tomuto virtuálnímu počítači. Můžete zobrazit v okně labs a na virtuálním počítači **virtuálních počítačů** okno. Uživatelé přiřazení k roli uživatele testovacího prostředí DevTest Labs můžete zobrazit všechny virtuální počítače, které byly vytvořeny v testovacím prostředí cvičení **všechny virtuální počítače** okno. Uživatelé, kteří mají roli uživatele testovacího prostředí DevTest Labs však nejsou automaticky udělit oprávnění ke čtení na prostředky virtuálních počítačů, které jste vytvořili jiným uživatelům. Proto tyto virtuální počítače nejsou zobrazeny na **virtuálních počítačů** okno.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaký je rozdíl mezi vlastní image a vzorce?
Vlastní image je virtuální pevný disk (VHD). Vzorec je bitovou kopii, která můžete nakonfigurovat další nastavení a poté uložit a reprodukovat. Vlastní image může být vhodnější, pokud chcete rychle vytvořit několik prostředí s využitím stejné základní, neměnné image. Vzorec může být lepší, pokud chcete reprodukovat konfigurace virtuálního počítače nejnovější pomůcky, v rámci virtuální sítě nebo podsítě, nebo jako virtuální počítač určité velikosti. Podrobnější vysvětlení najdete v tématu [porovnání vlastních imagí a vzorců ve službě DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak můžu vytvořit víc virtuálních počítačů ze stejné šablony najednou?
Máte dvě možnosti, souběžně vytvoření více virtuálních počítačů ze stejné šablony:
* Můžete použít [rozšíření Azure DevOps úkolů](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Je možné [generovat šablony Resource Manageru](devtest-lab-add-vm.md#save-azure-resource-manager-template) při vytváření virtuálního počítače, a [nasazení šablony Resource Manageru z prostředí Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak můžu přesunout svoje existující virtuální počítače Azure do Moje testovací prostředí DevTest Labs?
Kopírování existujících virtuálních počítačů k DevTest Labs:

1. Kopírování souboru virtuálního pevného disku existujícího virtuálního počítače pomocí [skript prostředí Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Vytvoření vlastní image](devtest-lab-create-template.md) uvnitř testovacího prostředí DevTest Labs.
3. Vytvoření virtuálního počítače v testovacím prostředí z vlastní image.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Můžete připojit několik disků do virtuálních počítačů?
Ano, můžete připojit několik disků k vašim virtuálním počítačům.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Pokud chcete použít bitovou kopii operačního systému Windows pro testování, je nutné koupit předplatné MSDN?
Použití imagí klienta operačního systému Windows (Windows 7 nebo novější) pro vývoj a testování v Azure, musíte udělat jednu z následujících akcí:

- [Koupit předplatné MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Pokud máte smlouvu Enterprise Agreement, vytvořte předplatné Azure s [nabídka Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p).

Další informace o kreditech Azure pro každou nabídku MSDN najdete v tématu [Azure měsíční kredit pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Automatizace procesu nahrávání souborů virtuálního pevného disku k vytváření vlastních imagí
Automatizace nahrávání souborů virtuálního pevného disku k vytváření vlastních imagí, máte dvě možnosti:

* Použití [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) zkopírovat nebo nahrajte soubory virtuálního pevného disku do účtu úložiště, který je spojen s testovacím prostředí.
* Použití [Průzkumníka služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Průzkumník služby Storage je samostatná aplikace, která běží na Windows, OS X a Linux.   

Chcete-li najít cílový účet úložiště, který je spojen s testovacího prostředí:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. V nabídce vlevo vyberte **skupiny prostředků**.
3. Vyhledejte a vyberte skupinu prostředků, který je spojen s testovacího prostředí.
4. V části **přehled**, vyberte jeden z účtů úložiště.
5. Vyberte **Objekty blob**.
6. Vyhledejte nahrávání v seznamu. Pokud žádný neexistuje, vraťte se ke kroku 4 a zkuste použít jiný účet úložiště.
7. Použití **URL** jako cíl v příkazu AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Automatizace procesu odstranění všech virtuálních počítačů v Moje testovací prostředí
Odstranit virtuální počítače z testovacího prostředí na webu Azure Portal. Také můžete odstranit všechny virtuální počítače ve vaší laboratoři pomocí skriptu prostředí PowerShell. V následujícím příkladu v části **hodnoty změnit** komentář, upravte hodnoty parametrů. Můžete načíst `subscriptionId`, `labResourceGroup`, a `labName` hodnoty z podokna testovacího prostředí na webu Azure Portal.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.Name -like "$($lab.Name)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefakty**
## <a name="what-are-artifacts"></a>Co se o artefakty?
Artefakty jsou přizpůsobitelné prvky, které vám umožní nasazovat nejnovější části nebo nasazení nástroje pro vývojáře k virtuálnímu počítači. Při vytváření virtuálního počítače, připojte k vašemu virtuálnímu počítači artefakty. Po zřízení virtuálního počítače artefakty nasazení a konfigurace virtuálního počítače. Jsou k dispozici v různých artefaktů dříve existující naše [veřejného úložiště GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Můžete také [vytváření vlastních artefaktů](devtest-lab-artifact-author.md).


**Konfigurace testovacího prostředí**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak vytvořit testovací prostředí ze šablony Resource Manageru?
Nabízíme [úložišti GitHub šablon Azure Resource Manageru prostředí](https://aka.ms/dtlquickstarttemplate) , kterou můžete nasadit jako – nebo ho upravit k vytváření vlastních šablon pro vaše testovací prostředí. Každá šablona obsahuje odkaz na prostředí jako nasadit-je ve svém vlastním předplatném Azure. Nebo můžete přizpůsobit šablonu a [nasazení pomocí Powershellu nebo rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Proč jsou svoje virtuální počítače vytvořené v různých skupinách prostředků, libovolného jména? Můžete přejmenovat nebo změnit tyto skupiny prostředků?
Skupiny prostředků vytvořené tímto způsobem tak, aby DevTest Labs můžete spravovat uživatelská oprávnění a přístup k virtuálním počítačům. I když můžete přesun virtuálních počítačů do jiné skupiny prostředků a název, který chcete použít, doporučujeme vám, že nemusíte provádět změny skupin prostředků. Pracujeme na vylepšení tohoto prostředí umožňující větší flexibilitu.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Kolik testovacích prostředí můžete vytvořit pod stejné předplatné?
Není k dispozici konkrétní limitu počtu testovacích prostředí, které je možné vytvořit jedno předplatné. Objem prostředků používá jedno předplatné je však omezená. Informace o [omezení a kvót pro předplatná Azure](../azure-subscription-service-limits.md) a [tom, jak tyto limity zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Kolik virtuálních počítačů můžete vytvořit počet v testovacím prostředí
Neexistuje žádná konkrétní omezení počtu virtuálních počítačů, které je možné vytvořit počet v testovacím prostředí. Prostředky (počet jader virtuálního počítače, veřejné IP adresy a tak dále), které se používají jsou však omezená na jedno předplatné. Informace o [omezení a kvót pro předplatná Azure](../azure-subscription-service-limits.md) a [tom, jak tyto limity zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak se dá sdílet přímý odkaz na Moje testovací prostředí?

1. Na webu Azure Portal přejděte na testovací prostředí.
2. Zkopírujte adresu URL testovacího prostředí ve svém prohlížeči a pak ho sdílet s uživateli vašeho testovacího prostředí.

> [!NOTE]
> Pokud je uživatel lab externího uživatele, který má [účtu Microsoft](#what-is-a-microsoft-account), ale který není členem instance Active Directory vaší organizace, uživatel může zobrazit chybová zpráva při pokusu o přístup k sdílený odkaz. Pokud externí uživateli se zobrazí chybová zpráva, požádejte uživatele, aby nejdřív vyberte jeho jméno v pravém horním rohu webu Azure portal. Potom v **Directory** části nabídky, uživatel může vyberte adresář, kde testovacím prostředí existuje.
>
>

## <a name="what-is-a-microsoft-account"></a>Co je účet Microsoft?
Účet Microsoft je účet, který používáte pro téměř vše, co dělat s Microsoft zařízení a služeb. Je e-mailovou adresu a heslo, které používáte k přihlášení na Skype, Outlook.com, OneDrive, Windows phone a Xbox Live. Jeden účet znamená, že soubory, fotografie, kontakty a nastavení můžete sledovat na libovolném zařízení.

> [!NOTE]
> Účet Microsoft používá k volání *Windows Live ID*.
>
>


**Řešení potíží**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Při vytváření virtuálního počítače se nezdařilo tento artefakt. Jak ji můžu vyřešit?
Zjistěte, jak získat protokoly neúspěšných artefaktu, najdete v článku [Diagnostika selhání artefaktů ve službě DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Není Proč moje stávající virtuální sítě ukládání správně?
Jednou z možností je, že váš název virtuální sítě obsahuje období. Pokud ano, zkuste odebrat období nebo jejich náhradu za pomlčky. Poté se pokuste znovu uložit virtuální síť.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Proč se při zřizování virtuálního počítače z prostředí PowerShell získat chybu "Nadřazený prostředek nebyl nalezen"?
Pokud jeden prostředek je nadřazený objekt na jiný prostředek, musí existovat nadřazený prostředek vytvořit podřízený prostředek. Pokud se nadřazený prostředek neexistuje, zobrazí se **ParentResourceNotFound** zprávy. Pokud nezadáte závislost na nadřazený prostředek, podřízený prostředek může být nasazena nadřazeného objektu.

Virtuální počítače jsou podřízené prostředky v rámci testovacího prostředí ve skupině prostředků. Při použití šablon Resource Manageru k nasazení virtuálních počítačů pomocí Powershellu, název skupiny prostředků, které jsou k dispozici v skriptu prostředí PowerShell by měl být název skupiny prostředků testovacího prostředí. Další informace najdete v tématu [řešení potíží s běžnými chybami nasazení Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Kde najdu Další informace o chybě, pokud se nezdaří nasazení virtuálního počítače?
Chyby nasazení virtuálního počítače zachyceny v protokolech aktivit. Testovací prostředí můžete najít protokoly aktivit virtuálního počítače v části **protokoly auditu** nebo **Diagnostika virtuálního počítače** v nabídce prostředků v okně virtuálního počítače testovacího prostředí (v okně se zobrazí po výběru virtuálního počítače z **Můj virtuální počítače** seznamu).

V některých případech nasazení dojde k chybě před zahájením nasazení virtuálního počítače. Příkladem je při překročení limitu předplatného pro prostředek, který byl vytvořen s virtuálním Počítačem. Podrobnosti o chybě v tomto případě jsou zachyceny v protokolech aktivit úrovni testovacího prostředí. Protokoly aktivit jsou umístěné v dolní části **konfigurace a zásad** nastavení. Další informace o používání aktivit protokoly v Azure, najdete v článku [zobrazení protokolů aktivit pro auditování akcí u prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
