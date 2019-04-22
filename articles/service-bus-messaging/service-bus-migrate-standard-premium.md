---
title: Migrovat existující Azure Service Bus standardní obory názvů na úrovni Premium | Dokumentace Microsoftu
description: Průvodce umožňuje migraci stávajících Azure Standard oborů názvů Service Bus Premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896319"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Migrovat existující Azure Service Bus standardní obory názvů na úrovni Premium

Azure Service Bus nabízely dříve, obory názvů jenom na úrovni Standard. Byly nastavení více tenantů, které byly optimalizovány pro prostředí pro vývojáře a Nízká propustnost.

V minulosti poslední se rozšířila Azure Service Bus nabízí na úrovni Premium, která nabízí vyhrazený prostředky na obor názvů pro předvídatelnou latenci a vyšší propustnost za pevnou cenu, která je optimalizována pro produkční prostředí a vysokou propustností vyžaduje další podnikové funkce.

Níže nástrojů umožňuje existující standardních názvových prostorů pro migraci na úrovni Premium.

>[!WARNING]
> Migrace je určena pro obor názvů Service Bus úrovně Standard bude ***upgradovat*** na úrovni Premium.
>
> Nástroje pro migraci ***nemá*** podporují Downgrade.
>[!NOTE]
> Tato migrace je určená nestane ***na místě***.
>
> Z toho vyplývá, že existující odesílatele a příjemce aplikace nevyžadují žádné změny kódu nebo konfigurace.
>
> Bude existující připojovací řetězec bude automaticky přejděte na nový obor názvů premium.
>
> Kromě toho jsou všechny entity ve standardním oboru názvů **zkopíruje** v Prémiovém oboru názvů během procesu migrace.
>
>
> Podporujeme ***entity 1000 za jednotku zasílání zpráv*** na Premium, takže k identifikaci kolik jednotek zasílání zpráv je třeba, spusťte prosím s počet entit, které mají na vaši aktuální standardní obor názvů.

## <a name="migration-steps"></a>Kroky migrace

>[!IMPORTANT]
> Zde jsou některé upozornění spojených s procesem migrace. Požadujeme, abyste plně seznámit se s kroky pro omezení možností chyb.

Proces migrace konkrétní krok za krokem, najdete v níže uvedené příručky.

Jsou logické kroky –

1. Vytvoření nového oboru názvů úrovně Premium.
2. Pair – obor názvů Standard a Premium k sobě navzájem.
3. Synchronizace (kopírování myší) entity z úrovně Standard na obor názvů úrovně Premium
4. Potvrďte migraci
5. Vyprázdnit entity ve standardním oboru názvů pomocí oboru názvů po migraci
6. Odstranit obor názvů Standard

>[!NOTE]
> Po migraci byl nejdříve zapsán, je velmi důležité pro přístup k původní standardní obor názvů a vyprazdňování front a předplatných.
>
> Po zprávy mají byla Vyprázdněné navýšení kapacity, může být odeslán nový obor názvů premium ke zpracování aplikací příjemce.
>
> Jakmile máte byla Vyprázdněné front a odběrů, doporučujeme odstranit staré obor názvů Standard. Můžete nesmí být nutnosti ho!

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrace pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure

K migraci vašeho oboru názvů Service Bus úrovně Standard na Premium pomocí nástroje Azure CLI nebo Powershellu, přečtěte si pod průvodce.

1. Vytvoření nového oboru názvů Service Bus úrovně Premium. Můžete odkazovat [šablon Azure Resource Manageru](service-bus-resource-manager-namespace.md) nebo [pomocí webu Azure portal](service-bus-create-namespace-portal.md). Je potřeba vybrat možnost "Premium" pro **serviceBusSku** parametru.

2. Nastavte následující proměnné prostředí zjednodušit migraci příkazy.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > Název po migraci (post_migration_dns_name) se použije pro přístup k původní po migraci obor názvů Standard. To je nutné použít vyprázdnit front a předplatných a potom odstranit obor názvů.

3. **Pár** obory názvů Standard a Premium a **zahájit synchronizaci** pomocí následující příkaz:

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Zkontrolovat stav použití migrace následující příkaz:
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migrace se považuje za dokončené při
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * Stav zřizování = "ÚSPĚCH"

    Tento příkaz také zobrazí konfiguraci migrace. Prosím zkontrolujte double, ujistěte se, že hodnoty jsou nastaveny jako předchozí deklaraci.

    Kromě toho se taky podívat obor názvů úrovně Premium na portálu a zajistěte, aby byly vytvořeny všechny fronty a témata a aby splňovaly co existoval na obor názvů Standard.

