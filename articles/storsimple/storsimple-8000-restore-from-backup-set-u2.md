---
title: Obnovení svazku ze zálohy v řadě StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak pomocí katalogu zálohování služby StorSimple Device Manager obnovit svazek StorSimple ze zálohovací sady.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723335"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Obnovení svazku StorSimple ze zálohovacího skladu

## <a name="overview"></a>Přehled

Tento kurz popisuje operaci obnovení provedenou na zařízení řady StorSimple 8000 pomocí existující zálohovací sady. Okno **Katalog zálohování** slouží k obnovení svazku z místní nebo cloudové zálohy. Okno **katalogu zálohování** zobrazuje všechny zálohovací sady, které jsou vytvořeny při ručním nebo automatickém zálohování. Operace obnovení ze zálohovací sady přenese svazek do režimu online okamžitě, zatímco data jsou stažena na pozadí.

Alternativní metodou pro spuštění obnovení je přejít na **zařízení > [Vaše zařízení] > svazky**. V okně **Svazky** vyberte svazek, klepnutím pravým tlačítkem myši vyvoláte místní nabídku a pak vyberte **příkaz Obnovit**.

## <a name="before-you-restore"></a>Před obnovením

Než začnete s obnovením, přečtěte si následující upozornění:

* **Svazek je nutné přepnout do režimu offline** – před zahájením operace obnovení přepne svazek do režimu offline na hostiteli i zařízení. Přestože operace obnovení automaticky přenese svazek do režimu online v zařízení, je nutné ručně převést zařízení do režimu online na hostiteli. Svazek můžete převést do režimu online na hostiteli, jakmile je svazek v zařízení online. (Nemusíte čekat na dokončení operace obnovení.) Postupy naleznete v části [Převést svazek offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ svazku po obnovení** – odstraněné svazky jsou obnoveny na základě typu ve snímku; to znamená, že svazky, které byly místně připnuté, jsou obnoveny jako místně připnuté svazky a svazky, které byly vrstvené, jsou obnoveny jako vrstvené svazky.

    Pro existující svazky aktuální typ použití svazku přepíše typ, který je uložen ve snímku. Pokud například obnovíte svazek ze snímku, který byl pořízen při vrstvení typu svazku a tento typ svazku je nyní místně připnutý (z důvodu operace převodu, která byla provedena), bude svazek obnoven jako místně vázaný svazek. Podobně pokud byl existující místně vázaný svazek rozbalen a následně obnoven ze staršího snímku pořízeného při menším svazku, obnovený svazek si zachová aktuální rozšířenou velikost.

    Svazek z vrstveného svazku nelze převést na místně vázaný svazek nebo z místně vázaný svazek na vrstvený svazek během obnovení svazku. Počkejte, až bude operace obnovení dokončena, a potom můžete převést svazek na jiný typ. Informace o převodu svazku naleznete v části [Změna typu svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Velikost svazku se projeví v obnoveném svazku** – To je důležité, pokud obnovujete místně vázaný svazek, který byl odstraněn (protože místně vázaných svazků jsou plně zřízeny). Ujistěte se, že máte dostatek místa před pokusem o obnovení místně vázaný svazek, který byl dříve odstraněn.

* **Svazek nelze rozbalit, když se obnovuje** – Počkejte, až bude operace obnovení dokončena, než se pokusíte svazek rozbalit. Informace o rozbalení svazku naleznete v najdete [v souboru Modify a volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Zálohování můžete provést při obnovení místního svazku** – Postupy naleznete [na: Pomocí služby Správce zařízení StorSimple můžete spravovat zásady zálohování](storsimple-8000-manage-backup-policies-u2.md).

* **Operaci obnovení můžete zrušit** – Pokud úlohu obnovení zrušíte, bude svazek vrácen zpět do stavu, ve kterého se nachází před zahájením operace obnovení. Postupy naleznete v části [Zrušení úlohy](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak funguje obnovení

Pro zařízení se spuštěnou aktualizací 4 nebo novější se implementuje obnovení založené na heatmap. Jako požadavky hostitele pro přístup k datům dosáhnout zařízení, tyto požadavky jsou sledovány a heatmap je vytvořen. Vysoká míra požadavků má za následek datové bloky s vyšším teplem, zatímco nižší míra požadavků se promítá do bloků s nižším teplem. K datům musíte přistupovat alespoň dvakrát, aby byli označeni jako _aktivní_. Soubor, který je změněn, je také označen jako _aktivní_. Jakmile zahájíte obnovení, pak proaktivní hydratace dat dochází na základě heatmap. U verzí starších než aktualizace 4 jsou data stažena během obnovení pouze na základě přístupu.

Následující upozornění platí pro obnovení založená na heatmap:

* Sledování heatmapy je povoleno pouze pro vrstvené svazky a místně připnuté svazky nejsou podporovány.

* Obnovení založené na heatmapu není podporováno při klonování svazku do jiného zařízení. 

* Pokud je na místě obnovení a místní snímek pro svazek, který má být obnoven existuje v zařízení, pak nemáme rehydratovat (jako data jsou již k dispozici místně). 

* Ve výchozím nastavení jsou při obnovení zahájeny úlohy rehydratace, které proaktivně rehydratují data na základě heatmapy. 

V aktualizaci 4 lze rutiny prostředí Windows PowerShell použít k dotazování spuštěných úloh rehydratace, zrušení úlohy rehydratace a získání stavu úlohy rehydratace.

* `Get-HcsRehydrationJob`- Tato rutina získá stav rehydratační úlohy. Pro jeden svazek se spustí jedna rehydratační úloha.

* `Set-HcsRehydrationJob`- Tato rutina umožňuje pozastavit, zastavit, pokračovat v rehydratační práci, když probíhá rehydratace.

Další informace o rutinách rehydratace naleznete v [odkazu rutiny prostředí Windows PowerShell pro StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Při automatické rehydrataci se obvykle očekává vyšší výkon přechodného čtení. Skutečná velikost vylepšení závisí na různých faktorech, jako je například přístupový vzor, změny dat a datový typ. 

Chcete-li zrušit úlohu rehydratace, můžete použít rutinu prostředí PowerShell. Pokud chcete trvale zakázat rehydratační úlohy pro všechny budoucí obnovení, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Jak používat katalog zálohování

Okno **Katalog zálohování** poskytuje dotaz, který vám pomůže zúžit výběr zálohovací sady. Zálohovací sady, které jsou načteny, můžete filtrovat na základě následujících parametrů:

* **Časový rozsah** – datum a časový rozsah při vytvoření zálohovací sady.
* **Zařízení** – zařízení, na kterém byla vytvořena zálohovací sada.
* **Zásady zálohování** nebo **svazek** – zásady zálohování nebo svazek přidružené k této zálohovací sadě.

Filtrované zálohovací sady jsou pak tabulkovány na základě následujících atributů:

* **Název** – název zásady zálohování nebo svazku přidruženého k zálohovací sadě.
* **Typ** – zálohovací sady mohou být místní snímky nebo cloudové snímky. Místní snímek je záloha všech dat svazku uložených místně v zařízení, zatímco snímek cloudu odkazuje na zálohování dat svazku s bydlištěm v cloudu. Místní snímky poskytují rychlejší přístup, zatímco cloudové snímky jsou vybrány pro odolnost proti chybám dat.
* **Velikost** – skutečná velikost zálohovací sady.
* **Vytvořeno na** – datum a čas, kdy byly vytvořeny zálohy. 
* **Svazky** - Počet svazků přidružených k zálohovací sadě.
* **Inicializováno** – zálohy mohou být inicializovány automaticky podle plánu nebo ručně uživatelem. (Zásady zálohování můžete použít k plánování zálohování. Alternativně můžete použít **take backup** možnost provést interaktivní nebo na vyžádání zálohování.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Jak obnovit svazek StorSimple ze zálohy

Okno Katalog **zálohování** můžete použít k obnovení svazku StorSimple z určité zálohy. Mějte však na paměti, že obnovení svazku vrátí svazek do stavu, ve který byl při zálohování pořízen. Všechna data, která byla přidána po operaci zálohování, budou ztracena.

> [!WARNING]
> Obnovení ze zálohy nahradí existující svazky ze zálohy. To může způsobit ztrátu všech dat, která byla zapsána po zálohování byla provedena.


### <a name="to-restore-your-volume"></a>Obnovení hlasitosti
1. Přejděte na službu Správce zařízení StorSimple a klepněte na tlačítko **Zálohovat katalog**.

2. Vyberte zálohovací sadu následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. V rozevíracím seznamu zvolte zásady svazku nebo zálohování zálohy, které chcete vybrat.
   4. Chcete-li spustit tento dotaz, klepněte na **tlačítko Použít.**

      Zálohy přidružené k vybranému svazku nebo zásady zálohování by se měly objevit v seznamu zálohovacích sad.
   
      ![Seznam zálohovacích skladeb](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozbalte zálohovací sadu a zobrazte přidružené svazky. Tyto svazky musí být přepnuty do offline na hostiteli a zařízení před jejich obnovením. Získejte přístup ke svazkům v okně **Svazky** zařízení a postupujte podle pokynů v [tématu Přepnutí svazku do offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) přepnutí do offline.
   
   > [!IMPORTANT]
   > Před přepnutím svazků do offline na zařízení se ujistěte, že jste svazky na hostiteli přeřadili do offline. Pokud neprovedete svazky offline na hostiteli, může to potenciálně vést k poškození dat.
   
4. Přejděte zpět na kartu **Katalog zálohování** a vyberte zálohovací sklad. Klepněte pravým tlačítkem myši a potom v místní nabídce vyberte **příkaz Obnovit**.

    ![Seznam zálohovacích skladeb](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Zobrazí se výzva k potvrzení. Zkontrolujte informace o obnovení a zaškrtněte políčko potvrzovací políčko.
   
    ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klepněte na tlačítko **Obnovit**. Tím se iniciuje úloha obnovení, kterou můžete zobrazit na stránce **Úlohy.**

   ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po dokončení obnovení ověřte, zda jsou obsah svazků nahrazeny svazky ze zálohy.


## <a name="if-the-restore-fails"></a>Pokud se obnovení nezdaří

Pokud se operace obnovení z nějakého důvodu nezdaří, zobrazí se výstraha. Pokud k tomu dojde, aktualizujte seznam záloh a ověřte, zda je záloha stále platná. Pokud je záloha platná a obnovujete z cloudu, mohou být problémy s připojením.

Chcete-li dokončit operaci obnovení, přepnete svazek do režimu offline na hostiteli a opakujte operaci obnovení. Všimněte si, že všechny změny dat svazku, které byly provedeny během procesu obnovení budou ztraceny.

## <a name="next-steps"></a>Další kroky
* Přečtěte [si, jak spravovat svazky StorSimple](storsimple-8000-manage-volumes-u2.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

