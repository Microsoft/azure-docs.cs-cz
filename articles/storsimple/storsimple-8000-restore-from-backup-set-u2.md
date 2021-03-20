---
title: Obnovení svazku ze zálohy v StorSimple 8000 series | Microsoft Docs
description: Vysvětluje způsob použití katalogu záloh služby StorSimple Správce zařízení k obnovení svazku StorSimple ze zálohovacího skladu.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94950723"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Obnovení svazku StorSimple ze zálohovacího skladu

## <a name="overview"></a>Přehled

V tomto kurzu se popisuje operace obnovení prováděná na zařízení řady StorSimple 8000 pomocí existujícího zálohovacího skladu. K obnovení svazku z místního nebo cloudového zálohování použijte okno **katalog záloh** . V okně **katalog záloh** se zobrazí všechny zálohovací sklady, které jsou vytvořeny při ručním nebo automatizovaném zálohování. Operace obnovení ze zálohovacího skladu přinese svazek do online režimu okamžitě při stahování dat na pozadí.

## <a name="before-you-restore"></a>Před obnovením

Než začnete s obnovením, Projděte si následující upozornění:

* **Svazek je nutné převést do režimu offline** – před zahájením operace obnovení si svazek převeďte do režimu offline na hostiteli i v zařízení. I když operace obnovení automaticky přinese do zařízení svazek online, musíte zařízení ručně převést do online režimu na hostiteli. Svazek můžete převést online na hostitele, jakmile je svazek v zařízení online. (Nemusíte čekat na dokončení operace obnovení.) Postupy získáte, když přejdete na příkaz [převést svazek do režimu offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ svazku po obnovení** – odstraněné svazky budou obnoveny na základě typu ve snímku. To znamená, že místně připnuté svazky se obnovily, protože místně připojené svazky a svazky, které byly vrstveny, se obnoví jako vrstvené svazky.

    Aktuální typ použití svazku pro existující svazky Přepisuje typ, který je uložený ve snímku. Pokud například obnovíte svazek ze snímku, který byl vytvořen při vrstvení typu svazku a tento typ svazku je nyní připojen místně (z důvodu operace převodu, která byla provedena), bude svazek obnoven jako místně připojený svazek. Podobně platí, že pokud byl existující místně připojený svazek rozbalený a následně obnoven ze staršího snímku pořízených, když byl svazek menší, obnovený svazek si uchová aktuální rozbalenou velikost.

    Svazek se nedá převést z vrstveného svazku na místně připojený svazek nebo z místně připojeného svazku na vrstvený svazek během obnovování svazku. Počkejte, než se dokončí operace obnovení, a pak můžete svazek převést na jiný typ. Informace o převodu svazku získáte tak, že přejdete na [změnit typ svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Velikost svazku se projeví v obnoveném svazku** – jedná se o důležitý aspekt, pokud obnovujete místně připojený svazek, který se odstranil (protože místně připojené svazky jsou plně zřízené). Ujistěte se, že máte dostatek místa, než se pokusíte obnovit místně připojený svazek, který byl dříve odstraněn.

* **Během obnovování svazku se nedá rozšířit** – počkejte, než se dokončí operace obnovení, než se pokusíte svazek rozšířit. Informace o rozšíření svazku získáte, když přejdete na [Upravit svazek](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Během obnovování místního svazku můžete provést zálohování** – pro postupy [použijte službu StorSimple Správce zařízení ke správě zásad zálohování](storsimple-8000-manage-backup-policies-u2.md).

* **Operaci obnovení můžete zrušit** – Pokud zrušíte úlohu obnovení, svazek se vrátí zpět do stavu, ve kterém byl předtím, než jste zahájili operaci obnovení. Postupy získáte, když přejdete na [Zrušit úlohu](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak funguje obnovení

U zařízení se systémem Update 4 nebo novějším je implementováno obnovení založené na heatmapu. V případě, že hostitel požaduje přístup k datům v zařízení, jsou tyto požadavky sledovány a vytvoří se heatmapu. Vysoká míra požadavků vede k datovým datům s vyšším objemem tepla, zatímco nižší rychlost požadavků se překládá na bloky s nižším objemem tepla. Musíte mít přístup k datům alespoň dvakrát, aby bylo možné je označit jako _horkou_. Změněný soubor je také označen jako _horká_. Po zahájení obnovení dojde k proaktivnímu vysazování dat na základě heatmapu. U verzí starších než Update 4 se data stáhnou při obnovení na základě jenom přístupu.

Pro obnovení na základě heatmapu se vztahují následující upozornění:

* Sledování heatmapu je povolené jenom pro vrstvené svazky a místně připnuté svazky se nepodporují.

* Obnovení založené na heatmapu se nepodporuje při klonování svazku na jiné zařízení. 

* Pokud je k dispozici místní obnovení a pro svazek, který má být obnoven na zařízení, existuje místní snímek, neprovedeme dedehydrataci (protože data jsou již místně k dispozici). 

* Ve výchozím nastavení se při obnovení vyvolaly úlohy pro vysazování, které proaktivně dehydratované data na základě heatmapu. 

Rutiny Windows PowerShellu se v Update 4 dají použít k dotazování na spouštění úloh pro vystavování, zrušení úlohy pro dehydrataci a získání stavu úlohy pro dosazování.

* `Get-HcsRehydrationJob` – Tato rutina načte stav úlohy opětovného nasazování. Pro jeden svazek se aktivuje jediná úloha pro vysazování.

* `Set-HcsRehydrationJob` – Tato rutina umožňuje pozastavit, zastavit a obnovit úlohu opětovného nasazování, když probíhá dosazování.

Další informace o rutinách pro vysazování najdete v referenčních informacích k [rutinám Windows PowerShellu pro StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Při automatickém vysazování se očekává obvykle vyšší výkon přechodného čtení. Skutečná velikost vylepšení závisí na různých faktorech, jako je vzor přístupu, četnost změn dat a datový typ. 

Chcete-li zrušit úlohu vysazování, můžete použít rutinu prostředí PowerShell. Pokud chcete trvale zakázat úlohy pro dosazování pro všechny budoucí obnovení, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Jak používat katalog záloh

Okno **katalog záloh** poskytuje dotaz, který vám pomůže zúžit výběr zálohovacího skladu. Můžete filtrovat zálohovací sklady, které jsou načteny na základě následujících parametrů:

* **Časový rozsah** – rozsah data a času, kdy byl vytvořen zálohovací sklad.
* **Zařízení** – zařízení, na kterém se vytvořil zálohovací sklad.
* **Zásady zálohování** nebo **svazek** – zásady zálohování nebo svazek přidružené k tomuto zálohovacímu skladu.

Filtrované zálohovací sklady jsou pak na základě těchto atributů:

* **Název** – název zásady zálohování nebo svazku přidruženého k zálohovacímu skladu.
* **Typ** – zálohovací sklady můžou být místní snímky nebo cloudové snímky. Místní snímek je záloha všech dat svazků uložených místně na zařízení, zatímco cloudový snímek odkazuje na zálohování dat svazků umístěných v cloudu. Místní snímky poskytují rychlejší přístup, zatímco se pro zajištění odolnosti dat volí cloudové snímky.
* **Size** – skutečná velikost zálohovacího skladu.
* **Vytvořeno** – datum a čas vytvoření zálohy. 
* **Svazky** – počet svazků přidružených k zálohovacímu skladu.
* **Iniciované** – zálohy se dají iniciovat automaticky podle plánu nebo ručně podle uživatele. (K naplánování záloh můžete použít zásady zálohování. Alternativně můžete použít možnost **provést zálohování** a provést interaktivní zálohování nebo zálohování na vyžádání.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Postup obnovení svazku StorSimple ze zálohy

K obnovení svazku StorSimple z konkrétní zálohy můžete použít okno **katalog záloh** . Mějte na paměti, že při obnovení svazku se svazek obnoví do stavu, ve kterém se vytvořila záloha. Všechna data, která byla přidána po operaci zálohování, budou ztracena.

> [!WARNING]
> Obnovení ze zálohy nahradí stávající svazky ze zálohy. To může způsobit ztrátu všech dat zapsaných po zálohování.


### <a name="to-restore-your-volume"></a>Postup obnovení svazku
1. Přejděte ke službě StorSimple Správce zařízení a potom klikněte na **katalog záloh**.

2. Vyberte zálohovací sklad následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. V rozevíracím seznamu vyberte svazek nebo zásady zálohování pro zálohu, kterou chcete vybrat.
   4. Kliknutím na **použít** spusťte tento dotaz.

      Zálohy přidružené k vybranému svazku nebo zásadám zálohování by se měly zobrazit v seznamu zálohovacích skladů.
   
      ![Seznam zálohovacích skladů](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozbalte zálohovací sklad, aby se zobrazily přidružené svazky. Aby bylo možné tyto svazky obnovit, je nutné je převést do režimu offline na hostiteli a zařízení. Přihlaste se ke svazkům v okně **svazky** zařízení a pak postupujte podle kroků v části [převedení svazku do režimu](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) offline, abyste je mohli převést do režimu offline.
   
   > [!IMPORTANT]
   > Nejdřív se ujistěte, že jste svazky nastavili offline na hostiteli, než na zařízení převezmete svazky v režimu offline. Pokud svazky nepřevedete na hostitele v režimu offline, může potenciálně vést k poškození dat.
   
4. Přejděte zpět na kartu **katalog záloh** a vyberte zálohovací sklad. Klikněte pravým tlačítkem a potom v místní nabídce vyberte **obnovit**.

    ![Seznam zálohovacích skladů 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Zobrazí se výzva k potvrzení. Zkontrolujte informace o obnovení a pak zaškrtněte políčko potvrzení.
   
    ![Stránka potvrzení](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klikněte na **obnovit**. Tím se spustí úloha obnovení, kterou můžete zobrazit pomocí přístupu na stránku **úlohy** .

   ![Stránka s potvrzením 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po dokončení obnovení ověřte, že obsah svazků je nahrazen svazky ze zálohy.


## <a name="if-the-restore-fails"></a>Pokud obnovení neproběhne úspěšně

Pokud operace obnovení z nějakého důvodu selže, zobrazí se upozornění. Pokud k tomu dojde, aktualizujte seznam zálohování a ověřte, zda je záloha stále platná. Pokud je záloha platná a obnovujete z cloudu, může problém způsobovat problémy s připojením.

Chcete-li dokončit operaci obnovení, zaveďte svazek do režimu offline na hostiteli a opakujte operaci obnovení. Všimněte si, že všechny změny dat svazků, které byly provedeny během procesu obnovení, budou ztraceny.

## <a name="next-steps"></a>Další kroky
* Naučte se [Spravovat StorSimple svazky](storsimple-8000-manage-volumes-u2.md).
* Naučte se [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).