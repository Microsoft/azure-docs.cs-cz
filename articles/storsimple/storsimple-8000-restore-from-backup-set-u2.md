---
title: Obnovit ze zálohy na řadu StorSimple 8000 svazek | Dokumentace Microsoftu
description: Vysvětluje, jak obnovit svazek StorSimple ze zálohovacího skladu pomocí služby Správce zařízení StorSimple katalog záloh.
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
ms.openlocfilehash: 57290971185d1fd64b3b5ca6ac37535070ddab9e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496478"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Obnovení svazku StorSimple ze zálohovacího skladu

## <a name="overview"></a>Přehled

Tento kurz popisuje operace obnovení provést na zařízení řady StorSimple 8000 pomocí existujícího zálohovacího skladu. Použití **katalog zálohování** okna obnovení svazku z místního nebo zálohování do cloudu. **Katalog zálohování** okně se zobrazí všechny zálohovací sklady, které jsou vytvořeny při ruční nebo automatické zálohy jsou prováděny. Operace obnovení ze zálohovacího skladu připojí svazek online hned, zatímco data se stáhne na pozadí.

Alternativní způsob spuštění obnovení, je přejít na **zařízení > [zařízení] > svazky**. V **svazky** okno, vyberte svazek, kliknutím pravým tlačítkem myši vyvolejte místní nabídku a zvolte **obnovení**.

## <a name="before-you-restore"></a>Před obnovením

Před zahájením obnovení, přečtěte si následující upozornění:

