---
title: Vytvoření image virtuálního počítače pro Azure Marketplace | Dokumentace Microsoftu
description: Podrobné pokyny o tom, jak vytvořit image virtuálního počítače pro Azure Marketplace pro ostatní uživatele k nákupu.
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 6737e16efa93370b5b5d2b46026fce3bbc22d38f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075154"
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Příručka k vytvoření image virtuálního počítače pro Azure Marketplace
Tento článek **kroku 2**, vás provede přípravou virtuálních pevných disků (VHD), které nasadíte do Azure Marketplace. Virtuální pevné disky jsou základem pro vaši skladovou jednotku. Proces se liší v závislosti na tom, jestli poskytujete skladovou jednotku založených na Linuxu nebo Windows. Tento článek popisuje oba scénáře. Tento proces se dá provádět zároveň s [vytváření účtů a registraci][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definování nabídky a skladové položky
V této části zjistíte, jak definovat nabídky a jejich přidružené skladové položky.

Nabídka je „nadřazený objekt“ všech skladových jednotek příslušné nabídky. Nabídek může být víc. Je jenom na vás, jak se rozhodnete svoje nabídky strukturovat. Když se nabídka převede do přípravy, převede se se všemi příslušnými skladovými jednotkami. Pečlivě zvažte svoje identifikátory skladových jednotek, protože budou vidět v adrese URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Azure portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Skladová jednotka je obchodní název pro image virtuálního počítače. Image virtuálního počítače obsahuje jeden operační systém disku a nula nebo více datových disků. Jde prakticky o kompletní profil úložiště pro virtuální počítač. Jeden virtuální pevný disk je potřeba na disk. I prázdné datové disky vyžadují vytvoření virtuálního pevného disku.

Bez ohledu na použitý operační systém přidávejte jenom nejmenší počet datových disků, které skladová jednotka potřebuje. Zákazníci nelze odebrat disky, které jsou součástí image v době nasazování však můžete vždy přidat disky během nebo po nasazení Pokud je budou potřebovat.

> [!IMPORTANT]
> *Neměňte počet disků v nové verzi image.* Pokud je nutné překonfigurovat datové disky na obrázku, definujte novou skladovou Položku. Publikování nové verze image s počty jiný disk bude mít riziko rozbíjející nové nasazení na základě nové verze image v případech, automatické škálování, automatické nasazení řešení pomocí šablony ARM a další scénáře.
>
>

### <a name="11-add-an-offer"></a>1.1 přidání nabídky
1. Přihlaste se k [portálu pro publikování] [ link-pubportal] pomocí svého účtu prodejce.
2. Vyberte **virtuálních počítačů** karta na portálu publikování. Do pole zobrazí zadejte název vaší nabídky. Název nabídky je obvykle název produktu nebo služby, který chcete prodávat na webu Azure Marketplace.
3. Vyberte **Vytvořit**.

### <a name="12-define-a-sku"></a>1.2 definice SKU
Jakmile budete mít přidanou nabídku, budete muset definovat a identifikovat skladové jednotky. Můžete mít více nabídek a každá nabídka může obsahovat více jednotek SKU je pod ním. Když se nabídka převede do přípravy, převede se se všemi příslušnými skladovými jednotkami.

1. **Přidejte skladovou jednotku.** SKU vyžaduje identifikátor, který se používá v adrese URL. Identifikátor musí být jedinečný ve vašem profilu publikování, ale neexistuje žádné riziko kolizí identifikátor s jinými vydavateli.

   > [!NOTE]
   > Nabídka a skladová položka identifikátory jsou zobrazeny v adrese URL nabídky na webu Marketplace.
   >
   >
2. **Přidejte souhrnný popis skladové jednotky.** Souhrnný popis jsou viditelné pro zákazníky, abyste měli dělat snadno čitelný. Tyto informace není potřeba zamykat, dokud se fáze "Nepřejde k přípravě". Do té doby je můžete upravovat.
3. Pokud používáte skladové jednotky založené na Windows, přejděte na navrhované odkazy, kde získáte schválené verze Windows Serveru.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Vytvoření virtuálního pevného disku kompatibilního s Azure (založený na systému Linux)
Tato část se zaměřuje na osvědčené postupy pro vytváření imagí virtuálních počítačů s linuxem pro Azure Marketplace. Podrobný návod najdete v následující dokumentaci: [Vytvoření vlastní image virtuálního počítače s Linuxem](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Vytvoření virtuálního pevného disku kompatibilního s Azure (založený na Windows)
Tato část se zaměřuje na kroky k vytvoření skladová jednotka založená na Windows serveru pro Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Ujistěte se, že používáte správné základní virtuální pevné disky
Operační systém virtuálního pevného disku pro vaši image virtuálního počítače musí být založené na Azure schválené základní imagi, která obsahuje Windows Server nebo SQL Server.

Chcete-li začít, vytvořte virtuální počítač z jednoho z následujících imagí, umístění [portálu Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter] [odkaz datactr-2012-r2], [2012 Datacenter] [link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 
* SQL Server 2012 SP2 

Tyto odkazy se dají najít i na Portálu publikování na stránce skladové jednotky.

> [!TIP]
> Pokud používáte aktuální portál Azure nebo PowerShell, Image Windows serveru publikované 8. září 2014 a později jsou schválené.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Vytvoření virtuálního počítače založené na Windows
Na portálu Microsoft Azure můžete vytvořit svůj virtuální počítač založený na schválené základní imagi v několika jednoduchých krocích. Následující seznam obsahuje přehled procesu:

1. Na stránce základní image zvolte **vytvořit virtuální počítač** přejdete do nového [portálu Microsoft Azure][link-azure-portal].

    ![Kreslení][img-acom-1]
2. Přihlaste se k portálu pomocí účtu Microsoft a heslem pro předplatné Azure, které chcete použít.
3. Postupujte podle výzev a vytvořte virtuální počítač pomocí základní image, kterou jste vybrali. Zadejte hostitele name (název počítače), uživatelské jméno (registrovaný jako správce) a heslo pro virtuální počítač.

    ![Kreslení][img-portal-vm-create]
4. Vyberte velikost nasazovaného virtuálního počítače:

    a.    Pokud se chystáte vyvíjet virtuální pevný disk v místním, velikost není důležitá. Zvažte možnost použít jeden z menších virtuálních počítačů.

    b.    Pokud se chystáte vyvíjet image v Azure, zvažte možnost použít pro zvolenou image jednu z doporučených velikostí virtuálního počítače.

    c.    Informace o cenách najdete **doporučené cenové úrovně** selektoru zobrazeném na portálu. Poskytne tři doporučené velikosti poskytnuté vydavatelem. (V tomto případě je vydavatelem Microsoft.)

    ![Kreslení][img-portal-vm-size]
5. Nastavte vlastnosti:

    a.    Pro rychlé nasazení, můžete nechat výchozí hodnoty pro vlastnosti z kategorie **volitelná konfigurace** a **skupiny prostředků**.

    b.    V části **účtu úložiště**, můžete volitelně vybrat účet úložiště, ve kterém se uloží operační systém virtuálního pevného disku.

    c.    V části **skupiny prostředků**, můžete volitelně vybrat logickou skupinu, do které chcete umístit virtuální počítač.
6. Vyberte **umístění** pro nasazení:

    a.    Pokud se chystáte vyvíjet virtuální pevný disk v místním, umístění není důležitá, protože odešlete image do Azure později.

    b.    Pokud se chystáte vyvíjet image v Azure, zvažte možnost od začátku používat jednu z oblastí Microsoft Azure ve Spojených státech. Tento výběr urychlí proces kopírování virtuálního pevného disku, že pokud jste svoji image odešlete k certifikaci, Microsoft provede za vás.

    ![Kreslení][img-portal-vm-location]
7. Klikněte na možnost **Vytvořit**. Virtuální počítač se začne nasazovat. Během několika minut budete mít úspěšné nasazení a můžete začít vytvářet image pro svoje skladové jednotky.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 vyvíjet virtuální pevný disk v cloudu
Důrazně doporučujeme vyvíjet virtuální pevný disk v cloudu pomocí protokolu RDP (Remote Desktop). Připojení k protokolu RDP se uživatelské jméno a heslo zadané během zřizování.

> [!IMPORTANT]
> **Nepoužívejte spravované disky.** Virtuální počítač, který slouží k vývoji virtuálního pevného disku do cloudu nesmí být založené na disky, které jsou spravované jako aktuálně nepodporuje vytvoření image z nich.
> Vytváří se virtuální počítač v volitelná funkce změnit výchozí nastavení pro disky spravovat.

> Pokud vyvíjíte svůj virtuální pevný disk místně (což nedoporučujeme), přečtěte si [vytvoření image virtuálního počítače v místním](marketplace-publishing-vm-image-creation-on-premise.md). Stahování virtuální pevný disk není nutný, pokud vyvíjíte v cloudu.
>
>

**Připojení přes protokol RDP pomocí [portálu Microsoft Azure][link-azure-portal]**

1. Vyberte **všechny služby** > **virtuálních počítačů**.
2. Otevře se okno Virtual machines. Ujistěte se, že běží virtuální počítač, který chcete propojit se službou a vyberte ho ze seznamu nasazených virtuálních počítačů.
3. Otevře se okno s popisem zvoleného virtuálního počítače. V horní části stránky, klikněte na tlačítko **připojit**.
4. Zobrazí se výzva k zadání uživatelského jména a hesla, které jste určili během zřizování.

**Připojení přes protokol RDP pomocí Powershellu**

Chcete-li stáhnout soubor vzdálené plochy do místního počítače, použijte [rutiny Get-AzureRemoteDesktopFile][link-technet-2]. Abyste tuto rutinu použili, budete muset znát název služby a název virtuálního počítače. Pokud jste vytvořili virtuální počítač z [portálu Microsoft Azure][link-azure-portal], najdete tyto informace ve vlastnostech virtuálního počítače:

1. Na portálu Microsoft Azure, vyberte **všechny služby** > **virtuálních počítačů**.
2. Otevře se okno Virtual machines. Vyberte virtuální počítač, který jste nasadili.
3. Otevře se okno s popisem zvoleného virtuálního počítače.
4. Klikněte na **Vlastnosti**.
5. První část názvu domény je název služby. Názvem hostitele je název virtuálního počítače.

    ![Kreslení][img-portal-vm-rdp]
6. Rutina pro stažení souboru RDP pro vytvořený virtuální počítač na místní plochu správce je následujícím způsobem.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Další informace o protokolu RDP najdete na webu MSDN v článku [připojit k virtuálnímu počítači Azure pomocí RDP nebo SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

**Konfigurace virtuálního počítače a vytvoření skladové jednotky**

Po operační systém, který se stáhne virtuální pevný disk použijte Hyper-v a konfigurace virtuálního počítače můžete začít vytvářet skladovou jednotku. Podrobný postup najdete v následujícím odkazu TechNetu: [Nainstalujte Hyper-v a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 výběr správné velikosti virtuálního pevného disku
Operačního systému Windows virtuální pevný disk ve vaší imagi virtuálního počítače měl vytvořit jako 128 GB pevného formátu virtuálního pevného disku.  

Pokud je fyzická velikost menší než 128 GB, měl by být virtuální pevný disk zhuštěný. Základní Image Windows a SQL Server k dispozici tyto požadavky už splňují, takže neměňte formát ani velikost získaného virtuálního pevného disku.  

Datové disky můžou být velké až 1 TB. Při rozhodování o velikosti disku, nezapomeňte, že zákazníci nemůžou měnit velikost virtuálních pevných disků v imagi v době nasazování. Datový disk VHD měl vytvořit jako virtuální pevný disk pevného formátu. Měly by být zhuštěné. Datové disky můžou být prázdné nebo můžou obsahovat data.

### <a name="35-install-the-latest-windows-patches"></a>3.5 instalaci nejnovějších oprav Windows
Základní image obsahují opravy, které byly v době publikace imagí nejnovější. Před publikováním operační systém virtuálního pevného disku, které jste vytvořili se ujistěte, že se spustil Windows Update a nainstalované všechny nejnovější kritické a důležité aktualizace zabezpečení.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 proveďte další konfigurace a plánování úloh podle potřeby
Pokud je potřeba další konfiguraci, zvažte možnost použít plánovanou úlohu, která se spustí při spuštění počítače a provede všechny finální změny k virtuálnímu počítači po nasazení:

* Osvědčilo se takové nastavení, kdy se úloha po úspěšném spuštění odstraní.
* Žádná konfigurace by se neměla spoléhat na jednotky jiné než jednotky C a D, protože tyto disky jsou pouze dva, které je vždycky zaručená. Jednotka C je disk s operačním systémem a jednotky D je dočasný místní disk.

### <a name="37-generalize-the-image"></a>3.7 Zobecněte image
Všechny Image v Tržišti Azure Marketplace musí být obecně opakovaně použitelné. Jinými slovy musí být zobecněn operační systém virtuálního pevného disku:

* Pro Windows, na obrázku by měl být "Sysprep" a by mělo být provedeno žádné konfigurace, které nepodporují **sysprep** příkazu.
* Spuštěním následujícího příkazu z WINDIR%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Pokyny týkající se nástroje Sysprep operační systém, najdete v kroku v následujícím článku MSDN: [Vytvoření a nahrání virtuálního pevného disku Windows serverem do Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Nasazení virtuálního počítače z virtuálních pevných disků
Po odeslání virtuální pevné disky (zobecněný virtuální pevný disk operačního systému a nula nebo více datových pevných disků) do účtu služby Azure storage, můžete je zaregistrovat jako uživatelskou image virtuálního počítače. Pak můžete otestovat této bitové kopie. Protože je zobecněný virtuální pevný disk operačního systému, nelze přímo nasadit virtuální počítač zadáním adresy URL virtuálního pevného disku.

Další informace o imagích virtuálních počítačů, najdete v těchto blogových příspěvcích:

* [Image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Jak prostředí PowerShell Image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [O imagích virtuálních počítačů v Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-classic-cli"></a>Nastavit potřebné nástroje, Powershellu a příkazového řádku Azure classic
* [Jak nastavit prostředí PowerShell](/powershell/azure/overview)
* [Postup instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="41-create-a-user-vm-image"></a>4.1 Vytvoření uživatelské image virtuálního počítače
#### <a name="capture-vm"></a>Zachycení virtuálního počítače
Přečtěte si na odkazy níže uvedené pokyny k zachycení virtuálního počítače pomocí rozhraní API, Powershellu nebo Azure CLI.

* [Rozhraní API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Zobecněte Image
Přečtěte si na odkazy níže uvedené pokyny k zachycení virtuálního počítače pomocí rozhraní API, Powershellu nebo Azure CLI.

* [Rozhraní API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 nasazení virtuálního počítače z uživatelské image virtuálního počítače
K nasazení virtuálního počítače z uživatelské image virtuálního počítače, můžete použít aktuální [webu Azure portal](https://manage.windowsazure.com) nebo prostředí PowerShell.

**Nasazení virtuálního počítače z aktuálního webu Azure portal**

1. Přejděte na **nový** > **Compute** > **virtuálního počítače** > **z Galerie**.

2. Přejděte na **Moje image**a potom vyberte image virtuálního počítače, ze kterého chcete virtuální počítač nasadit:

   1. Pozornosti Image můžete vybrat, protože **Moje image** zobrazení uvádí bitové kopie operačního systému a Image virtuálních počítačů.
   2. Počet disků vám pomůže určit, jaký typ image nasazujete, protože většina imagí virtuálních počítačů mají více než jeden disk. Je však stále možné mít image virtuálního počítače s pouze jedním diskem operačního systému, který by pak měl **počet disků** nastavena na hodnotu 1.

      ![Kreslení][img-manage-vm-select]
3. Postupujte podle pokynů Průvodce vytvořením virtuálního počítače a zadejte virtuální počítač název, virtuální počítač velikost, umístění, uživatelské jméno a heslo.

**Nasazení virtuálního počítače z prostředí PowerShell**

Pokud chcete nasadit velký virtuální počítač z nově vytvořené image generalizovaného virtuálního počítače, můžete použít následující rutiny.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Další pomoc najdete [Poradce při potížích s běžných problémů při vytváření virtuálního pevného disku].
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Získání certifikace pro vaši image virtuálního počítače
Dalším krokem přípravy vaší image virtuálního počítače pro Azure Marketplace je její certifikace.

Tento proces zahrnuje spuštění speciálního nástroje pro certifikaci, nahrání výsledků ověření do kontejneru Azure, kde jsou umístěné virtuální pevné disky, přidání nabídky, definování skladové jednotky a odeslání vaší image virtuálního počítače k certifikaci.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 stáhnout a spustit nástroj Certifikační testování Azure Certified
Certifikační nástroj poběží spuštěném virtuálním počítači, zřízeném z vaší uživatelské image virtuálního počítače zajistit, že je image virtuálního počítače kompatibilní s Microsoft Azure. Ověří, že jsou splněné pokyny a požadavky na přípravu vašeho virtuálního pevného disku. Výstupem nástroje je sestava kompatibility, která musí být nahrán na portálu pro publikování při žádost o certifikaci.

Certifikační nástroj můžete použít s Windows a virtuální počítače s Linuxem. Připojení virtuálních počítačů s Windows pomocí Powershellu a připojí virtuální počítače s Linuxem pak přes SSH.Net:

1. Nejdřív stáhněte certifikační nástroj [serveru pro stahování Microsoft][link-msft-download].
2. Otevřete certifikační nástroj a klikněte **spustit nový Test** tlačítko.
3. Z **informace o testu** obrazovky, zadejte název pro testovací běh.
4. Zvolte, jestli váš virtuální počítač používá Linux nebo Windows. Podle toho, který systém zvolíte, vyberte další možnosti.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Připojení certifikačního nástroje k imagi virtuálního počítače s Linuxem**
1. Zvolte režim ověřování SSH: heslo nebo soubor klíče.
2. Pokud používáte ověřování pomocí hesla, zadejte název systému DNS (Domain Name), uživatelské jméno a heslo.
3. Pokud používáte ověřování souborem klíče, zadejte název DNS, uživatelské jméno a umístění privátního klíče.

   ![Ověřování hesla z Image virtuálního počítače s Linuxem][img-cert-vm-pswd-lnx]

   ![Ověřování souborem klíče z Image virtuálního počítače s Linuxem][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Připojení certifikačního nástroje k imagi virtuálního počítače založené na Windows**
1. Zadejte plně kvalifikovaný název DNS virtuálního počítače (například MyVMName.Cloudapp.net).
2. Zadejte uživatelské jméno a heslo.

   ![Ověřování hesla Windows Image virtuálního počítače][img-cert-vm-pswd-win]

Až zvolíte správné možnosti pro vaši image Linuxu nebo virtuálních počítačů na bázi Windows, vyberte **Test připojení** zajistit, že SSH.Net nebo PowerShell mají platné připojení pro testovací účely. Po vytvoření připojení, vyberte **Další** spuštění testu.

Po dokončení testu dostanete výsledky (Prošel/Neprošel/Upozornění) pro každý prvek testu.

![Testovací případy pro Image virtuálního počítače s Linuxem][img-cert-vm-test-lnx]

![Testovací případy pro Image virtuálního počítače Windows][img-cert-vm-test-win]

Pokud selže některý z testů, nebude certifikaci vaší image. Pokud k tomuto problému dochází, zkontrolujte požadavky a proveďte potřebné změny.

Po dokončení automatizovaný test budete vyzváni, poskytuje jim další vstupy ve vaší imagi virtuálního počítače prostřednictvím dotazníku obrazovky.  Dokončení otázek a pak vyberte **Další**.

![Certifikační nástroj dotazník][img-cert-vm-questionnaire]

![Certifikační nástroj dotazník][img-cert-vm-questionnaire-2]

Po dokončení dotazník můžete zadat další informace, jako jsou informace o přístup SSH pro virtuální počítač s Linuxem, image a vysvětlení všech neúspěšných vyhodnocení. Kromě odpovědi na dotazník si můžete stáhnout výsledky testů a soubory protokolu pro prováděnou testovací případy. Uložte výsledky ve stejném kontejneru jako vaše virtuální pevné disky.

![Certifikace uložit výsledky testů][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 získání sdíleného přístupového podpisu URI vašich imagí virtuálních počítačů
Během procesu publikování zadáte identifikátory URI (URI), které vedou na jednotlivé virtuální pevné disky, které jste vytvořili pro vaši skladovou jednotku. Microsoft během certifikačního procesu potřebuje přístup k těmto virtuálním pevným diskům. Proto je potřeba vytvořit identifikátor URI sdíleného přístupového podpisu pro každý virtuální pevný disk. Tento identifikátor URI by měly být zadány ve **Imagí** karta na portálu publikování.

Sdílený přístupový podpis, který vytvořit identifikátor URI by měl splňovat následující požadavky:

Následující pokyny platí jenom pro nespravované disky, které jsou podporovány pouze typ.

* Při generování identifikátorů URI pro vaše virtuální pevné disky sdíleného přístupového podpisu, výpis a čtení oprávnění jsou dostačující. Neposkytujte přístup pro psaní nebo odstranění.
* Doba trvání přístupu by měl být minimálně tří (3) týdnů od vytvoření sdíleného přístupového podpisu URI.
* Pokud chcete ochranu pro čas UTC, vyberte den před aktuálním datem. Například pokud je aktuální datum 6. října 2014, vyberte 10 5. 2014.

Adresa URL SAS, mohou být generovány v několika způsoby, jak sdílet svůj virtuální pevný disk pro Azure Marketplace.
Toto jsou tři doporučených nástrojů:

1.  Azure Storage Explorer
2.  Průzkumník úložišť Microsoft
3.  Azure CLI

**Průzkumník služby Azure Storage (doporučeno pro uživatele Windows)**

Tady je postup pro vytvoření adresy URL SAS pomocí Průzkumníka služby Azure Storage

1. Stáhněte si [Azure Storage Explorer 6 Preview 3](https://azurestorageexplorer.codeplex.com/) na webu CodePlex. Přejděte na [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/) a klikněte na tlačítko **"Soubory ke stažení"**.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Stáhněte si [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) a nainstalovat po rozbalení ho.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Po dokončení instalace, otevřete aplikaci.
4. Klikněte na tlačítko **přidat účet**.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Zadejte název účtu úložiště, klíč účtu úložiště a doména koncových bodů úložiště. Tento účet úložiště se ve vašem předplatném Azure, kde mají uchovávat vašeho virtuálního pevného disku na portálu Azure portal.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Průzkumník služby Azure Storage po připojení k vašemu účtu konkrétní úložiště, se začnou zobrazovat všechny obsahuje v rámci účtu úložiště. Vyberte kontejner, ve kterém jste zkopírovali soubor VHD disku operačního systému (také datové disky případě, že jsou použitelné pro váš scénář).

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Po výběru kontejner objektů blob Azure Storage Explorer začne zobrazovat soubory v kontejneru. Vyberte soubor obrázku (VHD), který potřebuje k odeslání.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Po výběru souboru VHD v kontejneru, klikněte na tlačítko **zabezpečení** kartu.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  V **zabezpečení kontejneru objektů Blob** dialogové okno pole, ponechte výchozí hodnoty na **úroveň přístupu** kartu a potom klikněte na tlačítko **sdílené přístupové podpisy** kartu.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Podle následujících pokynů k vygenerování sdíleného přístupového podpisu URI pro VHD image:

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Z povolen přístup:** Pokud chcete ochranu pro čas UTC, vyberte den před aktuálním datem. Například pokud je aktuální datum 6. října 2014, vyberte 10 5. 2014.

    b. **Přístup k oprávnění:** Vyberte datum, které je nejméně tři týdny po **povolen přístup z** datum.

    c. **Povolené akce:** Vyberte **seznamu** a **čtení** oprávnění.

    d. Pokud jste vybrali soubor .vhd správně, je váš soubor se zobrazí v **název objektu Blob pro přístup k** s příponou VHD.

    e. Klikněte na tlačítko **podpis**.

    f. V **generované sdíleného přístupového podpisu identifikátoru URI tohoto kontejneru**, zkontrolujte následující zvýrazněný výše:

       - Ujistěte se, že název souboru bitové kopie a **"VHD"** jsou v identifikátoru URI.
       - Na konci podpisu, ujistěte se, že **"= rl"** se zobrazí. Tato hodnota ukazuje, že byl úspěšně poskytuje přístup pro čtení a seznam.
       - Ve středu podpis, ujistěte se, že **"sr = c"** se zobrazí. Tato hodnota ukazuje, že máte úrovně přístupu ke kontejneru

11. Aby bylo zajištěno, že generované sdílený přístup podpisu URI funguje, klikněte na tlačítko **otestovat v prohlížeči**. By se měl spustit proces stahování.

12. Zkopírujte URI sdíleného přístupového podpisu. Vložte tento identifikátor URI do publikování portálu.

13. Opakujte kroky 6 až 10 pro každý virtuální pevný disk ve skladové Položce.

**Microsoft Azure Storage Explorer (Windows/MAC/Linux)**

Tady je postup pro vytvoření adresy URL SAS pomocí Průzkumníka služby Microsoft Azure Storage

1.  Stáhněte si Microsoft Azure Storage Explorer formuláře [ http://storageexplorer.com/ ](http://storageexplorer.com/) webu. Přejděte na [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) a klikněte na tlačítko **"Stáhnout pro Windows"**.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Po dokončení instalace, otevřete aplikaci.

3.  Klikněte na tlačítko **přidat účet**.

4.  Konfigurace Microsoft Azure Storage Exploreru k předplatnému s přihlásit ke svému účtu

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Přejděte do účtu úložiště a pak vyberte kontejner

6.  Vyberte **"Get sdílené složky přístupový podpis..."** s použitím pravým tlačítkem myši na sady **kontejneru**

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Čas spuštění aktualizace, čas vypršení platnosti a oprávnění podle následujících

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Čas spuštění:** Pokud chcete ochranu pro čas UTC, vyberte den před aktuálním datem. Například pokud je aktuální datum 6. října 2014, vyberte 10 5. 2014.

    b.  **Čas vypršení platnosti:** Vyberte datum, které je nejméně tři týdny po **čas zahájení** datum.

    c.  **Oprávnění:** Vyberte **seznamu** a **čtení** oprávnění

8.  Zkopírujte URI sdíleného přístupového podpisu kontejneru

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Se vygenerovala adresa URL SAS pro kontejner úroveň a teď potřebujeme přidat název virtuálního pevného disku v ní.

    Formát adresy URL SAS na úrovni kontejneru: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Vložit název virtuálního pevného disku za název kontejneru v adrese URL SAS, jak je uvedeno níže `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Příklad:

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd je název virtuálního pevného disku, pak bude mít adresu URL virtuální pevný disk SAS `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Ujistěte se, že název souboru bitové kopie a **"VHD"** jsou v identifikátoru URI.
    - Ve středu podpis, ujistěte se, že **"sp = rl"** se zobrazí. Tato hodnota ukazuje, že byl úspěšně poskytuje přístup pro čtení a seznam.
    - Ve středu podpis, ujistěte se, že **"sr = c"** se zobrazí. Tato hodnota ukazuje, že máte úrovně přístupu ke kontejneru

9.  Ujistěte se, že generované sdílený přístup podpisu URI funguje, že ji otestujte v prohlížeči. By se měl spustit proces stahování

10. Zkopírujte URI sdíleného přístupového podpisu. Vložte tento identifikátor URI do publikování portálu.

11. Tyto kroky zopakujte pro každý virtuální pevný disk ve skladové jednotce.

**Azure CLI 2.0 (doporučeno pro mimo Windows a průběžné integrace)**

Toto jsou kroky pro vygenerování adresy URL SAS pomocí příkazového řádku Azure classic

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]

1.  Stáhněte si Microsoft Azure CLI z [tady](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Můžete také vyhledat odkazy na různé **[Windows](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)** a  **[MAC OS](https://docs.microsoft.com/cli/azure/install-azure-cli-macos?view=azure-cli-latest)**.

2.  Po stažení, tento nástroj nainstalujte.

3.  Vytvoření Bash (nebo jiného spustitelného souboru ekvivalentní skript) souboru následujícím kódem a uloží do místního prostředí

        export AZURE_STORAGE_ACCOUNT=<Storage Account Name>
        EXPIRY=$(date -d "3 weeks" '+%Y-%m-%dT%H:%MZ')
        CONTAINER_SAS=$(az storage container generate-sas --account-name -n vhds --permissions rl --expiry $EXPIRY -otsv)
        BLOB_URL=$(az storage blob url -c vhds -n <VHD Blob Name> -otsv)
        echo $BLOB_URL\?$CONTAINER_SAS

    Aktualizujte následující parametry v výše

    a. **`<Storage Account Name>`**: Zadejte název svého účtu úložiště

    b. **`<VHD Blob Name>`**: Zadejte název objektu blob služby virtuálního pevného disku.

    Vyberte datum, které je nejméně tři týdny po počátečním datu (výchozí nastavení generování tokenu sas). Příkladem hodnoty je: `2018-10-11T23:56Z`.

    Tady je příklad kódu po aktualizaci správné parametry exportu AZURE_STORAGE_ACCOUNT = vypršení platnosti vhdstorage1ba78dfb6bc2d8 = $(datum -d "tři týdny" "+ %Y-min %-dT % H: % MZ) CONTAINER_SAS = $(az storage kontejneru generovat sas - n virtuální pevné disky – oprávnění rl-- vypršení platnosti $EXPIRY - otsv) BLOB_URL = $(az storage blob url - c virtuální pevné disky - n osdisk_1ba78dfb6b.vhd - otsv) echo $BLOB_URL\?$CONTAINER_SAS

4.  Spusťte skript a poskytne vám adresa URL SAS pro úrovně přístupu ke kontejneru.

5.  Zkontrolujte vaše adresa URL SAS.

    - Ujistěte se, že váš název souboru obrázku a "VHD" jsou v identifikátoru URI.
    -   Ve středu podpis, ujistěte se, že se zobrazí "sp = rl". Tato hodnota ukazuje, že byl úspěšně poskytuje přístup pro čtení a seznam.
    -   Ve středu podpis, ujistěte se, že "sr = c" se zobrazí. Tento příklad ukazuje, že máte úrovně přístupu ke kontejneru

    Příklad:

    `https://vhdstorage1ba78dfb6bc2d8.blob.core.windows.net/vhds/osdisk_1ba78dfb6b.vhd?se=2018-10-12T00%3A04Z&sp=rl&sv=2018-03-28&sr=c&sig=...`

8.  Ujistěte se, že generované sdílený přístup podpisu URI funguje, že ji otestujte v prohlížeči. By se měl spustit proces stahování

9.  Zkopírujte URI sdíleného přístupového podpisu. Vložte tento identifikátor URI do publikování portálu.

10. Tyto kroky zopakujte pro každý virtuální pevný disk ve skladové jednotce.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 poskytují informace o imagi virtuálního počítače a žádost o certifikaci na portálu publikování
Jakmile vytvoříte svoji nabídku a skladovou Položku, měli byste zadat podrobnosti o imagi přidružené k dané skladové jednotce:

1. Přejděte [portálu pro publikování][link-pubportal]a pak se přihlaste pomocí svého účtu prodejce.
2. Vyberte **imagí virtuálních počítačů** kartu.
3. Identifikátor uvedený v horní části stránky je ve skutečnosti identifikátor nabídky, nikoli identifikátor skladové jednotky.
4. Vyplňte vlastnosti z kategorie **SKU** oddílu.
5. V části **operační systém řady**, klikněte na typ operačního systému, které jsou spojené s operačním systémem virtuálního pevného disku.
6. V **operačního systému** pole, popište operační systém. Zvažte formát jako je řada operačních systémů, typ, verze a aktualizace. Příkladem je "Windows Server Datacenter 2014 R2."
7. Vyberte až šest doporučené velikosti virtuálního počítače. Tyto velikosti jsou doporučení, která se zobrazí zákazníka v okně cenová úroveň na webu Azure Portal chvíli kdy se rozhodnou vaši image koupit a nasadit. **Jedná se jenom o doporučení. Zákazník se může vybrat jakoukoli velikost virtuálního počítače, které se vejdou disky určené ve vaší imagi.**
8. Zadejte verzi. Pole verze zapouzdřuje sémantickou verzi k identifikaci produktu a jeho aktualizace:
   * Verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla.
   * Obrázky v různých skladových položkách může mít různé hlavní verze a podverze.
   * Verze v rámci SKU by měly být jenom přírůstkové změny, které zvyšují verzi opravy (Z z X.Y.Z).
9. V **URL virtuálního pevného disku operačního systému** zadejte sdílený přístupový podpis pro operační systém virtuální pevný disk vytvořit identifikátor URI.
10. Pokud je této skladové jednotce přidružené datové disky, vyberte číslo logické jednotky (LUN), ke které byste chtěli tento datový disk připojit při nasazení.
11. V **URL virtuálního pevného disku LUN X** zadejte sdílený přístupový podpis pro první datový virtuální pevný disk vytvořit identifikátor URI.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Běžné problémy adresy URL SAS a opravy

|Problém|Zpráva o selhání|Napravit|Odkaz na dokumentaci|
|---|---|---|---|
|Chyba při kopírování Image - "?" nebyl nalezen v adrese url SAS|Chyba: Kopírování obrázků. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS.|Aktualizace adres Url SAS pomocí doporučené nástroje|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování Image - parametry "st" a "se" není v adrese url SAS|Chyba: Kopírování obrázků. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS.|Aktualizace adres Url SAS s počátečním a koncovým datem, které na něm|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování imagí – "sp = rl" není v adrese url SAS|Chyba: Kopírování obrázků. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS|Aktualizace adres Url SAS pomocí oprávnění nastavená jako "Čtení" a "List|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování imagí – adresa url SAS mít prázdné znaky v názvu virtuálního pevného disku|Chyba: Kopírování obrázků. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS.|Aktualizace adres Url SAS bez mezer|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování imagí – chyby autorizace adres Url SAS|Chyba: Kopírování obrázků. Nepodařilo se stáhnout objekt blob z důvodu chyby autorizace|Znovu vygenerovat adresu SAS Url|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování imagí – adresa Url SAS "st" a "se" parametry nemají specifikace úplné datum a čas|Chyba: Kopírování obrázků. Nepodařilo se stáhnout objekt blob z důvodu nesprávné adresy Url SAS |Parametry spuštění adresy Url SAS a koncové datum ("st", "se") musí mít specifikaci úplné datum a čas, jako je například 11-02-2017T00:00:00Z a ne pouze datum nebo zkrácené verze po dobu. Je možné se setkat se tento scénář s využitím Azure CLI verze 2.0 nebo vyšší. Ujistěte se, k poskytování specifikaci úplné datum a čas a znovu vygenerovat adresu SAS Url.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Další krok
Jakmile budete hotovi s podrobné údaje SKU, můžete se posunout dál a [marketingového obsahu Průvodce Azure Marketplace][link-pushstaging]. V tomto kroku procesu publikování zadáte marketingový obsah, ceny a jiné informace potřebné před **krok 3: Testování virtuální počítač v testovacím prostředí nabízejí**, kde můžete otestovat různé scénáře použití před nasazením nabídky na webu Azure Marketplace pro veřejné viditelnost a nákup.  

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: Publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
