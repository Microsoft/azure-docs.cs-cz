---
title: Azure Portal fondu hostitelů virtuálních počítačů s Windows – Azure
description: Postup vytvoření fondu hostitelů virtuálních počítačů s Windows pomocí Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 60566b95447c1b69fb257435f45a11524ac5d8b2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617335"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Kurz: Vytvoření fondu hostitelů pomocí Azure Portal

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Objekty, které vytvoříte pomocí virtuálního počítače s Windows (Classic), se nedají spravovat pomocí Azure Portal.

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích virtuálních počítačů s Windows. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou můžou uživatelé interaktivně pracovat, jako by na fyzickém počítači.

Tento článek vás provede procesem instalace pro vytvoření fondu hostitelů pro prostředí virtuálních ploch Windows prostřednictvím Azure Portal. Tato metoda poskytuje uživatelské rozhraní založené na prohlížeči k vytvoření fondu hostitelů na virtuálním počítači s Windows, vytvoření skupiny prostředků s virtuálními počítači v rámci předplatného Azure, připojení těchto virtuálních počítačů k doméně Azure Active Directory (AD) a registraci virtuálních počítačů pomocí virtuálního počítače s Windows.

## <a name="prerequisites"></a>Požadavky

Pro vytvoření fondu hostitelů musíte zadat následující parametry:

- Název bitové kopie virtuálního počítače
- Konfigurace virtuálního počítače
- Vlastnosti domény a sítě
- Vlastnosti fondu hostitelů virtuálních počítačů s Windows

Budete také muset znát následující věci:

- Kde je zdroj obrázku, který chcete použít. Je to z Galerie Azure nebo se jedná o vlastní image?
- Vaše přihlašovací údaje k doméně

Ujistěte se také, že jste zaregistrovali poskytovatele prostředků Microsoft. DesktopVirtualization. Pokud jste to ještě neudělali, pokračujte na **odběry**, vyberte název vašeho předplatného a pak vyberte **poskytovatelé prostředků**. Vyhledejte DesktopVirtualization, vyberte Microsoft. DesktopVirtualization a pak vyberte zaregistrovat.

Když vytvoříte fond hostitelů virtuálních počítačů s Windows pomocí šablony Azure Resource Manager, můžete vytvořit virtuální počítač z Galerie Azure, spravované bitové kopie nebo nespravované image. Další informace o tom, jak vytvořit image virtuálních počítačů, najdete v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) a [Vytvoření spravované image zobecněného virtuálního počítače v Azure](../virtual-machines/windows/capture-image-resource.md).

Pokud ještě nemáte předplatné Azure, [Vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete postupovat podle těchto pokynů.

## <a name="begin-the-host-pool-setup-process"></a>Zahájit proces nastavení fondu hostitelů

Chcete-li začít vytvářet nový fond hostitelů:

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Pokud se přihlašujete k portálu US Gov, použijte [https://portal.azure.us/](https://portal.azure.us/) místo toho.

2. Do panelu hledání zadejte **virtuální klient Windows** a potom v části služby vyhledejte a vyberte **Windows Virtual Desktop** .

3. Na stránce Přehled **virtuálního klienta Windows** vyberte **vytvořit fond hostitelů**.

4. Na kartě **základy** vyberte správné předplatné v části Podrobnosti o projektu.

5. Vyberte **vytvořit novou** , pokud chcete vytvořit novou skupinu prostředků, nebo z rozevírací nabídky vyberte existující skupinu prostředků.

6. Zadejte jedinečný název fondu hostitelů.

7. V poli umístění vyberte oblast, ve které chcete vytvořit fond hostitelů z rozevírací nabídky.

   Oblast Azure, která je přidružená k oblastem, kterou jste vybrali, je místo, kde se budou ukládat metadata pro tento fond hostitelů a příslušné objekty. Ujistěte se, že jste vybrali oblasti v zeměpisné oblasti, do kterých chcete ukládat metadata služby.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky Azure Portal zobrazující pole umístění s vybraným Východní USAm umístěním Vedle pole je text "metadata budou uložena v Východní USA."](media/portal-location-field.png)
  
   >[!NOTE]
   > Pokud chcete vytvořit fond hostitelů v [podporované oblasti](data-locations.md) mimo nás, budete muset znovu zaregistrovat poskytovatele prostředků. Po opětovné registraci byste měli v rozevíracím seznamu pro výběr umístění zobrazit další oblasti. Přečtěte si, jak se znovu zaregistrovat v článku věnovaném řešení potíží při [vytváření fondu hostitelů](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) .

8. V části typ fondu hostitelů vyberte, jestli fond hostitelů bude **osobní** nebo **sdružený**.

    - Pokud zvolíte možnost **osobní**, v poli Typ přiřazení vyberte buď možnost **automaticky** , nebo **přímo** .

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky s rozevírací nabídkou pro pole typu přiřazení Uživatel zvolil možnost automaticky.](media/assignment-type-field.png)