* **Svazek musíte převést do režimu offline** – trvat svazek do režimu offline na tomto hostiteli a zařízení, před spuštěním operace obnovení. I když operace obnovení automaticky připojí svazek online na zařízení, musíte převést zařízení online ručně na hostiteli. Jakmile svazek je online na zařízení je možné přenést online hlasitosti na hostiteli. (Nepotřebujete čekat na dokončení operace obnovení.) Postupy najdete v části [svazek převést do režimu offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ svazku po obnovení** – odstraněné svazky jsou obnoveny podle typu ve snímku; to znamená, svazky, které byly místně připnuté se obnoví jako místně připojené svazky a svazky, které byly vrstvené se obnoví jako vrstvené svazky.

    Pro existující svazky přepíše aktuální typ použití svazku typ, který je uložený do snímku. Například pokud svazek obnovit ze snímku, který se provede, když byl vrstveného svazku typ a typ svazku je teď místně připnuté (kvůli operaci převodu, která byla provedena), pak svazek se obnoví jako místně vázaný svazek. Podobně pokud existující místně vázaný svazek byl rozšířit a následně obnovit ze starší snímku provedená v případě, že svazek je menší, obnovené svazku zachová aktuální rozbalenou velikost.

    Svazek nelze převést z vrstveného svazku pro místně vázaný svazek nebo z místně vázaný svazek vrstvený svazek, když obnovujete svazek. Počkejte na dokončení operace obnovení, a převeďte svazek do jiného typu. Informace o převodu svazku, přejděte na [změnu typu svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Velikost svazku se projeví v obnovené svazku** – to je důležitým aspektem, pokud obnovujete místně vázaný svazek, který je odstraněný (protože místně připojené svazky jsou plně zřízený). Ujistěte se, že máte dostatek místa, než se pokusíte obnovit místně vázaný svazek, který byl dříve odstraněn.

* **Nelze rozšířit svazek, zatímco se obnovuje** – počkejte na dokončení operace obnovení před pokusem o rozšíření svazku. Informace o rozšiřování svazku, přejděte na [upravit svazek](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Můžete provést zálohu, zatímco obnovit místní svazek** – postupy najdete [Správa zásad zálohování pomocí služby Správce zařízení StorSimple](storsimple-8000-manage-backup-policies-u2.md).

* **Můžete je zrušit operaci obnovení** – v případě zrušení úlohy obnovení, pak svazek bude vrácena zpět do stavu, který byl před zahájením operace obnovení. Postupy najdete v části [zrušení úlohy](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak obnovit práci

Pro zařízení s verzí Update 4 nebo novější je implementováno obnovení na základě heatmapu. Hostitele žádostí o přístup k datům spojit se zařízením, tyto požadavky jsou sledovány a je vytvořen heatmapu. Frekvence požadavků vysokou bloků dat s vyšší heat způsobí, že nižší frekvence požadavků se přeloží na bloky dat s nižšími heat. Musí přístup k datům alespoň dvakrát být označený jako _horké_. Soubor, který je upraven je také označena jako _horké_. Po zahájení obnovení proaktivní dosazení dat dochází podle Heat mapě. Pro verze starší než Update 4, data se stáhne během obnovení založené na pouze přístup.

Následující upozornění se vztahují na základě heatmapu obnovení:

* Sledování Heatmapu je povoleno pouze vrstvené svazky a místně připojené svazky nepodporují.

* Při klonování svazku na jiné zařízení, na základě Heatmapu obnovení se nepodporuje. 

* Pokud je obnovení místně a v zařízení existuje místní snímek pro svazek pro obnovení, pak jsme není dosazení (protože data jsou již k dispozici místně). 

* Ve výchozím nastavení při obnovování, dosazování úlohy, které jsou spuštěny, které proaktivně dosazení dat založené na Heat mapě. 

V aktualizaci 4 rutin prostředí Windows PowerShell slouží k dotazování spuštěné úlohy dosazování dosazování úlohu zrušit a získat stav úlohy dosazování.

* `Get-HcsRehydrationJob` – Tato rutina načte stav úlohy dosazování. Dosazování jedné úlohy se aktivuje pro jeden svazek.

* `Set-HcsRehydrationJob` – Tato rutina umožňuje pozastavit, zastavit, pokud probíhá dosazování umožňuje pokračovat v úloze dosazování.

Další informace o rutinách dosazování, přejděte na [Reference k rutinám Windows Powershellu pro StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

S automatickou dosazování obvykle vyšší přechodné výkonu při čtení se očekává. Skutečná velikost vylepšení závisí na různých faktorech, jako je například přístup k nim, četnost změn dat a datovým typem. 

Pokud chcete zrušit úlohu dosazování, můžete použít rutinu prostředí PowerShell. Pokud chcete trvale zakázat dosazování úlohy pro všechny budoucí obnovení [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Použití katalogu záloh

**Katalog záloh** okno obsahuje dotaz, který umožňuje zúžit zálohování nastavte výběr. Můžete filtrovat zálohovacích skladů, které jsou načteny na základě následujících parametrů:

* **Časový rozsah** – rozsah data a času při vytvoření zálohovacího skladu.
* **Zařízení** – zařízení, na kterém byl zálohovací sklad vytvořen.
* **Zásady zálohování** nebo **svazku** – zásady zálohování nebo svazku přidruženém k tohoto zálohovacího skladu.

Filtrované sady záloh jsou pak uvedených na základě následujících atributů:

* **Název** – název zásady zálohování nebo svazku přidruženém k zálohovacího skladu.
* **Typ** – zálohovací sklady může být místní snímky nebo cloudových snímků. Místní snímek je zálohu všechna data svazku ukládají místně na zařízení, zatímco odkazuje snímek v cloudu k zálohování dat svazků umístěných v cloudu. Místní snímky poskytují rychlejší přístup, že cloudové snímky jsou zvolena větší odolnost dat.
* **Velikost** – skutečná velikost zálohovacího skladu.
* **Vytvořit na** – datum a čas, kdy byly vytvořeny zálohy. 
* **Svazky** – počet svazků, které jsou přidružené k zálohovacího skladu.
* **Zahájené** – zálohy, nepůjdou automaticky podle plánu nebo ručně uživatelem. (Zásady zálohování můžete použít k naplánování zálohování. Alternativně můžete použít **vytvořit zálohu** možnost vytvořit interaktivní nebo na vyžádání zálohu.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Postup při obnovení svazku StorSimple ze zálohy

Můžete použít **katalog záloh** okno při obnovení svazku StorSimple ze konkrétní zálohy. Mějte na paměti, ale, obnovení svazku místo něj se uplatní svazku do stavu, ve kterém byla, když bylo provedeno zálohování. Všechna data, která byla přidána po zálohování se ztratí.

> [!WARNING]
> Obnovení ze zálohy nahradí existující svazky ze zálohy. To může způsobit ztrátu všechna data, která byla zapsána po bylo provedeno zálohování.


### <a name="to-restore-your-volume"></a>Chcete-li obnovit svazek
1. Přejděte do služby Správce zařízení StorSimple a potom klikněte na tlačítko **katalog zálohování**.

2. Vyberte zálohu, nastavte následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. V rozevíracím seznamu vyberte svazek nebo zálohování zásadu pro zálohu, kterou chcete vybrat.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.

    Zálohy přidružené vybraný svazek nebo zásady zálohování by se měla zobrazit v seznamu sad záloh.
   
    ![Zálohovací sklad seznamu](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozbalte zálohovacího skladu zobrazíte přidružené svazky. Tyto svazky musí být převedeno do režimu offline v hostiteli a zařízení, než bude možné obnovit. Přístup ke svazkům na **svazky** okno vašeho zařízení a pak postupujte podle kroků v [svazek převést do režimu offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) jejich uvedení do režimu offline.
   
   > [!IMPORTANT]
   > Ujistěte se, že jste provedli svazky na hostiteli nejprve před uveďte svazky do režimu offline v zařízení. Pokud neprovedete svazky na hostiteli, může potenciálně vést k poškození.
   
4. Přejděte zpět **katalog záloh** kartě a vyberte sadu záloh. Klikněte pravým tlačítkem a pak v místní nabídce vyberte **obnovení**.

    ![Zálohovací sklad seznamu](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Zobrazí se výzva k potvrzení. Zkontrolovat informace o obnovení a pak zaškrtněte políčko potvrzení.
   
    ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Klikněte na tlačítko **obnovení**. Tím se spustí úlohy obnovení, které můžete zobrazit přechodem k **úlohy** stránky.

    ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po dokončení obnovení ověřte, že obsah svazky jsou nahrazené svazků ze zálohy.


## <a name="if-the-restore-fails"></a>Pokud se nezdaří obnovení

Pokud z nějakého důvodu selže operace obnovení, dostanete upozornění. Pokud k tomu dojde, obnovte záložní seznam k ověření, že je záloha stále platná. Pokud je záloha platná a provádíte obnovení z cloudu, pak problémy s připojením může být příčinou problému.

K dokončení operace obnovení provést offline svazek na hostiteli a opakujte operaci obnovení. Všimněte si, že všechny úpravy dat svazku, které byly provedeny během procesu obnovení budou ztraceny.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [svazky zařízení StorSimple spravovat](storsimple-8000-manage-volumes-u2.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

