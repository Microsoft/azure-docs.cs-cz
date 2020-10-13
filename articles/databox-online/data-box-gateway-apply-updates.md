---
title: Instalace aktualizace na zařízení s Azure Data Box Gateway Series | Microsoft Docs
description: Popisuje, jak použít aktualizace pomocí Azure Portal a místního webového uživatelského rozhraní pro zařízení Azure Data Box Gateway Series.
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: alkohli
ms.openlocfilehash: 337c0c4434eb768ee45429d9b2d23536db4c3fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575448"
---
# <a name="update-your-azure-data-box-gateway"></a>Aktualizace Azure Data Box Gateway

Tento článek popisuje kroky potřebné k instalaci aktualizace do Azure Data Box Gateway prostřednictvím místního webového uživatelského rozhraní a prostřednictvím Azure Portal. Aktualizace softwaru a opravy hotfix můžete použít, chcete-li udržovat Data Box Gateway zařízení v aktuálním stavu.

> [!IMPORTANT]
>
> - Aktualizace **1911** odpovídá verzi **1.6.1049.786** softwaru na vašem zařízení. Další informace o této aktualizaci najdete v [poznámkách k verzi](data-box-gateway-1911-release-notes.md).
>
> - Mějte na paměti, že při instalaci aktualizace nebo opravy hotfix dojde k restartování zařízení. Vzhledem k tomu, že Data Box Gateway je zařízení s jedním uzlem, při aktualizaci softwaru zařízení se přeruší všechny probíhající vstupně-výstupní operace a dojde k až 30minutovému výpadku zařízení.

Každý z těchto kroků je popsaný v následujících částech.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Zařízení automaticky vyhledává aktualizace jednou denně. Až budou aktualizace k dispozici, zobrazí se na portálu oznámení. Pak můžete stáhnout a nainstalovat aktualizace.

> [!NOTE]
> Než budete pokračovat v instalaci aktualizací, ujistěte se, že je zařízení v pořádku a zobrazuje se stav **online** .