9.  Pokud zvolíte **fond**, zadejte následující informace:

     - V poli **maximální počet relací** zadejte maximální počet uživatelů, které mají vyrovnávání zatížení pro jednoho hostitele relace.
     - Pro **algoritmus vyrovnávání zatížení** vyberte v závislosti na vzoru použití buď možnost šířka – první nebo hloubka – první.

       > [!div class="mx-imgBorder"]
       > ![Snímek obrazovky s polem typu přiřazení se zvolenými možnostmi ve fondu Uživatel v rozevírací nabídce pro vyrovnávání zatížení najede myší na ukazatel nad pozicí – první.](media/pooled-assignment-type.png)

10. Vyberte **Další: Virtual Machines >**.

11. Pokud jste již vytvořili virtuální počítače a chcete je použít s novým fondem hostitelů, vyberte možnost **ne**, vyberte **Další: pracovní prostor >** a přejděte do části [informace o pracovním prostoru](#workspace-information) . Pokud chcete vytvořit nové virtuální počítače a zaregistrovat je do nového fondu hostitelů, vyberte **Ano**.

Teď, když jste dokončili první část, pojďme přejít k další části procesu nastavení, kde vytvoříme virtuální počítač.

## <a name="virtual-machine-details"></a>Podrobnosti o virtuálním počítači

Teď, když máme první část, budete muset nastavit virtuální počítač.

Nastavení virtuálního počítače v rámci procesu nastavení fondu hostitelů:

1. V části **Skupina prostředků** vyberte skupinu prostředků, ve které chcete vytvořit virtuální počítače. Může se jednat o jinou skupinu prostředků než tu, kterou jste použili pro fond hostitelů.

2. Potom zadejte **předponu názvu** pro pojmenování virtuálních počítačů, které proces instalace vytvoří. Přípona bude obsahovat `-` čísla od 0.

3. Vyberte **umístění virtuálního počítače** , kde chcete vytvořit virtuální počítače. Můžou být stejné nebo odlišné od oblasti, kterou jste vybrali pro fond hostitelů.
   
4. Dále vyberte možnost dostupnosti, která nejlépe vyhovuje vašim potřebám. Další informace o tom, která možnost je pro vás nejvhodnější, najdete v tématu [Možnosti dostupnosti pro virtuální počítače v Azure](../virtual-machines/availability.md) a [Nejčastější dotazy](faq.md#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > [Snímek obrazovky s rozevírací nabídkou zóny dostupnosti Možnost zóna dostupnosti je zvýrazněna.](media/availability-zone.png)

5. Pak vyberte bitovou kopii, kterou je třeba použít k vytvoření virtuálního počítače. Můžete zvolit buď **galerii** , nebo **objekt BLOB úložiště**.

    - Pokud zvolíte položku **Galerie**, v rozevírací nabídce vyberte jednu z doporučených imagí:

      - Windows 10 Enterprise s více relacemi verze 1909
      - Windows 10 Enterprise multi-session verze 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise s více relacemi verze 2004
      - Windows 10 Enterprise multi-session verze 2004 + Microsoft 365 Apps

      Pokud nevidíte požadovaný obrázek, vyberte **Zobrazit všechny obrázky**, které vám umožní vybrat v galerii jiný obrázek nebo obrázek poskytnutý Microsoftem a jinými vydavateli. Ujistěte se, že je zvolená bitová kopie jednou z [podporovaných imagí operačního systému](overview.md#supported-virtual-machine-os-images).

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky Marketplace se seznamem obrázků ze společnosti Microsoft zobrazených.](media/marketplace-images.png)

      Můžete také přejít na **Moje položky** a zvolit si vlastní image, kterou jste už nahráli.

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky s kartou Moje položky](media/my-items.png)

    - Pokud zvolíte **objekt BLOB úložiště**, můžete použít vlastní sestavení Image pomocí technologie Hyper-V nebo na virtuálním počítači Azure. Stačí zadat umístění obrázku v objektu BLOB úložiště jako identifikátor URI.
   
   Umístění obrázku je nezávislé na možnosti dostupnosti, ale odolnost zóny obrázku určuje, jestli se tato image dá použít se zónou dostupnosti. Pokud při vytváření image vyberete zónu dostupnosti, ujistěte se, že používáte image z galerie s povolenou odolností zóny. Další informace o možnosti odolnosti zóny, kterou byste měli použít, najdete [v nejčastějších dotazech](faq.md#which-availability-option-is-best-for-me).

6. Pak vyberte **Velikost virtuálního počítače** , kterou chcete použít. Můžete ponechat výchozí velikost tak, jak je, nebo vybrat **změnit velikost** pro změnu velikosti. Pokud vyberete možnost **změnit velikost**, v zobrazeném okně zvolte velikost virtuálního počítače, který je vhodný pro vaše zatížení.

7. V části **počet virtuálních počítačů** zadejte počet virtuálních počítačů, které chcete pro fond hostitelů vytvořit.

    >[!NOTE]
    >Proces instalace může během nastavování fondu hostitelů vytvořit až 400 virtuálních počítačů a každý proces nastavení virtuálního počítače vytvoří ve vaší skupině prostředků čtyři objekty. Vzhledem k tomu, že proces vytváření nekontroluje kvótu předplatného, ujistěte se, že zadaný počet virtuálních počítačů je v rámci virtuálních počítačů Azure a omezení rozhraní API pro skupinu prostředků a předplatné. Po dokončení vytváření fondu hostitelů můžete přidat další virtuální počítače.

8. Vyberte, jaké disky s operačním systémem chcete použít pro vaše virtuální počítače: SSD úrovně Standard, SSD úrovně Premium nebo HDD úrovně Standard.

9. V části síť a zabezpečení vyberte **virtuální síť** a **podsíť** , kam chcete umístit virtuální počítače, které vytvoříte. Ujistěte se, že se virtuální síť může připojit k řadiči domény, protože budete muset připojit virtuální počítače uvnitř virtuální sítě k doméně. Servery DNS virtuální sítě, které jste vybrali, by měly být nakonfigurované tak, aby používaly IP adresu řadiče domény.

10. Vyberte druh skupiny zabezpečení, který chcete: **Basic**, **Advanced** nebo **none**.

    Pokud vyberete **základní**, budete muset vybrat, jestli chcete otevřít libovolný port pro příchozí spojení. Pokud vyberete **Ano**, zvolte ze seznamu standardních portů, pro které chcete povolit příchozí připojení.

    >[!NOTE]
    >Pro zvýšení zabezpečení doporučujeme, abyste neotevřeli veřejné příchozí porty.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou skupiny zabezpečení, která zobrazuje seznam dostupných portů v rozevírací nabídce.](media/available-ports.png)

    Pokud zvolíte možnost **Upřesnit**, vyberte existující skupinu zabezpečení sítě, kterou jste již nakonfigurovali.

11. Pak vyberte, jestli chcete virtuální počítače připojit k určité doméně a organizační jednotce. Pokud zvolíte **Ano**, zadejte doménu, ke které se chcete připojit. Volitelně můžete přidat konkrétní organizační jednotku, ve které chcete virtuální počítače. Pokud zvolíte **ne**, virtuální počítače budou připojené k doméně, která odpovídá příponě **hlavního názvu uživatele (UPN) připojení k doméně AD**.

    - Když zadáte organizační jednotku, ujistěte se, že používáte úplnou cestu (rozlišující název) a bez uvozovek.

12. V části účet správce domény zadejte přihlašovací údaje správce Doména služby Active Directory virtuální sítě, kterou jste vybrali. Tento účet nemůže mít povolené vícefaktorové ověřování (MFA). Když se připojíte k doméně Azure Active Directory Domain Services (Azure služba AD DS), účet musí být součástí skupiny správců DC služby Azure AD a heslo účtu musí fungovat ve službě Azure služba AD DS.

13. Vyberte **Další: pracovní prostor >**.

V takovém případě jsme připraveni zahájit další fázi nastavení fondu hostitelů: registrace skupiny aplikací do pracovního prostoru.

## <a name="workspace-information"></a>Informace o pracovním prostoru

Proces nastavení fondu hostitelů vytvoří ve výchozím nastavení skupinu desktopových aplikací. Aby fond hostitelů fungoval podle očekávání, budete muset tuto skupinu aplikací publikovat pro uživatele nebo skupiny uživatelů a tuto skupinu aplikací musíte zaregistrovat do pracovního prostoru.

Chcete-li zaregistrovat skupinu desktopových aplikací do pracovního prostoru:

1. Vyberte **Ano**.

   Pokud vyberete **ne**, můžete skupinu aplikací zaregistrovat později, ale doporučujeme vám, abyste měli k dispozici registraci v pracovním prostoru, jak to bude mít váš fond hostitelů fungovat správně.

2. Dále zvolte, zda chcete vytvořit nový pracovní prostor, nebo vybrat z existujících pracovních prostorů. Registrovat skupinu aplikací můžou pouze pracovní prostory vytvořené ve stejném umístění, ve kterém bude fond hostitelů povolen.

3. Volitelně můžete vybrat **Další: značky >**.

    Tady můžete přidat značky, abyste mohli objekty s metadaty seskupovat, aby bylo snazší pro vaše správce.

4. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

     >[!NOTE]
     >Proces kontroly a vytváření ověření nekontroluje, jestli vaše heslo splňuje standardy zabezpečení, nebo jestli je vaše architektura správná, takže budete muset vyhledat jakékoli problémy s některou z těchto věcí sami.

5. Zkontrolujte informace o nasazení a ujistěte se, že vše vypadá správně. Po dokončení vyberte **Vytvořit**. Tím se spustí proces nasazení, který vytvoří následující objekty:

     - Váš nový fond hostitelů.
     - Skupina desktopových aplikací.
     - Pracovní prostor, pokud jste se rozhodli ho vytvořit.
     - Pokud se rozhodnete zaregistrovat skupinu desktopových aplikací, registrace se dokončí.
     - Virtuální počítače (Pokud jste se rozhodli vytvořit), které jsou připojené k doméně a zaregistrované v novém fondu hostitelů.
     - Odkaz ke stažení pro šablonu správy prostředků Azure na základě vaší konfigurace.

Pak jste hotovi!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Spuštění šablony Azure Resource Manager pro zřízení nového fondu hostitelů

Pokud místo toho chcete použít automatizovaný proces, [Stáhněte si naši šablonu Azure Resource Manager](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) , abyste místo toho zřídili nový fond hostitelů.

>[!NOTE]
>Pokud k sestavení vašeho prostředí používáte automatizovaný proces, budete potřebovat nejnovější verzi konfiguračního souboru JSON. Soubor JSON můžete najít [tady](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů, můžete ho naplnit pomocí vzdálených aplikací RemoteApp. Další informace o tom, jak spravovat aplikace na virtuálním počítači s Windows, najdete v našem dalším kurzu:

> [!div class="nextstepaction"]
> [Kurz správy skupin aplikací](./manage-app-groups.md)
