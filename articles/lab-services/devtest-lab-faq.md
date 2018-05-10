---
title: Nejčastější dotazy k Azure DevTest Labs | Microsoft Docs
description: Odpovědi na časté otázky týkající se Azure DevTest Labs.
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
ms.openlocfilehash: a295cad2bf1cafce4dc64909174e9417daa7918e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-devtest-labs-faq"></a>Nejčastější dotazy k Azure DevTest Labs
Získejte odpovědi na některé nejčastější dotazy o Azure DevTest Labs.

**Obecné**
## <a name="what-if-my-question-isnt-answered-here"></a>Co když není zde odpovědi můj dotaz?
Pokud váš dotaz není zde uvedeno, dejte nám vědět, vám mohou pomoci vám najít odpověď.

* Odešlete dotaz na konci tohoto článku. Spojte se s týmem Azure Cache a ostatními členy komunity o tomto článku.
* K dosažení širší cílové skupiny, odeslat dotaz na [fórum Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Spojte se s týmem Azure DevTest Labs i ostatním členům komunity.
* Pro žádosti o funkce, odeslání požadavků a nápady, jak [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Proč používat Azure DevTest Labs?
Azure DevTest Labs můžete uložit team čas a peníze. Vývojáři mohou vytvářet své vlastní prostředí pomocí několika různých základny. Také můžete používají artefaktů můžete rychle nasadit a nakonfigurovat aplikace. Pomocí vlastních bitových kopií a vzorce můžete uložit virtuální počítače (VM) jako šablona a snadno je opakovat v týmu. DevTest Labs také nabízí několik konfigurovat zásady tohoto testovacího prostředí, které mohou správci plýtvání a spravovat týmových prostředí. Tyto zásady obsahují auto vypnutí, prahová hodnota náklady, maximální virtuálních počítačů na uživatele a maximální velikost virtuálního počítače. Podrobnější vysvětlení DevTest Labs, najdete v článku [přehled](devtest-lab-overview.md) nebo [úvodní video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Co znamená "obav samoobslužné služby"?
Bez obav samoobslužných znamená, že vývojáři a testerům, sada vytvořit svoje vlastní prostředí podle potřeby. Správci mají zároveň budete vědět, že DevTest Labs minimalizovat náklady na odpady a řízení zabezpečení. Správci mohou určit, které velikosti virtuálních počítačů je povoleno, maximální počet virtuálních počítačů, a když jsou virtuální počítače spuštění a vypnutí. DevTest Labs také umožňuje snadno monitorovat náklady a nastavit výstrahy, kterým budete vědět, jak jsou používány prostředky testovacího prostředí.

## <a name="how-can-i-use-devtest-labs"></a>Jak můžete použít DevTest Labs?
DevTest Labs je užitečné, kdykoli mají dev nebo testovací prostředí a chcete rychle reprodukovat nebo spravovat pomocí zásad náklady na ukládání.

Tady je několik scénářů, které používají DevTest Labs pro naše zákazníky:

* Spravovat vývojářů a testovací prostředí na jednom místě. Zásady použití ke snížení nákladů a vytvořit vlastní Image do sdílené složky sestavení napříč týmem.
* Vývoj aplikace s použitím vlastních bitových kopií pro uložení stavu disku v průběhu fáze vývoj.
* Sledování nákladů ve vztahu k průběh.
* Vytvořte velkokapacitního testovací prostředí pro testování záruku kvality.
* Pomocí artefaktů a vzorce snadno konfigurovat a reprodukujte aplikace v různých prostředích.
* Distribuce virtuálních počítačů pro hackathons (pracovní spolupráce pro vývoj nebo testování) a pak snadno zrušit jejich zřízení je při ukončení události.

## <a name="how-am-i-billed-for-devtest-labs"></a>Jak se fakturuje pro DevTest Labs?
DevTest Labs je bezplatná služba. Vytvoření prostředí a konfiguraci zásad, šablony a artefakty v DevTest Labs je zdarma. Platí pouze pro prostředky Azure používá v prostředích, jako jsou virtuální počítače, účty úložiště a virtuální sítě. Další informace o náklady na prostředky testovacího prostředí najdete v tématu [ceny Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Zabezpečení**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Jaké jsou různé úrovně zabezpečení v DevTest Labs?
Zabezpečení přístupu je dáno [řízení přístupu na základě Role (RBAC)](../role-based-access-control/built-in-roles.md). Informace o tom, jak funguje přístup, je dobré se další rozdíly mezi oprávnění, role a obor, podle definice RBAC.

* **Oprávnění**: oprávnění je definovaný přístup k určité akce. Oprávnění může být například přístup pro čtení ke všem virtuálním počítačům.
* **Role**: role je sadu oprávnění, které mohou být seskupeny a přiřazen k uživateli. Například uživatele k roli vlastníka předplatného má přístup ke všem prostředkům v rámci předplatného.
* **Obor**: obor je úrovní v hierarchii prostředek služby Azure. Například obor možné skupinu prostředků, jeden testovacího prostředí nebo celé předplatné.

V rámci oboru DevTest Labs existují dva typy rolí, které definují oprávnění uživatele:

* **Vlastník testovacího prostředí**: vlastník testovacího prostředí má přístup ke všem prostředkům v testovacím prostředí. Vlastník testovacího prostředí můžete upravit zásady, číst a zapisovat do všech virtuálních počítačů, změňte virtuální síť a tak dále.
* **Uživatel Lab**: uživatel testovacího prostředí můžete zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě. Ale uživatel testovacího prostředí nelze změnit zásady nebo všechny virtuální počítače, které byly vytvořené pomocí jiných uživatelů. 

Také můžete vytvořit vlastní role v DevTest Labs. Naučte se vytvářet vlastní role v DevTest Labs, najdete v tématu [udělení uživatelských oprávnění k zásad konkrétní testovacího](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Protože obory jsou hierarchické, když má uživatel oprávnění u určité oboru, uživateli je udělen těchto oprávnění v každé nižší úrovni oboru v oboru. Například pokud uživatel je přiřazenou roli vlastník předplatného, uživatel má přístup ke všem prostředkům v předplatném. Tyto prostředky zahrnují všechny virtuální počítače, všechny virtuální sítě a všechny laboratoře. Vlastník předplatného automaticky převezme roli vlastníka testovacího prostředí. Jako opak však není pravda. Vlastník testovacího prostředí má přístup k testovacím prostředí, které je obor nižší než úroveň předplatného. Proto testovacím vlastníka neuvidí virtuálních počítačů, virtuálních sítí nebo jiným prostředkům, které jsou mimo testovací prostředí.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Jak vytvořit roli, aby uživatelé mohli provádět konkrétní úlohu?
Komplexní článek o tom, jak vytvořit vlastní role a přiřaďte oprávnění role, naleznete v části [udělení uživatelských oprávnění k zásad konkrétní testovacího](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Tady je příklad skriptu, který vytvoří roli *DevTest Labs pokročilé uživatele*, který má oprávnění ke spuštění a zastavení všech virtuálních počítačů v testovacím prostředí:

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
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Umožňuje DevTest Labs integraci s Moje CI/CD nástrojů?
Pokud používáte Visual Studio Team Services, můžete použít [DevTest Labs úlohy rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) k automatizaci vaší verze kanálu v DevTest Labs. Některé úlohy, které můžete dělat s touto příponou patří:

* Vytvoření a nasazení virtuálního počítače automaticky. Také můžete nakonfigurovat virtuální počítač s posledním sestavení pomocí Azure File Copy nebo prostředí PowerShell Team Services úloh.
* Automaticky zaznamenejte stav virtuálního počítače po testování pro reprodukci chyby na stejného virtuálního počítače pro další šetření.
* Odstraňte virtuální počítač na konci kanálu verze, když už ho nepotřebují.

Následující příspěvky nabídka pokyny a informace o použití rozšíření Team Services:

* [Rozšíření Visual Studio Team Services a DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Nasazení nového virtuálního počítače ve stávající testovacího prostředí DevTest Labs z Team Services](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Použití správy verzí Team Services pro nepřetržitou nasazení do DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pro ostatní průběžnou integraci (CI) / toolchains nastavené průběžné doručování (CD), můžete dosáhnout stejné scénáře pomocí nasazení [šablon Azure Resource Manageru](https://aka.ms/dtlquickstarttemplate) pomocí [rutin prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) a [Sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Můžete taky použít [rozhraní REST API pro DevTest Labs](http://aka.ms/dtlrestapis) integrovat s vaší nástrojů.  


**Virtual Machines**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>V okně virtuálních počítačů, která je v DevTest Labs nejsou zobrazeny virtuální počítače
Když vytvoříte virtuální počítač v DevTest Labs, budete mít oprávnění k přístupu k tohoto virtuálního počítače. Můžete zobrazit v okně prostředí a na virtuální počítač **virtuální počítače** okno. Uživatelé přiřazení k roli uživatele testovacího prostředí DevTest Labs můžete zobrazit všechny virtuální počítače, které byly vytvořeny v testovacím prostředí v tomto prostředí **všechny virtuální počítače** okno. Uživatelé, kteří mají roli uživatele testovacího prostředí DevTest Labs však nejsou automaticky udělit oprávnění ke čtení na prostředky virtuálních počítačů, které jste vytvořili jiných uživatelů. Proto nejsou tyto virtuální počítače zobrazují na **virtuální počítače** okno.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaký je rozdíl mezi vlastní image a vzorce?
Vlastní image je virtuální pevný disk (VHD). Vzorec je bitovou kopii, kterou můžete nakonfigurovat další nastavení a potom uložte a reprodukujte. Vlastní image může být vhodnější než, pokud chcete rychle vytvořit několik prostředí pomocí stejné základní, neměnné bitové kopie. Vzorec může být lepší, pokud chcete reprodukujte konfiguraci virtuálního počítače s nejnovější bity v rámci virtuální síť nebo podsíť, nebo jako virtuální počítač určité velikosti. Podrobnější vysvětlení najdete v tématu [porovnání vlastních bitových kopií a vzorce v DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Vytvoření víc virtuálních počítačů ze stejné šablony najednou?
Máte dvě možnosti pro vytvoření současně víc virtuálních počítačů ze stejné šablony:
* Můžete použít [rozšíření Visual Studio Team Services úlohy](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Můžete [generovat šablony Resource Manageru](devtest-lab-add-vm.md#save-azure-resource-manager-template) při vytváření virtuálního počítače, a [nasazení šablony Resource Manageru z prostředí Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Přesunutí mé existující virtuální počítače Azure do mé testovacího prostředí DevTest Labs
Zkopírovat stávající virtuální počítače do DevTest Labs:

1. Zkopírujte soubor virtuálního pevného disku vašeho stávajícího virtuálního počítače pomocí [skript prostředí Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Vytvořit vlastní image](devtest-lab-create-template.md) uvnitř testovacího prostředí DevTest Labs.
3. Vytvoření virtuálního počítače v testovacím prostředí z vlastní image.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Můžete připojit více disků do virtuálních počítačů?
Ano, můžete připojit více disků do virtuálních počítačů.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Pokud chcete použít bitovou kopii operačního systému Windows pro moje testování, je nutné zakoupit předplatné MSDN?
Chcete-li použít Image operačního systému klientů systému Windows (Windows 7 nebo novější verze) pro vaše vývoj nebo testování v Azure, musíte udělat jednu z těchto možností:

- [Kupte si předplatné MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Pokud máte smlouvu Enterprise Agreement, vytvořte předplatné Azure s [Enterprise pro vývoj/testování nabídka](https://azure.microsoft.com/offers/ms-azr-0148p).

Další informace o kredity Azure pro každou nabídku MSDN najdete v tématu [platební měsíční Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak mohu automatizovat proces odesílání soubory virtuálního pevného disku pro vytvoření vlastní Image?
K automatizaci odesílání soubory virtuálního pevného disku pro vytvoření vlastní Image, máte dvě možnosti:

* Použití [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) zkopírovat nebo odeslat soubory virtuálního pevného disku k účtu úložiště, který je spojen s testovacím prostředí.
* Použití [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer je samostatná aplikace, která běží na systému Windows, OS X a Linux.   

Najít cílový účet úložiště, který je spojen s testovacího prostředí:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. V nabídce vlevo vyberte **skupiny prostředků**.
3. Najděte a vyberte skupinu prostředků, který je spojen s testovacího prostředí.
4. V části **přehled**, vyberte jeden z účtů úložiště.
5. Vyberte **objekty BLOB**.
6. Podívejte se na odeslání v seznamu. Pokud žádný neexistuje, vraťte se ke kroku 4 a zkuste jiný účet úložiště.
7. Použití **URL** jako cíl v příkazu AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak mohu automatizovat proces odstranění všech virtuálních počítačů v mé prostředí?
Virtuální počítače můžete odstranit z testovacího prostředí na portálu Azure. Také můžete odstranit všechny virtuální počítače ve vašem testovacím prostředí pomocí skriptu prostředí PowerShell. V následujícím příkladu v části **hodnoty změnit** komentář, upravte hodnoty parametrů. Můžete získat `subscriptionId`, `labResourceGroup`, a `labName` hodnoty z podokna testovacího prostředí na portálu Azure.

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
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefakty**
## <a name="what-are-artifacts"></a>Jaké jsou artefakty?
Artefakty je přizpůsobitelné prvky, které můžete použít k nasazení vaší nejnovější bits nebo nasazení vaší nástroje pro vývojáře k virtuálnímu počítači. Artefakty připojte k virtuálnímu počítači, při vytváření virtuálního počítače. Po zřízení virtuálního počítače artefakty nasazení a konfiguraci virtuálního počítače. Jsou k dispozici v různých dříve existující artefakty naše [veřejného úložiště GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Můžete také [vytvářet vlastní artefakty](devtest-lab-artifact-author.md).


**Konfiguraci testovacího prostředí**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Vytvoření testovacího prostředí z šablony Resource Manageru
Nabízíme [úložiště GitHub šablon Azure Resource Manager testovacím](https://aka.ms/dtlquickstarttemplate) , kterou můžete nasadit jako-je nebo upravit můžete vytvořit vlastní šablony pro vaše prostředí. Každá šablona obsahuje odkaz na prostředí jako nasadit – v předplatného Azure. Nebo můžete upravit šablonu a [nasadit pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Proč se vytvoří virtuálních počítačů v různých skupinách prostředků, s libovolné názvy? Můžete přejmenovat nebo upravit tyto skupiny prostředků?
Skupiny prostředků jsou vytvořené tímto způsobem, abyste mohli spravovat DevTest Labs, uživatelská oprávnění a přístup k virtuálním počítačům. I když můžete přesunout virtuální počítač do jiné skupiny prostředků a název, který chcete použít, doporučujeme vám, že nemusíte provádět změny skupiny prostředků. Pracujeme na vylepšení toto prostředí umožňující větší flexibilitu.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Kolik labs můžete vytvořit v rámci stejného předplatného?
Není k dispozici konkrétní limit počtu laboratořích, které lze vytvořit jedno předplatné. Je však omezená objem prostředků používá za předplatné. Další informace o [omezení a kvóty pro předplatná Azure](../azure-subscription-service-limits.md) a [postup tyto limity zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Kolik virtuálních počítačů můžete vytvořit za testovacího prostředí
Neexistuje žádné konkrétní omezení na počet virtuálních počítačů, které lze vytvořit za testovacího prostředí. Prostředky (virtuální počítač jader, veřejné IP adresy a tak dále), které se používají jsou však omezená na jedno předplatné. Další informace o [omezení a kvóty pro předplatná Azure](../azure-subscription-service-limits.md) a [postup tyto limity zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak sdílet přímý odkaz na můj testovacího prostředí?

1. Na portálu Azure přejděte do testovacího prostředí.
2. Zkopírujte adresu URL testovacího prostředí z prohlížeče a potom ho sdílet s uživateli testovacího prostředí.

> [!NOTE]
> Pokud je uživatel testovacím externí uživatel, který má [účtu Microsoft](#what-is-a-microsoft-account), ale který není členem instanci Active Directory vaší organizace, uživatel může zobrazit chybová zpráva při pokusu o přístup k sdílený odkaz. Pokud externí uživateli se zobrazí chybová zpráva, požádejte uživatele, aby nejprve vybrat jména v pravém horním rohu portálu Azure. Pak na **Directory** části nabídky, uživatel může vyberte adresář, kde existuje testovací prostředí.
>
>

## <a name="what-is-a-microsoft-account"></a>Co je účet Microsoft?
Účet Microsoft je účet, který použijete pro téměř vše, co se děje s Microsoft zařízení a služeb. Je e-mailovou adresu a heslo, které používáte pro přihlášení k Outlook.com, Skype, OneDrive, Windows phone a Xbox Live. Jeden účet znamená, že soubory, fotografie, kontakty a nastavení můžete podle můžete na libovolném zařízení.

> [!NOTE]
> Účet Microsoft používá k volání *Windows Live ID*.
>
>


**Řešení potíží**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Moje artefaktů došlo k chybě během vytváření virtuálních počítačů. Jak odstranit ji?
Další informace o získání protokoly pro vaše selhání artefaktů, najdete v tématu [postup diagnostikování selhání artefaktů v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Není proč můj stávající virtuální sítě ukládání správně?
Jednou z možností je, že název virtuální sítě obsahuje tečky. Pokud ano, zkuste odebrání období nebo jejich náhradu pomlčky. Pak zkuste znovu uložit virtuální sítě.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Proč se při můžu zřídit virtuální počítač z prostředí PowerShell zobrazí chyba "Nebyl nalezen nadřazený prostředek"?
Pokud jeden prostředek je nadřazený na jiný prostředek, nadřazený prostředek musí existovat před vytvořením podřízené prostředků. Pokud nadřazený prostředek neexistuje, zobrazí **ParentResourceNotFound** zprávy. Pokud nezadáte závislost na nadřazeném prostředku, před nadřazený může být nasazený podřízených prostředků.

Virtuální počítače jsou podřízené prostředky v testovacím prostředí ve skupině prostředků. Při použití šablony Resource Manageru k nasazení virtuálních počítačů pomocí prostředí PowerShell, název skupiny prostředků uvedené v skriptu prostředí PowerShell by měl být název skupiny prostředků v prostředí. Další informace najdete v tématu [odstraňování běžných chyb nasazení Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Kde můžete najít další informace o chybě, pokud se nezdaří nasazení virtuálního počítače?
Chyby nasazení virtuálního počítače zachyceny v protokoly aktivity. Testovacího prostředí můžete najít protokoly aktivity virtuálních počítačů v rámci **protokoly auditu** nebo **diagnostiky virtuálního počítače** v nabídce prostředků v okně v prostředí virtuálního počítače (v okně se zobrazí po výběru virtuálního počítače z **Můj virtuální počítače** seznamu).

V některých případech chyba nasazení se vyskytuje před začátkem nasazení virtuálního počítače. Příkladem je při překročení limitu předplatného pro prostředek, který byl vytvořen s virtuálním Počítačem. V takovém případě podrobnosti o chybě zachyceny v protokoly aktivity úrovni testovacího prostředí. Protokoly aktivity jsou umístěné v dolní části **konfiguraci a zásady** nastavení. Další informace o používání aktivita přihlásí Azure, najdete v části [zobrazit protokoly aktivity akce u prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
