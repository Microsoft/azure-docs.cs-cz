---
title: Vytváření bitové kopie virtuálního počítače pro Azure Marketplace | Microsoft Docs
description: Podrobné pokyny o tom, jak vytvořit bitovou kopii virtuálního počítače pro Azure Marketplace pro ostatní k nákupu.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: ad6d48a03575e8fabd7eed2ebc1f7926ec4559d4
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808736"
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Průvodce pro vytvoření bitové kopie virtuálního počítače pro Azure Marketplace
Tento článek **kroku 2**, vás provede procesem přípravy virtuálních pevných disků (VHD), které nasadíte do Azure Marketplace. Virtuální pevné disky jsou základ pro vaše SKU. Proces se liší v závislosti na tom, jestli tím SKU systémem Linux nebo systému Windows. Tento článek se týká obou scénářů. Tento postup lze provést paralelně s [vytváření účtů a registrace][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definování nabídky a SKU
V této části se dozvíte k definování nabídky a jejich přidružené SKU.

Nabídka je „nadřazený objekt“ všech skladových jednotek příslušné nabídky. Nabídek může být víc. Je jenom na vás, jak se rozhodnete svoje nabídky strukturovat. Když se nabídka převede do přípravy, převede se se všemi příslušnými skladovými jednotkami. Pečlivě zvažte vaše identifikátory SKU, protože se budou viditelné v adrese URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Portál Azure preview: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

SKU je komerční název image virtuálního počítače. Image virtuálního počítače obsahuje disk jeden operační systém a nula nebo více datových disků. Jde prakticky o kompletní profil úložiště pro virtuální počítač. Jeden virtuální pevný disk je potřeba na disk. Data i prázdné disky se vyžaduje virtuální pevný disk, který se má vytvořit.

Bez ohledu na použitý operační systém přidávejte jenom nejmenší počet datových disků, které skladová jednotka potřebuje. Zákazníci disky, které jsou součástí bitové kopie v době nasazení nelze odebrat, ale můžete vždy přidat disky během nebo po nasazení Pokud je potřebují.

> [!IMPORTANT]
> **Neměňte počet disků v nové verzi bitové kopie.** Pokud je nutné překonfigurovat datových disků v imagi, definujte nové SKU. Publikování nové verze bitové kopie s počty jiný disk bude mít potenciálně narušující nové nasazení založené na novou verzi bitové kopie v případech automatické škálování, automatické nasazení řešení pomocí šablony ARM a v dalších scénářích.
>
>

### <a name="11-add-an-offer"></a>1.1 přidání nabídky
1. Přihlaste se k [publikování portál] [ link-pubportal] pomocí účtu prodejce.
2. Vyberte **virtuální počítače** karta publikování portálu. Pole výzvami položka zadejte název nabídky. Název nabídky je obvykle název produktu nebo služby, který chcete prodeje v Azure Marketplace.
3. Vyberte **Vytvořit**.

### <a name="12-define-a-sku"></a>1.2 definice SKU
Po přidání nabídku, musíte definovat a identifikaci vaší SKU. Můžete mít více nabídkami a každý nabídka může mít více SKU v něm. Když se nabídka převede do přípravy, převede se se všemi příslušnými skladovými jednotkami.

1. **Přidáte SKU.** Verze SKU vyžaduje identifikátor, který se používá v adrese URL. Identifikátor musí být jedinečný v rámci vaší profil publikování, ale neexistuje žádné riziko identifikátor kolizí s jinými vydavateli.

   > [!NOTE]
   > Identifikátory SKU a nabídky se zobrazí v adrese URL nabídky na Marketplace.
   >
   >
2. **Přidejte souhrnný popis pro vaše SKU.** Souhrn popisy jsou viditelné pro zákazníky, takže si vytvořit snadno čitelné. Tyto informace nemusí být uzamčena dokud fázi "Push do přípravy". Do té doby je můžete upravovat.
3. Pokud používáte skladové jednotky založené na Windows, přejděte na navrhované odkazy, kde získáte schválené verze Windows Serveru.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Vytvoření virtuálního pevného disku kompatibilní s Azure (systémem Linux)
Tato část se zaměřuje na osvědčené postupy pro vytváření bitové kopie virtuálních počítačů na bázi systému Linux pro Azure Marketplace. Podrobný postup najdete v následující dokumentaci: [vytvořit vlastní image virtuálního počítače s Linuxem](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Vytvoření virtuálního pevného disku kompatibilní s Azure (založené na Windows)
Tato část se zaměřuje na postup vytvoření SKU, založené na Windows serveru pro Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Ujistěte se, že používáte správné základní virtuální pevné disky
Operační systém virtuálního pevného disku pro bitové kopie virtuálního počítače musí být založená na schválení Azure Základní bitová kopie obsahující systém Windows Server nebo SQL Server.

Chcete-li začít, vytvoření virtuálního počítače z jednoho z následujících bitových kopií, nachází na [portálu Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1] [link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent], [standardní][link-sql-2014-std], [webové] [ link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [standardní][link-sql-2012-std], [webové] [ link-sql-2012-web])

Tyto odkazy se dají najít i na Portálu publikování na stránce skladové jednotky.

> [!TIP]
> Pokud používáte aktuální portál Azure nebo prostředí PowerShell, jsou schváleny bitové kopie systému Windows Server publikované na 8. září 2014 a novější.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Vytvoření virtuálního počítače založené na systému Windows
Z portálu Microsoft Azure můžete vytvořit virtuální počítač na základě schválené základní bitové kopie v několika jednoduchých krocích. Toto je přehled procesu:

1. Na stránce základní bitovou kopii, vyberte **vytvořit virtuální počítač** přesměrovat na nový [portálu Microsoft Azure][link-azure-portal].

    ![Kreslení][img-acom-1]
2. Přihlaste se k portálu účtu Microsoft a heslo pro předplatné Azure, kterou chcete použít.
3. Postupujte podle výzev a vytvořte virtuální počítač s použitím základní bitové kopie, které jste vybrali. Budete muset poskytnout hostiteli name (název počítače), uživatelské jméno (zaregistrované jako správce) a heslo pro virtuální počítač.

    ![Kreslení][img-portal-vm-create]
4. Vyberte velikost virtuálního počítače pro nasazení:

    a.    Pokud máte v plánu vyvíjet virtuálního pevného disku na místě, velikost, nezáleží. Zvažte možnost použít jeden z menších virtuálních počítačů.

    b.    Pokud se chystáte vyvíjet image v Azure, zvažte možnost použít pro zvolenou image jednu z doporučených velikostí virtuálního počítače.

    c.    Informace o cenách najdete v části **doporučená cenové úrovně** selektor zobrazená na portálu. Poskytne tři doporučené velikosti poskytnuté vydavatelem. (V tomto případě je vydavatelem Microsoft.)

    ![Kreslení][img-portal-vm-size]
5. Nastavte vlastnosti:

    a.    Pro rychlé nasazení, můžete ponechat výchozí hodnoty pro vlastnosti v rámci **volitelné konfiguraci** a **skupiny prostředků**.

    b.    V části **účet úložiště**, můžete volitelně vybrat účet úložiště, ve kterém bude uložen operační systém virtuálního pevného disku.

    c.    V části **skupiny prostředků**, můžete volitelně vybrat logickou skupinu, do kterého chcete umístit virtuální počítač.
6. Vyberte **umístění** pro nasazení:

    a.    Pokud máte v plánu vyvíjet virtuálního pevného disku na místě, umístění není důležité, protože odešlete bitovou kopii do Azure později.

    b.    Pokud se chystáte vyvíjet image v Azure, zvažte možnost od začátku používat jednu z oblastí Microsoft Azure ve Spojených státech. Tím se urychlí proces kopírování virtuálního pevného disku, který provádí Microsoft vaším jménem při odesílání bitové kopie pro certifikaci.

    ![Kreslení][img-portal-vm-location]
7. Klikněte na možnost **Vytvořit**. Virtuální počítač spustí nasazení. Během několika minut budete mít úspěšné nasazení a můžete začít vytvářet image pro svoje skladové jednotky.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 vyvíjet svůj disk VHD v cloudu
Důrazně doporučujeme vývoji svůj disk VHD v cloudu pomocí protokolu RDP (Remote Desktop). Připojit k protokolu RDP s uživatelské jméno a heslo zadané při zřizování.

> [!IMPORTANT]
> **Nepoužívejte disky spravované.** Virtuální počítač použité k jejich vývoji virtuálního pevného disku do cloudu nesmí být založené na discích spravovaných jako aktuálně nepodporuje vytvoření bitové kopie z nich.
> Vytvoření virtuálního počítače v volitelná funkce změn na výchozí hodnoty pro disky spravovat.

> Pokud vyvíjíte svůj disk VHD najdete v části místní (což nedoporučujeme), [vytváření bitové kopie virtuálního počítače místní](marketplace-publishing-vm-image-creation-on-premise.md). Stahování svůj disk VHD není nutný, pokud vyvíjíte v cloudu.
>
>

**Připojení prostřednictvím protokolu RDP pomocí [portálu Microsoft Azure][link-azure-portal]**

1. Vyberte **všechny služby** > **virtuálních počítačů**.
2. Otevře se okno virtuálních počítačů. Zajistěte, aby virtuální počítač, který chcete propojit s běží a vyberte ho ze seznamu nasazených virtuálních počítačů.
3. Otevře se okno popisující vybraný virtuální počítač. V horní části, klikněte na tlačítko **Connect**.
4. Zobrazí se výzva k zadání uživatelského jména a hesla, který jste určili během zřizování.

**Připojení přes RDP pomocí prostředí PowerShell**

Chcete-li stáhnout soubor vzdálené plochy na místní počítač, použijte [rutiny Get-AzureRemoteDesktopFile][link-technet-2]. Chcete-li použít tuto rutinu, musíte znát název služby a název virtuálního počítače. Pokud jste vytvořili virtuální počítač z [portálu Microsoft Azure][link-azure-portal], můžete najít tyto informace v části Vlastnosti virtuálního počítače:

1. Na portálu Microsoft Azure, vyberte **všechny služby** > **virtuálních počítačů**.
2. Otevře se okno virtuálních počítačů. Vyberte virtuální počítač, který jste nasadili.
3. Otevře se okno popisující vybraný virtuální počítač.
4. Klikněte na **Vlastnosti**.
5. První část názvu domény je název služby. Názvem hostitele je název virtuálního počítače.

    ![Kreslení][img-portal-vm-rdp]
6. Stáhnout soubor RDP pro virtuální počítač vytvořený na plochu místní správce rutiny je následující.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Další informace o protokolu RDP naleznete na webu MSDN v článku [připojit k virtuální počítač Azure s protokolu RDP nebo SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Nakonfigurujte virtuální počítač a vytvořte vaše SKU**

Po operační systém, který je stáhli VHD použijte Hyper-v a konfigurace virtuálního počítače zahájíte proces vytváření vašeho SKU. Podrobné kroky najdete na následující odkaz na webu TechNet: [Hyper-v instalovat a konfigurovat virtuální počítač](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 vyberte správnou velikost virtuálního pevného disku
Operační systém Windows virtuálního pevného disku v bitové kopii virtuálního počítače by se vytvořit jako virtuální pevný disk pevného formátu 128 GB.  

Pokud je velikost fyzické méně než 128 GB, by měla být zhuštěných virtuální pevný disk. Poskytuje základní Image pro Windows a systému SQL Server již splňovat tyto požadavky, takže neměňte formát nebo velikost virtuálního pevného disku získat.  

Datových disků může být větší než 1 TB. Při rozhodování o velikosti disku, mějte na paměti, že zákazníci nemůže změnit velikost virtuálních pevných disků v rámci bitovou kopii v době nasazení. Datový disk virtuálních pevných disků by se vytvořit jako virtuální pevný disk pevného formátu. Měly by být zhuštěných. Datové disky můžou být prázdné nebo můžou obsahovat data.

### <a name="35-install-the-latest-windows-patches"></a>3.5 instalaci nejnovějších oprav systému Windows
Základní image obsahují opravy, které byly v době publikace imagí nejnovější. Před publikováním operačního systému virtuálního pevného disku, které jste vytvořili, ujistěte se, že Windows Update byl spuštěn a zda byly nainstalovány všechny nejnovější kritická a důležitá aktualizace zabezpečení.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 proveďte další úlohy konfigurace a plán, podle potřeby
Pokud není nutná další konfigurace, zvažte použití naplánovanou úlohu, která se spouští při spuštění proveďte změny v konečný k virtuálnímu počítači po jeho nasazení:

* Osvědčilo se takové nastavení, kdy se úloha po úspěšném spuštění odstraní.
* Žádná konfigurace se spoléhají na jednotkách než jednotky jazyka C nebo D, protože se jedná pouze dvě jednotky, které jsou vždy zaručit existovat. Jednotka C je disk operačního systému a jednotky D je dočasný místní disk.

### <a name="37-generalize-the-image"></a>3.7 Zobecněte bitovou kopii
Všechny bitové kopie v Azure Marketplace musí být znovu použitelné obecné způsobem. Jinými slovy musí být zobecněn operačního systému virtuálního pevného disku:

* Pro systém Windows, musí být bitovou kopii "Sysprep" a by mělo být provedeno žádné konfigurace, které nepodporují **sysprep** příkaz.
* Spuštěním následujícího příkazu z windir%\System32\Sysprep % adresáře.

        sysprep.exe /generalize /oobe /shutdown

  Informace o tom, jak operační systém pro nástroj sysprep najdete v krok v následujícím článku na webu MSDN: [vytvoření a nahrání virtuálního pevného disku serveru Windows Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Nasadit virtuální počítač z virtuální pevné disky
Po odeslání virtuální pevné disky (zobecněný virtuální pevný disk operačního systému a nula nebo více dat na disku VHD) pro účet úložiště Azure je můžete zaregistrovat jako uživatelské image virtuálního počítače. Potom můžete otestovat této bitové kopie. Všimněte si, že vzhledem k tomu, že je zobecněný virtuální pevný disk operačního systému, nelze nasadit přímo virtuálního počítače tím, že poskytuje adresu URL virtuálního pevného disku.

Další informace o bitové kopie virtuálního počítače, projděte si následující příspěvky blogu:

* [Image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Jak prostředí PowerShell bitové kopie virtuálního počítače](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [O Image virtuálních počítačů v Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Nastavit potřebné nástroje, prostředí PowerShell a rozhraní příkazového řádku Azure
* [Jak nastavit prostředí PowerShell](/powershell/azure/overview)
* [Postup instalace rozhraní příkazového řádku Azure](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 vytvořit uživatelské image virtuálního počítače
#### <a name="capture-vm"></a>Zachycení virtuálního počítače
Přečtěte si prosím odkazy níže uvedené pokyny k zachycení virtuálního počítače pomocí rozhraní API, Powershellu nebo Azure CLI.

* [Rozhraní API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Zobecněte Image
Přečtěte si prosím odkazy níže uvedené pokyny k zachycení virtuálního počítače pomocí rozhraní API, Powershellu nebo Azure CLI.

* [Rozhraní API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 nasaďte virtuální počítač z uživatelské image virtuálního počítače
Chcete-li nasadit virtuální počítač z uživatelské image virtuálního počítače, můžete použít aktuální [portál Azure](https://manage.windowsazure.com) nebo prostředí PowerShell.

**Nasadit virtuální počítač z aktuální portál Azure**

1. Přejděte na **nový** > **výpočetní** > **virtuálního počítače** > **z Galerie**.

2. Přejděte na **Moje image**a potom vyberte image virtuálního počítače, ze kterého chcete nasadit virtuální počítač:

   1. Zaměřit se na která image můžete vybrat, protože **Moje image** zobrazení obsahuje bitové kopie operačního systému a Image virtuálních počítačů.
   2. Prohlížení počet disků může pomoct určit, jaký typ obrázku nasazujete, protože většina Image virtuálních počítačů mají více než jeden disk. Je však stále možné, že image virtuálního počítače s pouze jednoho operačního systému disku, což by pak znamenalo **počet disků** nastavena na hodnotu 1.

      ![Kreslení][img-manage-vm-select]
3. Postupujte podle Průvodce vytvořením virtuálního počítače a zadejte virtuální počítač název, virtuální počítač velikost, umístění, uživatelské jméno a heslo.

**Nasadit virtuální počítač z prostředí PowerShell**

Pokud chcete nasadit velký virtuální počítač z zobecněný image virtuálního počítače, které jsou právě vytvořili, můžete použít následující rutiny.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> O další pomoc naleznete [Poradce při potížích s běžné problémy došlo při vytváření virtuálního pevného disku].
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Získat certifikační pro bitové kopie virtuálního počítače
Dalším krokem při přípravě image virtuálních počítačů pro Azure Marketplace je jej certifikaci.

Tento proces zahrnuje spuštění nástroje speciální certifikační, odesílání výsledky ověření ke kontejneru Azure, kde jsou umístěny virtuální pevné disky, přidání nabídku, definování vaší SKU a odesílání bitové kopie virtuálních počítačů pro certifikaci.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Stáhněte a spusťte nástroj pro testování certifikační pro certifikaci Azure
Spuštění nástroje certifikační v spuštěný virtuální počítač, zajištěného z vaší uživatelské image virtuálního počítače, pro zajištění, že image virtuálního počítače je kompatibilní s Microsoft Azure. Ověří, že jsou splněné pokyny a požadavky na přípravu vašeho virtuálního pevného disku. Výstup nástroje je zpráva o kompatibilitě, které musí být nahrán na portálu publikování při požadavku certifikační.

Nástroj certifikační lze použít s Windows a virtuální počítače s Linuxem. Připojení k systému Windows virtuální počítače pomocí prostředí PowerShell a připojí k virtuální počítače s Linuxem pomocí SSH.Net:

1. První, stáhněte si nástroj Certifikační na [společnosti Microsoft stažení][link-msft-download].
2. Otevřete nástroj certifikační a klikněte **spustit nový Test** tlačítko.
3. Z **testování informace** obrazovky, zadejte název pro test spustit.
4. Zvolte, jestli váš virtuální počítač používá Linux nebo Windows. Podle toho, který systém zvolíte, vyberte další možnosti.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Připojení nástroje certifikační do bitové kopie virtuálního počítače s Linuxem**
1. Zvolte režim ověřování SSH: heslo nebo soubor klíče.
2. Pokud používáte ověřování pomocí hesla, zadejte název, uživatelské jméno a heslo systému DNS (Domain Name).
3. Pokud používáte ověřování soubor klíče, zadejte název DNS, uživatelské jméno a umístění privátního klíče.

   ![Ověřování hesla bitové kopie virtuálních počítačů Linux][img-cert-vm-pswd-lnx]

   ![Soubor klíče ověřování bitové kopie virtuálních počítačů Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Připojení nástroje certifikační do bitové kopie virtuálních počítačů na bázi Windows**
1. Zadejte plně kvalifikovaný název DNS virtuálního počítače (například MyVMName.Cloudapp.net).
2. Zadejte uživatelské jméno a heslo.

   ![Ověřování hesla bitové kopie systému Windows virtuálního počítače][img-cert-vm-pswd-win]

Po výběru se správnými možnostmi pro bitové kopie virtuálního počítače založené na systému Windows nebo Linux, vyberte **Test připojení** zajistit, že SSH.Net nebo prostředí PowerShell není platné připojení pro účely testování. Po vytvoření připojení, vyberte **Další** spustíte test.

Po dokončení testu dostanete výsledky (Prošel/Neprošel/Upozornění) pro každý prvek testu.

![Testovací případy bitové kopie virtuálních počítačů Linux][img-cert-vm-test-lnx]

![Testovací případy pro Image virtuálního počítače Windows][img-cert-vm-test-win]

Pokud některý z testů selže, nebude musí být certifikovaná bitové kopie. Pokud k tomu dojde, zkontrolujte požadavky na a proveďte potřebné změny.

Po dokončení automatizovaných testů budete vyzváni k zadání dalších na image virtuálního počítače prostřednictvím dotazník obrazovky.  Dokončení otázky a potom vyberte **Další**.

![Dotazník nástroj certifikace][img-cert-vm-questionnaire]

![Dotazník nástroj certifikace][img-cert-vm-questionnaire-2]

Po dokončení dotazník, můžete poskytovat další informace, jako jsou informace o přístupu SSH pro virtuální počítač s Linuxem bitové kopie a vysvětlení pro všechny neúspěšné vyhodnocování. Kromě vaše odpovědi na dotazník můžete stáhnout výsledků testů a soubory protokolu pro spuštění testovací případy. Výsledky uložte ve stejném kontejneru jako virtuální pevné disky.

![Uložit certifikační výsledky testů][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 získáte sdílený přístupový podpis identifikátor URI pro vaše Image virtuálních počítačů
Během procesu publikování zadejte identifikátory URI (Identifier), které vést ke každému z virtuálních pevných disků jste vytvořili pro vaše SKU. Microsoft během certifikačního procesu potřebuje přístup k těmto virtuálním pevným diskům. Proto musíte vytvořit sdílený přístupový podpis identifikátor URI pro každý virtuální pevný disk. Toto je identifikátor URI, který by měly být zadány ve **bitové kopie** na portálu publikování.

Sdílený přístupový podpis, který vytvořili identifikátoru URI by měl splňovat následující požadavky:

Poznámka: následující pokyny platí pouze pro nespravované disky, které jsou podporovány pouze typ.

* Při generování sdílený přístupový podpis identifikátory URI pro virtuální pevné disky, je dostatečné oprávnění seznamu a pro čtení. Neposkytujte přístup pro psaní nebo odstranění.
* Dobu trvání pro přístup musí být minimálně týdny tří (3) ze při vytvoření sdílený přístupový podpis identifikátor URI.
* Aby se předešlo pro čas UTC, vyberte den, než aktuální datum. Například pokud je aktuální datum 6. října 2014, vyberte 5/10/2014.

SAS adresa URL může být generována v několika způsoby, jak sdílet svůj disk VHD pro Azure Marketplace.
Následují 3 doporučených nástrojů:

1.  Azure Storage Explorer
2.  Průzkumník úložišť Microsoft
3.  Azure CLI

**Azure Storage Explorer (doporučeno pro uživatele systému Windows)**

Toto jsou kroky pro vytvoření adresy URL SAS pomocí Azure Storage Explorer

1. Stáhněte si [Azure Storage Explorer 6 Preview 3](https://azurestorageexplorer.codeplex.com/) na webu CodePlex. Přejděte na [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/) a klikněte na tlačítko **"Server"**.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Stáhněte si [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) a po rozbalení ji nainstalovat.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Po instalaci, otevřete aplikaci.
4. Klikněte na tlačítko **přidejte účet**.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Zadejte název účtu úložiště, klíč účtu úložiště a úložiště koncové body domény. Toto je účet úložiště ve vašem předplatném Azure, kde mají uchovávat svůj disk VHD na portálu Azure.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Azure Storage Explorer po připojení k vašemu účtu konkrétní úložiště, zahájí se zobrazuje všechny obsahuje v rámci účtu úložiště. Vyberte kontejner, kam jste zkopírovali soubor VHD disku operačního systému (také datových disků Pokud jsou k dispozici pro váš scénář).

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Po výběru kontejneru objektů blob, Azure Storage Explorer spustí zobrazující soubory v kontejneru. Vyberte soubor bitové kopie (VHD), který musí být odeslána.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Po výběru souboru VHD v kontejneru, klikněte na **zabezpečení** kartě.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  V **zabezpečení kontejneru objektů Blob** dialogové okno pole, ponechte výchozí hodnoty na **úroveň přístupu** a pak klikněte **sdílené přístupové podpisy** kartě.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Postupujte podle následujících kroků a vygenerovat sdílený přístupový podpis identifikátor URI pro image VHD:

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Přístup povolen ze:** aby se předešlo pro čas UTC, vyberte den před aktuálním datem. Například pokud je aktuální datum 6. října 2014, vyberte 5/10/2014.

    b. **Umožnit přístup:** vyberte datum, které je minimálně 3 týdny po **přístup povolen ze** datum.

    c. **Akce povolené:** vyberte **seznamu** a **čtení** oprávnění.

    d. Pokud jste zvolili souboru VHD správně, pak se zobrazí v souboru **název objektu Blob pro přístup k** s příponou VHD.

    e. Klikněte na tlačítko **generování podpisu**.

    f. V **vygenerovat sdílený přístup podpis identifikátor URI tohoto kontejneru**, zkontrolujte následující kroky jako zvýrazněná výše:

       - Ujistěte se, že název souboru bitové kopie a **"VHD"** jsou v identifikátoru URI.
       - Na konci podpis, ujistěte se, že **"= rl"** se zobrazí. To znamená, že přístup pro čtení a seznamu zadaná úspěšně.
       - Uprostřed podpis, ujistěte se, že **"sr = c"** se zobrazí. Tento příklad ukazuje, abyste měli přístup na úrovni kontejneru

11. Aby se zajistilo, že vygenerovaného sdílet přístup podpis URI funguje, klikněte na tlačítko **Test v prohlížeči**. By se měl spustit proces stahování.

12. Zkopírujte sdílený přístupový podpis identifikátor URI. Je to identifikátor, který se má vložit do Portálu publikování.

13. Opakujte kroky 6-10 pro každý virtuální pevný disk v verze SKU.

**Microsoft Azure Storage Explorer (Windows nebo MAC/Linux)**

Toto jsou kroky pro vytvoření adresy URL SAS pomocí Microsoft Azure Storage Explorer

1.  Stáhněte si Microsoft Azure Storage Explorer formuláře [ http://storageexplorer.com/ ](http://storageexplorer.com/) webu. Přejděte na [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) a klikněte na tlačítko **"Stáhnout pro systém Windows"**.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Po instalaci, otevřete aplikaci.

3.  Klikněte na tlačítko **přidejte účet**.

4.  Nakonfigurovat k předplatnému Microsoft Azure Storage Explorer přihlášení ke svému účtu

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Přejděte k účtu úložiště a pak vyberte kontejner

6.  Vyberte **"Get přístupový podpis sdílenou složku..."** Klikněte pravým tlačítkem z pomocí **kontejneru**

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Čas spuštění aktualizace, čas vypršení platnosti a oprávnění podle následující

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Čas spuštění:** aby se předešlo pro čas UTC, vyberte den před aktuálním datem. Například pokud je aktuální datum 6. října 2014, vyberte 5/10/2014.

    b.  **Čas vypršení platnosti:** vyberte datum, které je minimálně 3 týdny po **čas spuštění** datum.

    c.  **Oprávnění:** vyberte **seznamu** a **čtení** oprávnění

8.  Zkopírujte URI sdílený přístupový podpis kontejneru

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Adresa URL generovaného SAS pro kontejner úroveň a nyní budeme muset přidat název virtuálního pevného disku v ní.

    Formát adresy URL úrovně SAS kontejneru: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Za název kontejneru v adrese URL SAS, jak je uvedeno níže vložit název disku VHD `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Příklad:

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd je název virtuálního pevného disku, pak bude mít adresu URL SAS virtuálního pevného disku `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Ujistěte se, že název souboru bitové kopie a **"VHD"** jsou v identifikátoru URI.
    - Uprostřed podpis, ujistěte se, že **"sp = rl"** se zobrazí. To znamená, že přístup pro čtení a seznamu zadaná úspěšně.
    - Uprostřed podpis, ujistěte se, že **"sr = c"** se zobrazí. Tento příklad ukazuje, abyste měli přístup na úrovni kontejneru

9.  Aby se zajistilo, že vygenerovaného sdílet přístup podpis URI funguje, otestujte ji v prohlížeči. By se měl spustit proces stahování

10. Zkopírujte sdílený přístupový podpis identifikátor URI. Je to identifikátor, který se má vložit do Portálu publikování.

11. Tyto kroky zopakujte pro každý virtuální pevný disk ve skladové jednotce.

**Rozhraní příkazového řádku Azure (doporučeno pro jiný systém než Windows & nepřetržité integrace)**

Toto jsou kroky pro vytvoření adresy URL SAS pomocí rozhraní příkazového řádku Azure

1.  Stáhněte si Microsoft Azure CLI z [zde](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Můžete také najít jiné odkazy pro **[Windows](http://aka.ms/webpi-azure-cli)** a  **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Po jeho stažení, nainstalujte prosím

3.  Vytvoření prostředí PowerShell (nebo jiného spustitelného souboru skriptu) soubor s následujícím kódem a uložit místně

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Aktualizujte následující parametry ve výše

    a. **`<StorageAccountName>`**: Zadejte název účtu úložiště

    b. **`<Storage Account Key>`**: Zadejte klíč účtu úložiště

    c. **`<Permission Start Date>`**: Aby se předešlo pro čas UTC, vyberte den, než aktuální datum. Například pokud je aktuální datum 25. října 2016, pak hodnota by měla být 10/25/2016. Pokud používáte Azure CLI 2.0 (az příkaz), zadejte datum a čas v počátečním a koncovým datem, například: 10-25-2016T00:00:00Z.

    d. **`<Permission End Date>`**: Vyberte datum, které je minimálně 3 týdny po **počáteční datum**. Hodnota musí být **11/02/2016**. Pokud používáte Azure CLI 2.0 (az příkaz), zadejte datum a čas v počátečním a koncovým datem, například: 11-02-2016T00:00:00Z.

    Následuje příklad kódu po aktualizaci správné parametry

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=<account-key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Otevřete editor prostředí Powershell s režimem "Spustit jako správce" a otevřete soubor v kroku #3. Můžete použít libovolný editor skriptů, který je k dispozici v operačním systému.

5.  Spusťte skript a jeho získáte adresu SAS pro kontejner úrovně přístupu

    Následující bude výstup podpis SAS a zkopírujte části zvýrazněných v programu Poznámkový blok

    ![Kreslení](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Nyní budete mít úroveň kontejneru SAS adresa URL a budete muset přidat název disku VHD v ní.

    Kontejner úrovně SAS URL #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Název disku VHD vložení za název kontejneru v adrese URL SAS, jak je uvedeno níže `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Příklad:

    TestRGVM201631920152.vhd je název virtuálního pevného disku, pak bude mít adresu URL SAS virtuálního pevného disku

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Ujistěte se, že název souboru obrázku a "VHD" jsou v identifikátoru URI.
    -   Uprostřed podpis, ujistěte se, že se zobrazí "sp = rl". To znamená, že přístup pro čtení a seznamu zadaná úspěšně.
    -   Uprostřed podpis, ujistěte se, že "sr = c" se zobrazí. Tento příklad ukazuje, abyste měli přístup na úrovni kontejneru

8.  Aby se zajistilo, že vygenerovaného sdílet přístup podpis URI funguje, otestujte ji v prohlížeči. By se měl spustit proces stahování

9.  Zkopírujte sdílený přístupový podpis identifikátor URI. Je to identifikátor, který se má vložit do Portálu publikování.

10. Tyto kroky zopakujte pro každý virtuální pevný disk ve skladové jednotce.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 poskytují informace o image virtuálního počítače a požádat o certifikaci publikování portálu
Po vytvoření nabídku a SKU, měli byste zadat podrobnosti bitové kopie, které jsou přidružené k této SKU:

1. Přejděte na [publikování portál][link-pubportal]a pak se přihlaste pomocí účtu prodejce.
2. Vyberte **Image virtuálních počítačů** kartě.
3. Identifikátor uvedených v horní části stránky, je ve skutečnosti identifikátor nabídka a není identifikátor SKU.
4. Vyplňte vlastnosti v rámci **SKU** části.
5. V části **operační systém řady**, klikněte na typ operačního systému, které jsou spojené s operačním systémem virtuálního pevného disku.
6. V **operačního systému** pole, popisují operačního systému. Vezměte v úvahu formátu například řada operačního systému, typ, verze a aktualizace. Příkladem může být "Windows Server Datacenter 2014 R2."
7. Vyberte až šest velikosti doporučené virtuálních počítačů. Toto jsou doporučení, které získat zobrazují zákazníkovi v okně cenová úroveň na portálu Azure, když se rozhodnete koupit a nasazení bitové kopie. **Jsou to jenom doporučení. Zákazník je možné vybrat libovolnou velikost virtuálního počítače, která bude vyhovovat disky zadané v bitové kopii.**
8. Zadejte verzi. Pole verze zapouzdří sémantickou verzi k identifikaci produktu a jeho aktualizací:
   * Verze musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
   * Bitové kopie v různých SKU může mít jiný hlavní verze a podverze.
   * Verze v rámci SKU by měl být pouze přírůstkové změny, které zvyšují oprav verze (Z z X.Y.Z).
9. V **URL virtuálního pevného disku operačního systému** zadejte sdílený přístupový podpis URI vytvořený pro operační systém virtuálního pevného disku.
10. Pokud existují datových disků přidružených tento SKU, vyberte číslo logické jednotky (LUN), do kterého chcete tento disk data k upevnění po nasazení.
11. V **LUN X virtuálního pevného disku URL** zadejte sdílený přístupový podpis URI vytvořený pro první data virtuálního pevného disku.

    ![Kreslení](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Běžné problémy SAS URL & opravy

|Problém|Zpráva o selhání|Napravit|Dokumentace k propojení|
|---|---|---|---|
|Chyba při kopírování bitové kopie - "?" nebyl nalezen v adrese url SAS|Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout blob pomocí zadaný identifikátor Uri pro SAS.|Aktualizace, pomocí SAS adresa Url, které se doporučuje nástroje|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování bitové kopie - parametry "st" a "se" není v adrese url SAS|Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout blob pomocí zadaný identifikátor Uri pro SAS.|Aktualizovat adresu Url SAS s počátečním a koncovým datem na něm|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování bitové kopie - "sp = rl" není v adrese url SAS|Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout blob pomocí zadaný identifikátor Uri pro SAS|Aktualizovat adresu Url SAS s oprávněními nastavenými jako "Číst" a "seznamu|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování bitové kopie - SAS url obsahovat prázdné znaky v názvu virtuálního pevného disku|Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout blob pomocí zadaný identifikátor Uri pro SAS.|Aktualizujte adresu Url SAS, bez mezer|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování bitové kopie – Chyba autorizace adres Url SAS|Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout blob kvůli chybě autorizace|Znovu vygenerovat adresu SAS Url|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Chyba při kopírování bitové kopie – adresa Url SAS "st" a "se" parametry nemají specifikace úplné data a času|Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout objektů blob z důvodu nesprávné SAS adresa Url |Parametry Start SAS adresa Url a koncové datum ("st", "se") musí mít specifikace úplné datum a čas, jako je například 11-02-2017T00:00:00Z a ne jen data nebo zkrácení verze po dobu. Je možné setkat tento scénář pomocí Azure CLI 2.0 (az příkaz). Nezapomeňte zadat specifikace úplné datum a čas a znovu je obnovovat SAS adresa Url.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Další krok
Jakmile jste hotovi s podrobnostmi SKU, můžete přesunout dál [marketing vodítko obsahu Azure Marketplace][link-pushstaging]. V tomto kroku procesu publikování zadáte marketing obsahu, ceny a jiné informace, které jsou potřebné před **krok 3: testování virtuální počítač v pracovní nabízejí**, kde můžete testovat různé scénáře případ použití před nasazením nabídnout Azure Marketplace pro veřejné viditelnost a nákup.  

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)

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
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