5. Potvrďte migraci spuštěním následujícího dokončení příkazu
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrace pomocí webu Azure portal

Migrace na webu Azure portal má stejný logický tok jako migrace pomocí příkazů. Odkazovat na pod průvodce krok za krokem procesu k migraci pomocí portálu.

1. Vyberte si **"Migrace na Premium"** nabídky z navigační nabídky v levém podokně. Klikněte na tlačítko **"Začínáme"** tlačítka budete pokračovat na další stránku.
    ![Migrace cílová stránka][]

2. Kompletní **nastavení**.
   ![Nastavit obor názvů][]
   1. Vytvořte a přiřaďte obor názvů úrovně Premium pro existující obor názvů Standard pro migraci.
        ![Nastavení oboru názvů – vytvořit obor názvů úrovně premium][]
   2. Vyberte si **"Po migraci name"** pro přístup k obor názvů Standard, tak po dokončení migrace.
        ![Nastavení oboru názvů – vyberte název migrace příspěvku][]
   3. Klikněte na tlačítko **'Další'** pokračovat.
3. **Synchronizace** entity mezi obor názvů Standard a Premium.
    ![Nastavit obor názvů - synchronizace entit - start][]

   1. Klikněte na tlačítko **zahájit synchronizaci** zahajte synchronizaci entity.
   2. Klikněte na tlačítko **"Yes"** v místní nabídce potvrzení spuštění synchronizace.
   3. Počkejte, dokud **synchronizace** je dokončena. Stav je k dispozici na na stavovém řádku.
        ![Obor názvů - synchronizace entit – průběh instalace][]
        >[!IMPORTANT]
        > Pokud potřebujete **přerušit** z jakéhokoli důvodu, najdete v tématu přerušení tok v tomto dokumentu v části Nejčastější dotazy.
   4. Po dokončení synchronizace klikněte na tlačítko **'Další'** tlačítko v dolní části stránky.

4. Zkontrolujte změny na stránce souhrnu.
    ![Obor názvů přepínač - přepínač nabídky][]

5. Klikněte na **"Dokončit migraci"** obory názvů a dokončení migrace.
    ![Obor názvů přepínače – úspěch][]

## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-happens-when-the-migration-is-committed"></a>Co se stane, když se zaměřuje na migraci?

Po migraci je potvrzená, připojovací řetězec, který odkazuje na obor názvů Standard bude odkazovat na názvový prostor úrovně Premium.

Aplikace odesílatele a příjemce se odpojit od standardní Namespace a automaticky znovu připojit k oboru názvů úrovně Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>K čemu já po dokončení Standard na Premium migrace?

Standard na Premium migrace se zajistí, že metadata entit (témata, odběry, filtry, et al.) jsou zkopírovaná z standardu pro obor názvů úrovně Premium. Data zprávy, která byla potvrzena pro obor názvů Standard není zkopírovaná z standardu pro obor názvů úrovně Premium.

Kvůli tomu obor názvů Standard může mít některé zprávy, které byly odeslány a potvrzené při migraci probíhá. Tyto zprávy musí ručně Vyprázdněné ze standardní Namespace a odesílat přes Namespace Premium ručně.

K tomu, můžete ***musí*** pomocí konzolové aplikace nebo skript, který vyprázdnit obor názvů Standard entity pomocí **název DNS po migraci** zadaný v příkazu migrace a poté pošlete zprávy Namespace Premium, takže mohou být zpracovány příjemci.

Jakmile zprávy mají Vyprázdněné, pokračujte k odstranit obor názvů Standard.

>[!IMPORTANT]
> Upozorňujeme, že jakmile zprávy na obor názvů Standard mají Vyprázdněné, můžete **musí** odstranit obor názvů Standard.
>
> To je důležité, protože připojovací řetězec, který původně teď označuje obor názvů Standard ve skutečnosti odkazuje na názvový prostor úrovně Premium. Můžete nesmí být nutnosti tento standardní Namespace už.
>
> Odstraňuje se obor názvů Standard, že jste migrovali pomáhá snižuje v agregaci, přidali později. 

### <a name="how-much-downtime-do-i-expect"></a>Jak velká Doba výpadku můžu očekávat?
Výše popsaný proces migrace slouží k očekávaných výpadků aplikací. Je to s využitím připojovací řetězec, který se odkazuje na novém oboru názvů úrovně Premium pomocí aplikace odesílatele a příjemce.