1. Až budou aktualizace k dispozici pro vaše zařízení, zobrazí se oznámení. Vyberte oznámení nebo na horním panelu příkazů **aktualizujte zařízení**. To vám umožní použít aktualizace softwaru zařízení.

    ![Snímek obrazovky Azure Stack Edge Data Box Gateway domovské stránce s přehledem a aktualizujte možnosti zařízení s názvem.](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. V okně **aktualizace zařízení** ověřte, že jste si přečetli licenční smlouvy spojené s novými funkcemi v poznámkách k verzi.

    Můžete si **Stáhnout a nainstalovat** aktualizace nebo **Stáhnout** aktualizace. Tyto aktualizace pak můžete nainstalovat později.

    ![Snímek obrazovky dialogového okna aktualizace zařízení s možnostmi přijmout a porozumět a možností stažení a instalace s názvem.](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Pokud chcete stáhnout a nainstalovat aktualizace, ověřte možnost, že se aktualizace po dokončení stahování automaticky nainstalují.

    ![Snímek obrazovky dialogového okna aktualizace zařízení s možností přijmout a možností stažení s názvem.](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. Spustí se stahování aktualizací. Zobrazí se oznámení o tom, že probíhá stahování.

    ![Snímek obrazovky s oznámením, že probíhá stahování a instalace aktualizací.](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    V Azure Portal se zobrazí také nápis s oznámením. To indikuje průběh stahování.

    ![Snímek obrazovky Azure Stack Edge Data Box Gateway domovské stránce s možností přehled a oznámení o stahování aktualizací se vyvolala.](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Můžete vybrat toto oznámení nebo vybrat **aktualizovat zařízení** a zobrazit podrobný stav aktualizace.

    ![Snímek obrazovky dialogového okna stáhnout a nainstalovat aktualizace se zprávou stav: probíhá zpráva a zpráva stáhnout aktualizace se vyvolala.](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. Po dokončení stahování se banner oznámení aktualizuje, aby označoval dokončení. Pokud jste se rozhodli stáhnout a nainstalovat aktualizace, bude instalace automaticky zahájena.

    ![Snímek obrazovky Azure Stack Edge Data Box Gateway domovské stránce s možností přehled a staženým nápisem oznámení o aktualizacích.](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Pokud se rozhodnete stahovat pouze aktualizace, vyberte oznámení a otevřete okno **aktualizace zařízení** . Vyberte **Nainstalovat**.
  
    ![Snímek obrazovky dialogového okna aktualizace zařízení s možností instalovat s názvem.](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Zobrazí se oznámení o tom, že probíhá instalace.

    ![Snímek obrazovky Azure Stack Edge Data Box Gateway domovské stránce s možností přehled a zprávou o průběhu stahování a instalace se vyvolala.](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    Portál také zobrazí informační výstrahu, která indikuje, že instalace probíhá. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Vzhledem k tomu, že se jedná o zařízení s jedním uzlem, po instalaci aktualizací se zařízení restartuje. Kritická výstraha při restartování bude znamenat, že dojde ke ztrátě prezenčního signálu zařízení.

    ![Snímek obrazovky Azure Stack Edge Data Box Gateway domovské stránce s možností přehled a kritickou hlavičkou oznámení výstrah.](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Vyberte výstrahu, aby se zobrazila odpovídající událost zařízení.

    ![Snímek obrazovky oddílu události zařízení s ztraceným prezenčním signálem události zařízení, která se vyvolala](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. Po instalaci aktualizací se stav zařízení aktualizuje na **online** .

    ![Snímek obrazovky Azure Stack Edge Data Box Gateway domovské stránce s možností přehled a online stavem s názvem.](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    V horním panelu příkazů vyberte **aktualizace zařízení**. Ověřte, že se aktualizace úspěšně nainstalovala a že verze softwaru zařízení bude odpovídat.

    ![Snímek obrazovky dialogového okna aktualizace zařízení s nainstalovanou verzí softwaru s názvem.](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

- Stáhnout aktualizaci nebo opravu hotfix
- Instalace aktualizace nebo opravy hotfix

Každý z těchto kroků je podrobně popsán v následujících částech.

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Chcete-li stáhnout aktualizaci, proveďte následující kroky. Aktualizaci si můžete stáhnout z umístění dodaného společností Microsoft nebo z katalogu Microsoft Update.

Chcete-li stáhnout aktualizaci z katalogu Microsoft Update, proveďte následující kroky.

1. Spusťte prohlížeč a přejděte na adresu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Snímek obrazovky katalogu Microsoft Update v okně prohlížeče se Data Box Gateway zadaným do textového pole hledání V adresním řádku prohlížeče a v textovém poli hledání se zavolá.](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo opravy hotfix nebo podmínek pro aktualizaci, kterou chcete stáhnout, do znalostní báze (KB). Zadejte například **Azure Data box Gateway**a pak klikněte na **Hledat**.

   Výpis aktualizace se zobrazí jako **Azure Data Box Gateway 1911**.

   ![Snímek obrazovky katalogu Microsoft Update v okně prohlížeče se zobrazenými výsledky hledání Data Box Gateway a voláním.](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Vyberte **Stáhnout**. Existuje jeden soubor ke stažení s názvem *SoftwareUpdatePackage.exe* , který odpovídá aktualizaci softwaru zařízení. Stáhněte soubor do složky v místním systému. Můžete také zkopírovat složku do síťové sdílené složky, která je dosažitelná ze zařízení.

   ![Snímek obrazovky dialogového okna pro stažení s odkazem na balíček aktualizace softwaru dot E X E s názvem.](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo oprav hotfix se ujistěte, že:

- Máte aktualizaci nebo opravu hotfix staženou místně na svém hostiteli nebo přístupnou přes sdílenou síťovou složku.
- Stav zařízení je v pořádku, jak je znázorněno na stránce **Přehled** místního webového uživatelského rozhraní.

   ![Snímek obrazovky Data Box Gateway místní web U I s možností řídicího panelu a stavem softwaru: v pořádku zpráva byla vyvolána zpráva.](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Dokončení tohoto postupu trvá přibližně 20 minut. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

1. V místním webovém uživatelském rozhraní přejdete na **Údržba**  >  **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte.

   ![Snímek obrazovky Data Box Gateway místní web U I s možností aktualizace softwaru a aktuální zprávou o verzi softwaru vyvolanou.](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Zadejte cestu k souboru aktualizace. Můžete také přejít k instalačnímu souboru aktualizace, pokud je umístěn ve sdílené síťové složce. Vyberte soubor aktualizace softwaru s příponou *SoftwareUpdatePackage.exe* .

   ![Snímek obrazovky Průzkumníka souborů se souborem aktualizace softwaru tečka E X E s názvem.](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Vyberte **Použít**.

   ![Snímek obrazovky Data Box Gateway místní web U I s možností aktualizace softwaru, v textovém poli cesta k souboru aktualizace a možnost použít aktualizaci s názvem.](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Po zobrazení výzvy k potvrzení vyberte **Ano** a pokračujte. Když je zařízení s jedním uzlem, po použití aktualizace se zařízení restartuje a dojde k výpadku.
   ![Snímek obrazovky dialogového okna aktualizace softwaru s zvýrazněnou možností Ano](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. Spustí se aktualizace. Po úspěšné aktualizaci zařízení se restartuje. Místní uživatelské rozhraní není v tuto dobu k dispozici.

6. Po dokončení restartování přejdete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, v místním webovém uživatelském rozhraní, **Maintenance**navštivte web  >  **aktualizace softwaru**údržba. Zobrazená verze softwaru v tomto příkladu je **1.6.1049.786**.

   ![Snímek obrazovky Data Box Gateway místní web U I s možností aktualizace softwaru a aktualizovanou aktuální zprávu o verzi softwaru vyvolanou.](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě Azure Data box Gateway](data-box-gateway-manage-users.md).
