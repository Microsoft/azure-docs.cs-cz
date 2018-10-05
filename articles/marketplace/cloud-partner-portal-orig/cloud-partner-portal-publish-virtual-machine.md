---
title: Vytvoření nabídky virtuálních počítačů | Dokumentace Microsoftu
description: Publikování virtuálního počítače na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: e01fc839e901b39618990946a11ee8883dbe5f74
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809518"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publikování virtuálního počítače na webu Azure Marketplace

Tento článek obsahuje kroky potřebné k publikování nabídky virtuálních počítačů na webu Azure Marketplace.

## <a name="prerequisites"></a>Požadavky

Následující technická a Netechnická požadavky platí pro publikování virtuálního počítače na webu Azure Marketplace

### <a name="technical"></a>Technická

-   [Technické požadavky pro vytváření imagí virtuálních počítačů pro Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Vytvoření a nahrání linuxového virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Vytvořit & testovací virtuální počítač s Linuxem pomocí bitové kopie](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Vytvoření a nahrání virtuálního pevného disku Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Vytvořit & testovací virtuálního počítače s Windows pomocí bitové kopie](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Jak řešit běžné problémy během vytváření virtuálního pevného disku](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Doporučení zabezpečení pro Image Azure Marketplace](https://docs.microsoft.com/en-us/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Netechnická (požadavky)

 -   Vaše společnost (nebo její pobočka) se nachází v určité zemi zákazník podporovanými webem Azure Marketplace

-   Váš produkt musí mít licenci způsobem, který je kompatibilní s modely fakturace podporovanými webem Azure Marketplace

-   Zodpovídáte za zpřístupnění technické podpory pro zákazníky přiměřené způsobem. Tato podpora může bude zdarma, placená nebo komunitní.

-   Zodpovídáte za licencování váš software a všechny závislosti softwaru třetích stran.

-   Poskytnete obsah, který splňuje kritéria pro nabídky na webu Azure Marketplace a portálu pro správu Azure.

-   Souhlasíte s podmínkami zásady účasti na Azure Marketplace a smlouvě s vydavatelem.

-   Budete souhlasit s [Terms of Use](https://azure.microsoft.com/support/legal/website-terms-of-use/) , [prohlášení o ochraně osobních údajů Microsoft](http://www.microsoft.com/privacystatement/default.aspx), a [smlouvou Microsoft Azure Certified Program](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="before-you-begin"></a>Než začnete

Po splnění všech požadavků, můžete spustit vytváření šablony řešení nabídky. Než začnete, projděte si následující nabídky a informace o SKU.

**Nabídka**

V rámci nabídky Azure aplikace odpovídá třídu produktu, nabídky od vydavatele. Pokud máte nový typ řešení/aplikaci, která byste chtěli být k dispozici na webu Azure Marketplace, je nová nabídka nejlepším řešením. Nabídka je vlastně kolekce SKU. Každé nabídky se zobrazí jako vlastní entity na webu Azure Marketplace.

**SKU**

SKU je nejmenší zakoupitelná jednotka nabídky. V rámci stejné třídy produkt (nabídku), skladové položky vám umožní rozlišovat mezi různé podporované funkce. Například spravované nabídky nebo nespravované a různé modely fakturace jsou podporovány.

Přidání více jednotek SKU v následujících scénářích:
- Chcete zajistit podporu různými modely fakturace, jako je například používání vlastní licence (BYOL) nebo průběžných plateb (PAYG).
- Každý SKU podporuje různé funkce a každá sada funkcí se cenově liší.

Skladová položka se zobrazí v případě nadřazené nabídky na webu Azure Marketplace, se zobrazuje jako vlastní entita určené k prodeji na webu Azure portal.

## <a name="to-create-a-new-offer"></a>Chcete-li vytvořit novou nabídku

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com/).

2.  V levém navigačním panelu vyberte **+ nová nabídka**a pak vyberte **virtuálních počítačů**.

    ![Nová nabídka pro virtuální počítače](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  V části **nová nabídka**vyberte **Editor**.

![Nová nabídka editoru](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  V části **Editor**, budete poskytovat informace v následující zobrazení:
    - Nabídka nastavení
    - Skladové položky
    - Marketplace
    - Podpora každé zobrazení obsahuje sadu polí umožňující vyplnit. Povinné pole jsou označena červená hvězdička (\*)

## <a name="to-configure-offer-settings"></a>Ke konfiguraci nastavení nabídky

1. Nakonfigurujte následující **nabízejí Identity** polí v nabídky nastavení.

    **ID nabídky**

     Jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID je viditelný v adresách URL produktu, šablon Azure Resource Manageru, a oznámí fakturace. Můžete použít pouze malé alfanumerické znaky nebo spojovníky (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. Například, pokud vydavatele **contoso** publikuje nabídky s ID nabídky **ukázkový virtuální počítač**, zobrazí se v Azure marketplace jako **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >Toto pole je uzamčen, když dostane za provozu v rámci nabídky.

    **ID vydavatele**

    Rozevírací seznam pro profil vydavatele. Zvolte profil, který chcete publikovat nabídku v rámci. 
    >[!Note]
    >Toto pole je uzamčen, když dostane za provozu v rámci nabídky.

    **Název**

    Zobrazovaný název nabídky. Tento název se zobrazí na webu Azure Marketplace a na webu Azure Portal. Může mít maximálně 50 znaků. Použijte následující pokyny pro název nabídky:
    -  Do názvu zahrňte rozpoznatelný název značky pro váš produkt. 
    - Nezahrnujte název společnosti, pokud je jak nabídku na trh.
    - Pokud v rámci této nabídky máte marketing na svůj vlastní web, ujistěte se, že název shodný s názvem na vašem webu.

2. Vyberte **Uložit** dokončete nabídky nastavení.

## <a name="to-create-a-sku"></a>Chcete-li vytvořit SKU

1. Vyberte **SKU**. 
2. Vyberte **přidejte skladovou jednotku** k zadejte ID skladové položky. SKU ID je jedinečný identifikátor pro danou skladovou jednotku v rámci nabídky. Toto ID je viditelný v adresách URL produktu, šablon Azure Resource Manageru, a oznámí fakturace. SKU ID:
    - Pouze nesmí být delší než 50 znaků.
    - Můžete pouze obsahovat malé alfanumerické znaky nebo spojovníky (-).
    - Nemůže končit pomlčkou.

    ![Přidejte skladovou jednotku](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Nakonfigurujte podrobnosti SKU

Po přidání SKU, se zobrazí v seznamu skladových položek v zobrazení skladové položky. Chcete-li zobrazit podrobné údaje SKU, vyberte název skladové položky. Zobrazení podrobností o slouží k přidání informací do následujících polí.

### <a name="hide-this-sku"></a>Skrýt toto SKU

Toto nastavení použijte ke správě viditelnosti SKU. Pokud je vypnutý "Skrýt tuto skladovou Položku", SKU není viditelný v [Azure Marketplace](https://azuremarketplace.microsoft.com) a [webu Azure Portal](https://portal.azure.com/) zákazníkům. Můžete chtít skrýt skladovou Položku, pokud chcete pouze je k dispozici prostřednictvím šablon řešení a není pro nákup jednotlivě.

### <a name="cloud-availability"></a>Dostupnost cloudu

Toto pole můžete zadat dostupnosti pro vaši skladovou jednotku v různých Cloudů Azure.

**Veřejné Azure**

Tato skladová položka je možné nasadit zákazníkům ve všech veřejných oblastech Azure, které integraci webu Marketplace.

**Cloud Azure Government**

Tato skladová položka je možné nasadit v cloudu Azure Government. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), doporučujeme, abyste vydavatelé otestovat a ověřit svoje řešení funguje podle očekávání. Pro fázi a testování [požádat o zkušební verzi účtu](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>Microsoft Azure Government je cloud určený pro státní úřady s řízený přístup pro zákazníky od federální, stavu, místní nebo kmenové, opravňujících partnerů a k poskytování těchto entit.

### <a name="countryregion-availability"></a>Dostupnost země/oblast

Toto pole identifikuje oblasti, ve kterých se to se dá koupit skladovou jednotku. Je potřeba pečlivě zvážit, kde můžete zpřístupnit skladové jednotky. Některé země jsou klasifikovány jako "Microsoft daně uhrazené Country".

-   "Microsoft daně uhrazené ze zemí," společnost Microsoft shromažďuje daně od zákazníků a zaplatí (vykazovány) daň vládě.

-   V jiných zemích zodpovídají za shromažďuje daně, které zákazníkům a platit daň vládě partnery. Pokud chcete prodávat v těchto zemích, musíte mít možnost Vypočítat a hradit daně, které v nich.

![Vyberte zemi nebo oblast dostupnosti](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Ceny

Dvě cenové modely jsou aktuálně podporovány.

#### <a name="bring-your-own-license-byol"></a>Přineste si – vlastní licence (BYOL)

Spravujete licencování softwaru běžícího na virtuálním počítači. Microsoft vám bude účtovat jenom náklady na infrastrukturu.

#### <a name="usage-based-monthly-billed-sku"></a>Měsíční SKU se fakturuje podle využití

Zákazníkům účtovat podle sazeb nastavit vydavatelé o velikostech virtuálních počítačů pro každý za každou hodinu. V případě klíčových **hodinové fakturace** modelu SKU, celková cena bude součtem nákladů na software účtuje vydavatelem a účtovat microsoftem náklady na infrastrukturu. Toto celkové náklady se zobrazí zákazníka jako po hodinách a měsíční cena při nákupu zvažují. Fakturace v tomto případě bude každý měsíc.

V rámci modelu na základě využití jsou třeba další nastavení.

**Bezplatná zkušební verze**

Můžete zadat, pokud chcete poskytnout bezplatnou zkušební verzi pro vaše zákazníky.
Tady zákazník nebude účtovat, náklady na software pro prvních 30 nebo 90 dnů (v závislosti na výběru) po nasazení virtuálního počítače. Po ukončení bezplatného zkušebního období vám účtují poplatky podle sazeb nastavil vydavatelů v hodinový model pro každý za každou hodinu.

**Za jádro**

Můžete nastavit ceny podle počtu jader pro vaši skladovou jednotku. V takovém případě stačí zadat základní ceny pro jedním jádrem a jsme automaticky – výpočetní ceny pro ostatní jádra. Zadání ceny v USD na portálu a bude automaticky výpočtu ceny pro ostatní oblasti. Ceny v dalších oblastech můžete ověřit pomocí **exportovat Data ceny**

![Za jádro](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Diskrétní ceny**

Můžete nastavit, ceny pro každou sadu jader zvlášť pokud byste chtěli cena každé základní samostatně.

![Diskrétní ceny](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Export-Import ceny**

Máte možnost exportovat ceny, která je nakonfigurovaná prostřednictvím portálu provádět změny prostřednictvím rozhraní excel. To také umožňuje ověřit ceny podle oblasti a ceny v místní měny.
Kliknutím na **ceny Export** stáhne excelový soubor se předem vyplní podrobnosti o cenách. Bude možné upravit v rámci aplikace excel a potom pomocí **ceny importu** k importu, které byly provedeny změny.
Importované ceny se projeví i na portálu.

V této cenové Excelu jsou uvedené ceny pro různé oblasti v místní měně. Kurzu, který jsme použili se aktualizují každý den.

![Export-import ceny s příklady směnného kurzu](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   Ceny nelze změnit po ukončení nabídky za provozu. Však může i nadále budete moci přidat nebo odebrat oblasti jsou podporované.
>-   Tato cena se účtuje uživateli kromě [Azure\'ceny virtuálních počítačů s](http://aka.ms/vmpricingdetails).
>-   Ceny se nastavují pro všechny oblasti v místní měně, pomocí kurzů k dispozici měny v době nastavování ceny.
>-   Nastavit nebo zobrazit ceny v jednotlivých oblastech jednotlivě, ocenění tabulky export a import pomocí vlastní ceny.

## <a name="vm-images"></a>Image virtuálních počítačů

Další části a proveďte bude části Imagí virtuálních počítačů. Před přechodem do této části, musíte mít virtuální pevný disk, který chcete publikovat připravený. Tady je pár odkazů, což pomáhá vytvořit virtuální pevný disk:

-   [Technické požadavky pro vytváření imagí virtuálních počítačů pro Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Vytvoření a nahrání linuxového virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Vytvořit & testovací virtuální počítač s Linuxem pomocí bitové kopie](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Vytvoření a nahrání virtuálního pevného disku Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Vytvořit & testovací virtuálního počítače s Windows pomocí bitové kopie](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Jak řešit běžné problémy během vytváření virtuálního pevného disku](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

Jakmile se vaše virtuální pevný disk připravený, můžete začít vyplňování v této části.
Tady jsou některé podrobnosti pro některá pole.

### <a name="recommended-vm-sizes"></a>Doporučené velikosti virtuálních počítačů

Vyberte až šest doporučené velikosti virtuálního počítače. Jedná se o doporučení, která se zobrazí zákazníků na webu Azure Marketplace a v okně cenová úroveň na webu Azure Portal chvíli kdy se rozhodnou vaši image koupit a nasadit. **Jedná se jenom o doporučení. Zákazník se může vybrat jakoukoli velikost virtuálního počítače, které se vejdou disky určené ve vaší imagi.**  Následující snímek obrazovky ukazuje doporučené velikosti virtuálních počítačů, které zákazník uvidí na portálu Azure Portal.


![Doporučené velikosti virtuálních počítačů](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Otevření portů

Zadejte porty, na které jste provedli otevřený a dostupný. Tyto porty jsou otevřené během nasazení virtuálních počítačů.

### <a name="adding-vm-images"></a>Přidání Imagí virtuálních počítačů

Dalším krokem je přidání image virtuálního počítače pro vaši skladovou jednotku. Můžete přidat až 8 diskových verzí za skladové položky. Pouze nejvyšší verze číslo disku pro určité skladové položky se zobrazí na webu Azure Marketplace. Ostatní budou viditelné prostřednictvím rozhraní API.

V části **disková verze**vyberte **+ nová verze**. To ukazuje následující pole, které je třeba vyplnit.

#### <a name="vm-image-version"></a>Verze image virtuálního počítače

Verze image virtuálního počítače je potřeba postupovat podle [sémantickou verzi](http://semver.org/) formátu. Verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla. Obrázky v různých skladových položkách může mít různé hlavní verze a podverze. Verze v rámci SKU by měly být jenom přírůstkové změny, které zvyšují verzi opravy (Z z X.Y.Z).

#### <a name="os-vhd-url"></a>ADRESA URL VIRTUÁLNÍHO PEVNÉHO DISKU

Zadejte [sdíleného přístupového podpisu URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) pro operační systém virtuálního pevného disku.

Pokud je této skladové jednotce přidružené datové disky, můžete přidat tyto disky tak, že vyberete **+ nový datový disk** odkaz. Tato akce zobrazí další pole můžete vyplnit.

#### <a name="lun-vhd-url"></a>URL VIRTUÁLNÍHO PEVNÉHO DISKU LUN

Zadejte [sdíleného přístupového podpisu URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) pro datový disk.

#### <a name="lun-number"></a>Číslo logické jednotky

Přiřaďte tuto logickou jednotku číslo. Toto číslo se vyhradí pro tento disk data v této skladové Položce.

>[!Note]
>Po publikování SKU s danou verzí virtuálních počítačů a datové disky Zamknout a nedá se změnit. Pro všechny další virtuální počítač verze, které se přidají do skladovou Položku nelze změnit počet datových disků, které jsou potřebné pro podporu.

#### <a name="common-sas-url-issues--fixes"></a>Běžné problémy adresy URL SAS a opravy

| Problém                                                                 | Zpráva                                                                           | Napravit                                                           |  Odkaz na dokumentaci                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Chyba při kopírování Image - "?" nebyl nalezen v adrese url SAS                | Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS.       | Aktualizace adres Url SAS pomocí doporučené nástroje                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Chyba při kopírování Image - parametry "st" a "se" není v adrese url SAS   | Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS.        | Aktualizace adres Url SAS s počátečním a koncovým datem, které na něm             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Chyba při kopírování imagí – "sp = rl" není v adrese url SAS                    | Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS         | Aktualizace adres Url SAS pomocí oprávnění nastavená jako "Čtení" a "List     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Chyba při kopírování imagí – adresa url SAS mít prázdné znaky v názvu virtuálního pevného disku     | Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout objekt blob pomocí zadaný identifikátor Uri SAS.        | Aktualizace adres Url SAS bez mezer                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Chyba při kopírování imagí – chyby autorizace adres Url SAS               | Chyba: Kopírování bitové kopie. Nepodařilo se stáhnout objekt blob z důvodu chyby autorizace     | Znovu vygenerovat adresu SAS Url                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Konfigurace na webu Marketplace

Použít ke konfiguraci pole, která se zobrazují pro nabídky na Marketplace zobrazení [Azure Marketplace](https://azuremarketplace.microsoft.com) a na [webu Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID předplatných ve verzi Preview

Seznam ID předplatných Azure, který chcete mít přístup do nabídky při publikování nabídky. Tato předplatná uvedená prázdné využijete k otestování zobrazená v náhledu nabídka dříve než přidáte za provozu. Portál pro partnery můžete do seznamu povolených až 100 předplatných.

### <a name="suggested-categories"></a>Navrhované kategorií

Vyberte kategorie až 5 ze zadaného seznamu, který vaše nabídka může být nejlepší přidružený. Vybrané kategorie se použije k mapování vaší nabídky na o produktových kategoriích, které jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com) a [webu Azure Portal](https://portal.azure.com/).

Následující příklady ukazují informace z marketplace na webu Azure Marketplace a na webu Azure Portal.

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>Loga

Postupujte podle následujících pokynů loga nahráli na portál partnerů cloudu:

-   Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.

-   Jsou bílé barvy motivu na webu Azure portal a černé. Nepoužívejte tyto barvy jako barva pozadí vašeho loga. Použijte barvu, která by provedla vašeho loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy.

    >[!Note] 
    >Pokud pracujete s průhledným pozadím, ujistěte se, že nejsou bílé, loga nebo text černou nebo modrou.

-   Nepoužívejte v logu pozadí s barevným přechodem.

-   Předejde text loga. To zahrnuje vaše společnost nebo název značky. Vzhled a chování vašeho loga musí být *plochý* a přechody se měli vyhnout.

-   Logo by neměl být roztažená.

#### <a name="hero-logo"></a>Hero Logo

Hero logo je volitelné. Vydavatele můžete nahrávat Hero logo. Ale po nahrání loga ho nelze odstranit. Partner musí postupovat podle pokynů Azure Marketplace pro Hero ikony.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Pokyny pro ikona loga Hero

-   Zobrazovaný název vydavatele, název plánu a nabídky, dlouhé shrnutí jsou zobrazeny pomocí bílé barevné písma. Vyhněte se použití jakékoli světlé barevné na pozadí. Černá, bílý a průhledné pozadí nejsou povoleny pro Hero ikony.

-   Vydavatel zobrazovaný název, plán, title, nabídky, dlouhé shrnutí a tlačítka pro vytvoření jsou vložené prostřednictvím kódu programu uvnitř Hero logo při jejím uvedené. Při navrhování Hero logo, nezadávejte žádný text. Ponechte prázdné místo na pravé straně loga. Tento prostor by měl být 415 × 100 pixelů a je posunut 370 pixelů od levého okraje.

![Příklad hero loga](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Průběžná správa

Chcete-li konfigurovat nastavení správy zájemce nabídky, postupujte podle [tyto pokyny](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Konfigurace podpory

Pomocí zobrazení podpory uveďte následující informace:

- Kontaktní údaje z vaší společnosti, jako je například technikům podpory.
- Kontakty na podporu zákazníků.

## <a name="to-publish-the-offer"></a>Publikování nabídky

Posledním krokem je publikování nabídky. Postupujte podle [tyto pokyny k publikování vaší nabídky](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).