Výpadek zkušení aplikací je omezená na objem čas potřebný k aktualizaci položky DNS tak, aby odkazoval na názvový prostor úrovně Premium.

To může být předpokládá se, že ***přibližně 5 minut***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>Je nutné provést změny konfigurace při provádění migrace?
Ne, nejsou žádné změny kódu/konfigurace potřebné pro tuto migraci provést. Připojovací řetězec, který aplikace odesílatele a příjemce použít pro přístup k standardní Namespace, automaticky se namapuje tak, aby fungoval jako **alias** pro Premium Namespace.

### <a name="what-happens-when-i-abort-the-migration"></a>Co se stane, když jsem migraci přerušit?
Pomocí příkazu "Přerušení" nebo přes Azure portal můžete přenos byl přerušen. 

#### <a name="azure-cli-or-powershell"></a>Azure CLI nebo Powershellu

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>portál Azure

![Přerušit tok – zrušení synchronizace][]
![přerušit tok - přerušit dokončení][]

Při migraci byl přerušen, ve skutečnosti zruší proces kopírování entitami (témata, odběry a filtry) úroveň ze Standard na obor názvů úrovně Premium a zruší párování.

Připojovací řetězec **není** aktualizovat tak, aby odkazoval na názvový prostor úrovně Premium. Vaše stávající aplikace i nadále fungovat stejným způsobem jako před zahájením migrace.

Však to **nemá** odstraňování entit v oboru názvů úrovně Premium nebo odstranit názvový prostor úrovně Premium, samotného. To je třeba provést ručně, pokud kdybychom se rozhodli není pokračovat v migraci po všech.

>[!IMPORTANT]
> Pokud se rozhodnete k migraci přerušit, odstraňte Namespace Premium, který se má zřídit na migraci tak, aby se vám neúčtují poplatky za prostředky.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nechci se nyní máte k vyprázdnění zpráv. Co mám udělat?

Mohou být zprávy, které jsou odeslané aplikací odesílatele a uložena do úložiště na standardní Namespace, zatímco probíhá migrace a usiluje o těsně před migrací.

Vzhledem k tomu, že během migrace skutečná data/datovou část zprávy není zkopíruje úroveň ze Standard na Premium, ty se musí ručně vyprázdnit a pak posílají do oboru názvů úrovně Premium.

Ale pokud můžete migrovat během intervalu plánované údržby a údržbu a nechcete ručně vyprázdnit a odesílat zprávy, prosím použijte níže uvedený postup –

1. Zastavit aplikace odesílatele a povolit příjemcům zpracovávat zprávy, které jsou aktuálně ve standardním oboru názvů a vyprázdnění fronty.
2. Jakmile front a předplatných ve standardní Namespace jsou prázdné, použijte postup popsaný výše k provedení migrace z úrovně Standard na obor názvů úrovně Premium.
3. Po dokončení migrace můžete restartovat aplikace odesílatele.
4. Odesílateli a příjemci budou nyní automaticky připojit s oborem názvů Premium.

    >[!NOTE]
    > Příjemce nemusí zastavit pro migraci.
    >
    > Po dokončení migrace se příjemci odpojit od obor názvů Standard a automaticky se připojovat k oboru názvů úrovně Premium.

## <a name="next-steps"></a>Další postup

* Další informace o [rozdílů mezi úrovněmi Standard a zasílání zpráv úrovně Premium](./service-bus-premium-messaging.md)
* Další informace o obnovení aspekty vysoké dostupnosti a geografické Diaster pro Service Bus úrovně Premium [zde](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Migrace cílová stránka]: ./media/service-bus-standard-premium-migration/1.png
[Nastavit obor názvů]: ./media/service-bus-standard-premium-migration/2.png
[Nastavení oboru názvů – vytvořit obor názvů úrovně premium]: ./media/service-bus-standard-premium-migration/3.png
[Nastavení oboru názvů – vyberte název migrace příspěvku]: ./media/service-bus-standard-premium-migration/4.png
[Nastavit obor názvů - synchronizace entit - start]: ./media/service-bus-standard-premium-migration/5.png
[Obor názvů - synchronizace entit – průběh instalace]: ./media/service-bus-standard-premium-migration/8.png
[Obor názvů přepínač - přepínač nabídky]: ./media/service-bus-standard-premium-migration/9.png
[Obor názvů přepínače – úspěch]: ./media/service-bus-standard-premium-migration/12.png

[Přerušit tok – zrušení synchronizace]: ./media/service-bus-standard-premium-migration/abort1.png
[Přerušit tok - přerušit dokončení]: ./media/service-bus-standard-premium-migration/abort3.png
