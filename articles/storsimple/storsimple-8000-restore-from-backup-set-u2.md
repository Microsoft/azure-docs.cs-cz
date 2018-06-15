---
title: Obnovit svazek ze zálohy na řadu zařízení StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak používat službu StorSimple Manager zařízení katalogu zálohy obnovit svazek StorSimple ze zálohovacího skladu.
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
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875456"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Obnovit svazek StorSimple ze zálohovacího skladu

## <a name="overview"></a>Přehled

Tento kurz popisuje operaci obnovení provést na zařízení řady StorSimple 8000 pomocí existujícího zálohovacího skladu. Použití **katalog zálohování** okno obnovení svazku z místní nebo cloudové zálohování. **Katalog zálohování** zobrazuje všechny zálohovací sklady, které vytvářejí, když jsou provedeny ruční nebo automatické zálohy. Operace obnovení ze zálohovacího skladu připojí svazek online okamžitě, když data se stáhne na pozadí.

Alternativní metoda zahájíte obnovení je přejít na **zařízení > [zařízení] > svazky**. V **svazky** okně, vyberte svazek, klikněte pravým tlačítkem na vyvolání v místní nabídce a potom vyberte **obnovení**.

## <a name="before-you-restore"></a>Před obnovením

Před zahájením obnovení, přečtěte si následující upozornění:

* **Je nutné provést svazek offline** – trvat svazek offline na hostiteli a zařízení, před spuštěním operace obnovení. Přestože operace obnovení automaticky připojí svazek online na zařízení, je nutné ručně přenést zařízení online na hostiteli. Převedete online svazek na hostiteli, jakmile je svazek online na zařízení. (Není nutné čekat, až po dokončení operace obnovení.) Postupy, přejděte na [do offline režimu svazku](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ svazku po obnovení** – odstraněné svazky jsou obnoveny podle typu ve snímku; to znamená, svazky, které byly místně vázaný se obnoví jako místně vázaných svazků a svazky, které byly zřízeny vrstvené se obnoví jako vrstvené svazky.

    Pro existující svazky přepíše aktuální typ použití svazku typ, který je uložený ve snímku. Například pokud obnovujete svazek ze snímku, která se provede, když byla vrstvené typ svazku a typ svazku je nyní místně vázaný (z důvodu převodu operace, která byla provedena), pak svazek se obnoví jako místně vázaný svazek. Podobně pokud existující místně vázaný svazek byla rozšířena a následně obnovit ze starší snímek pořízený při menší svazek, obnovený svazek bude zachovat aktuální rozbalenou velikost.

    Svazek nelze převést z vrstvený svazek k místně vázaný svazek nebo z místně vázaný svazek k vrstvený svazek, při obnovení svazku. Počkejte na dokončení operace obnovení, a pak můžete převést svazek k jinému typu. Informace o převodu svazku, přejděte na [změnit typ svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Velikost svazku se odrazí v obnovené svazku** – Toto je důležitý faktor, pokud obnovujete místně vázaný svazek, který byl odstraněn, (protože místně vázaných svazků jsou plně zřízený). Ujistěte se, že máte dostatek místa, před dalším pokusem o obnovení místně vázaný svazek, který byl dříve odstraněn.

* **Nelze rozbalit svazku, zatímco je obnovena** – počkejte na dokončení operace obnovení před dalším pokusem o rozšířit svazek. Informace o rozšiřování svazek, přejděte na [upravit svazek](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Při obnovení místního svazku můžete provést zálohu** – postupy naleznete na [použít službu StorSimple Manager zařízení ke správě zásady zálohování](storsimple-8000-manage-backup-policies-u2.md).

* **Můžete zrušit operaci obnovení** – Pokud zrušíte úlohy obnovení, pak svazek bude vrácena zpět do stavu, který byl předtím, než jste spustili operaci obnovení. Postupy, přejděte na [zrušení úlohy](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak obnovit práci

Pro zařízení se systémem Update 4 nebo novější se implementuje na základě heatmap obnovení. Jako hostitele požadavky na přístup k datům přístup zařízení, tyto požadavky jsou sledovány a k vytvoření heatmap. Rychlost vysokou požadavků výsledkem bloky dat s vyšší heat, zatímco překládá nižší rychlost požadavků na bloky dat s nižší heat. Je nutné získat přístup dat alespoň dvakrát na označit jako _aktivní_. Soubor, který se mění je také označena jako _aktivní_. Jakmile zahájíte obnovení, nastane proaktivní dosazováním dat podle heatmap. Verze starší než aktualizace 4, data se stáhne během obnovení založená na přístupu jen.

Zohledněním těchto aspektů použít na základě heatmap obnovení:

* Sledování Heatmap je povolená jenom pro vrstvené svazky a místně vázaných svazků nejsou podporovány.

* Obnovení na základě Heatmap není podporováno při klonování svazku na jiné zařízení. 

* Pokud dojde obnovení na místě a místní snímek pro svazek, který se má obnovit v zařízení existuje, pak jsme není rehydrataci při spotřebě (protože data je již k dispozici místně). 

* Ve výchozím nastavení Pokud obnovujete, rehydrataci úlohy se spouští které proaktivně rehydrataci při spotřebě dat podle heatmap. 

V aktualizaci 4 lze použít rutiny prostředí Windows PowerShell pro dotazování spuštěné úlohy rehydrataci, zrušení úlohy rehydrataci a získat stav úlohy rehydrataci.

* `Get-HcsRehydrationJob`– Tato rutina načte stav úlohy rehydrataci. Aktivuje úlohu jeden rehydrataci se pro jeden svazek.

* `Set-HcsRehydrationJob`– Tato rutina umožňuje pozastavit, zastavit, obnovte úlohu rehydrataci, když probíhá rehydrataci.

Další informace o rutinách rehydrataci, přejděte na [odkazu na rutiny Windows Powershellu pro StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

S automatickou rehdyration obvykle vyšší výkon přechodný pro čtení se očekává. Skutečné magniutde vylepšení závisí na různých faktorech, jako je například vzor přístupu, mísení dat a typu dat. 

Pokud chcete zrušit úlohu rehydrataci, můžete použít rutinu prostředí PowerShell. Pokud chcete trvale zakázat rehydrataci úloh pro všechny budoucí obnovení [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Použití zálohování katalogu

**Zálohování katalogu** okno obsahuje dotaz, který umožňuje zúžit zálohování nastavte výběr. Můžete filtrovat zálohovací sklady, které jsou načteny na základě následujících parametrů:

* **Čas rozsah** – rozsah data a času v okamžiku vytvoření zálohovacího skladu.
* **Zařízení** – zařízení, v němž byla vytvořena zálohovacího skladu.
* **Zásady zálohování** nebo **svazku** – zásady zálohování nebo svazku přidruženém k tohoto zálohovacího skladu.

Filtrované zálohovací sklady jsou pak poskytovalo na základě následujících atributů:

* **Název** – název zásady zálohování nebo svazku přidruženém k zálohovacího skladu.
* **Typ** – zálohovací sklady může být místní snímky nebo cloudových snímků. Místní snímek je zálohování všech dat uložených místně na zařízení, svazku, zatímco cloudový snímek odkazuje na zálohování svazku dat umístěných v cloudu. Místní snímky poskytují rychlejší přístup, že jsou pro záleží na odolnosti dat zvolena cloudových snímků.
* **Velikost** – skutečná velikost zálohovacího skladu.
* **Vytvořit v** – datum a čas, kdy byly vytvořeny zálohy. 
* **Svazky** -počet svazků, které jsou přidružené k zálohovacího skladu.
* **Iniciované** – zálohy lze inicializovat automaticky podle plánu nebo ručně uživatelem. (Můžete použít zásady zálohování naplánovat zálohování. Alternativně můžete použít **provést zálohování** možnost provést zálohu interaktivní nebo na vyžádání.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Jak obnovit svazek StorSimple ze zálohy

Můžete použít **zálohování katalogu** okno obnovit svazek StorSimple ze konkrétní zálohy. Mějte na paměti, ale, obnovení svazku vrátíte svazku stavu, ve kterém byl v době vytvoření zálohy. Žádná data, která byla přidána po operace zálohování se ztratí.

> [!WARNING]
> Obnovení ze zálohy nahradí existující svazky ze zálohy. To může způsobit ztrátu všechna data, která byla zapsána po vytvoření zálohy.


### <a name="to-restore-your-volume"></a>K obnovení svazku
1. Přejděte do služby StorSimple Manager zařízení a pak klikněte na tlačítko **katalog zálohování**.

2. Vyberte zálohovací sklad následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. V rozevíracím seznamu vyberte svazek nebo zálohování zásady pro zálohu, kterou chcete vybrat.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.

    Zálohování přidružené k vybranému svazku nebo zásady zálohování by se měla objevit v seznamu sad záloh.
   
    ![Zálohovací sklad seznamu](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozbalte zálohovací sklad zobrazíte přidružené svazky. Tyto svazky musí být převedeno do režimu offline v hostiteli a zařízení, než bude možné obnovit. Přístup ke svazkům na **svazky** okno vašeho zařízení a pak postupujte podle kroků v [do offline režimu svazek](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) je uvedení do režimu offline.
   
   > [!IMPORTANT]
   > Ujistěte se, že jste provedli svazky do offline režimu na hostiteli nejprve, před provedením svazky do režimu offline v zařízení. Pokud neprovedete offline svazky na hostiteli, může potenciálně vést k poškození dat.
   
4. Přejděte zpět **zálohování katalogu** a vyberte zálohovacího skladu. Klikněte pravým tlačítkem myši a potom v místní nabídce vyberte **obnovení**.

    ![Zálohovací sklad seznamu](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Zobrazí se výzva k potvrzení. Zkontrolovat informace o obnovení a pak zaškrtněte políčko potvrzení.
   
    ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Klikněte na tlačítko **obnovení**. Tím se vyvolá úloha obnovení, který si můžete zobrazit přístup k **úlohy** stránky.

    ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po dokončení obnovení ověřte, že obsah svazků jsou nahrazovány svazků ze zálohy.


## <a name="if-the-restore-fails"></a>Pokud se nezdaří obnovení

Obdržíte výstrahu, pokud z nějakého důvodu selže operace obnovení. Pokud k tomu dojde, aktualizujte seznamu zálohování a ověřte, zda zálohování je stále platný. Pokud záloha není platná a zda obnovujete z cloudu, pak problémy s připojením k může být příčinou problému.

Chcete-li dokončit operaci obnovení, provést offline svazek na hostiteli a opakujte operaci obnovení. Všimněte si, že veškeré úpravy data na svazku, které byly provedeny během procesu obnovení budou ztraceny.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [svazky spravovat zařízení StorSimple](storsimple-8000-manage-volumes-u2.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

