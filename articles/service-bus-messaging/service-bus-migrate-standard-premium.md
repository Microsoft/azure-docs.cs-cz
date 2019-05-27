---
title: Migrovat existující standardní obory názvů služby Azure Service Bus na úrovni premium | Dokumentace Microsoftu
description: Průvodce umožňuje migrovat existující služby Azure Service Bus standardních názvových prostorů Premium
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
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 65c207b4d03e7d156c8c871a3642601fd0489ead
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991422"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrovat existující standardní obory názvů služby Azure Service Bus na úrovni premium
Azure Service Bus nabízely dříve, obory názvů jenom na úrovni standard. Obory názvů jsou nastavení více tenantů, která jsou optimalizována pro prostředí pro vývojáře a Nízká propustnost. Úroveň premium nabízí vyhrazené prostředky na obor názvů pro předvídatelnou latenci a vyšší propustnost za pevnou cenu. Na úrovni premium je optimalizovaná pro vysokou propustnost a produkční prostředí, které vyžadují další podnikové funkce.

Tento článek popisuje, jak migrovat obory názvů stávající úrovně standard na úroveň premium.  

>[!WARNING]
> Migrace je určena pro standardní obory názvů služby Service Bus upgradovat na úroveň premium. Nástroj pro migraci nepodporuje Downgrade.

Některé z bodů do mějte na paměti: 
- Tato migrace je určená nestane na místě, což znamená, že stávající aplikace odesílatele a příjemce **nevyžadují žádné změny kódu nebo konfigurace**. Bude existující připojovací řetězec bude automaticky přejděte na nový obor názvů premium.
- **Premium** oboru názvů by měl mít **žádné entity** v něm k úspěšné migraci. 
- Všechny **entity** ve standardním oboru názvů jsou **zkopírovat** do oboru názvů úrovně premium během procesu migrace. 
- Podporuje migraci **1 000 entity za jednotku zasílání zpráv** na úrovni premium. Chcete-li zjistit, kolik jednotek zasílání zpráv je třeba, začněte s počet entit, které mají na vaši aktuální standardní obor názvů. 
- Nelze migrovat přímo z **úroveň basic** k **premier úrovně**, ale můžete provést tak nepřímo migrací z úrovně basic na standard první a poté úroveň ze standard na premium v dalším kroku.

## <a name="migration-steps"></a>Kroky migrace
Některé podmínky jsou spojeny s procesem migrace. Seznamte se s následující kroky a snížení rizika vzniku chyby. Tyto kroky popisují proces migrace a podrobné informace jsou uvedeny v následující části.

1. Vytvoření nového oboru názvů úrovně premium.
1. Spárujte obory názvů standard a premium k sobě navzájem.
1. Synchronizace (kopírování myší) entit na základě standardu pro obor názvů úrovně premium.
1. Potvrďte migraci.
1. Vyprázdnit entity ve standardním oboru názvů pomocí oboru názvů po migraci.
1. Odstraňte obor názvů standard.

>[!IMPORTANT]
> Po migraci byl nejdříve zapsán, přístup k původní standardní obor názvů a výpusť front a odběrů. Po zprávy mají Vyprázdněné, mohou zasílat na novém oboru názvů úrovně premium ke zpracování aplikací příjemce. Poté, co jste se nečekaně front a odběrů, doporučujeme, že jste odstranili staré obor názvů standard.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrace pomocí Azure CLI nebo Powershellu

K migraci standardní obor názvů služby Service Bus Premium pomocí Azure CLI nebo PowerShell nástroje, postupujte podle těchto kroků.

1. Vytvořte nový obor názvů služby Service Bus úrovně premium. Můžete odkazovat [šablon Azure Resource Manageru](service-bus-resource-manager-namespace.md) nebo [pomocí webu Azure portal](service-bus-create-namespace-portal.md). Je potřeba vybrat možnost **premium** pro **serviceBusSku** parametru.

1. Nastavte následující proměnné prostředí zjednodušit migraci příkazy.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Po migraci/název aliasu (post_migration_dns_name) se použije pro přístup k původní po migraci obor názvů standard. Využit k vyprázdnění fronty a předplatná a pak odstraňte oboru názvů.

1. Spárujte obory názvů standard a premium a spuštění synchronizace pomocí následujícího příkazu:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Kontrola stavu migrace s použitím následujícího příkazu:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migrace se považuje za dokončené, když se zobrazí následující hodnoty:
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * Stav zřizování = "ÚSPĚCH"

    Tento příkaz také zobrazí konfiguraci migrace. Zkontrolujte, že hodnoty jsou nastavené správně. Zkontrolujte taky obor názvů úrovně premium na portálu, aby byly vytvořeny všechny fronty a témata, a aby odpovídaly co existovala v oboru názvů standard.

1. Spuštěním následujícího příkazu pro dokončení potvrzení migrace:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrace s využitím webu Azure portal

Migrace s využitím webu Azure portal má stejný logický tok jako migrace pomocí příkazů. Postupujte podle těchto kroků k migraci pomocí webu Azure portal.

1. Na **navigace** nabídky v levém podokně vyberte **migrace na premium**. Klikněte na tlačítko **Začínáme** tlačítko Přejít na další stránku.
    ![Migrace cílová stránka][]

1. Kompletní **nastavení**.
   ![Nastavit obor názvů][]
   1. Vytvořte a přiřaďte obor názvů úrovně premium pro existující obor názvů standard pro migraci.
        ![Nastavení oboru názvů – vytvořit obor názvů úrovně premium][]
   1. Zvolte **po migraci název**. Tento název budete používat pro přístup k obor názvů standard po dokončení migrace.
        ![Nastavení oboru názvů – vyberte název migrace příspěvku][]
   1. Vyberte **'Další'** pokračujte.
1. Synchronizace entit mezi obory názvů standard a premium.
    ![Nastavit obor názvů - synchronizace entit - start][]

   1. Vyberte **zahájit synchronizaci** zahajte synchronizaci entity.
   1. Vyberte **Ano** v dialogovém okně potvrďte a spuštění synchronizace.
   1. Počkejte, dokud neskončí synchronizace. Stav je k dispozici na na stavovém řádku.
        ![Obor názvů - synchronizace entit – průběh instalace][]
        >[!IMPORTANT]
        > Pokud chcete přerušit migraci z jakéhokoli důvodu, najdete v tématu přerušení tok v tomto dokumentu v části Nejčastější dotazy.
   1. Po dokončení synchronizace vyberte **Další** v dolní části stránky.

1. Zkontrolujte změny na stránce souhrnu. Vyberte **dokončit migraci** přepnout obory názvů a k dokončení migrace.
    ![Přepnout obor názvů – přepínač nabídky][] po dokončení migrace se zobrazí na stránce potvrzení.
    ![Obor názvů přepínače – úspěch][]

## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-happens-when-the-migration-is-committed"></a>Co se stane, když se zaměřuje na migraci?

Po migraci je potvrzená, připojovací řetězec, který odkazuje na obor názvů standard bude odkazovat na názvový prostor úrovně premium.

Aplikace odesílatele a příjemce se odpojit od standardní Namespace a automaticky znovu připojit k oboru názvů úrovně premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>K čemu já po dokončení standard na premium migrace?

Standard na premium migrace zajistí, že metadata entit, jako jsou témata, odběry a filtry jsou zkopírovány z obor názvů standard na obor názvů úrovně premium. Data zprávy, která byla potvrzena pro obor názvů standard není zkopírován z oboru názvů standard na obor názvů úrovně premium.

Obor názvů standard může mít některé zprávy, které byly odeslány a potvrzené při migraci probíhá. Tyto zprávy ze standardní Namespace ručně vyprázdnit a ručně odešlete je premium Namespace. Chcete-li ručně vyprázdnit zprávy, použijte konzolovou aplikaci nebo skript, který pozastavuje obor názvů standard entity pomocí názvu DNS migrace příspěvku, který jste zadali v příkazech migrace. Odeslat tyto zprávy pro názvový prostor úrovně premium, takže mohou být zpracovány příjemci.

Po zprávy mají Vyprázdněné, odstraňte obor názvů standard.

>[!IMPORTANT]
> Po zprávy na obor názvů standard mají Vyprázdněné, odstraňte obor názvů standard. To je důležité, protože připojovací řetězec, který původně označuje obor názvů standard nyní odkazuje na názvový prostor úrovně premium. Už nemusíte standardní Namespace. Odstraňuje se obor názvů standard, který jste migrovali pomáhá redukovat nejasnosti novější.

### <a name="how-much-downtime-do-i-expect"></a>Jak velká Doba výpadku můžu očekávat?
Proces migrace slouží k očekávaných výpadků aplikací. Výpadek je snížit pomocí připojovacího řetězce, které aplikace odesílatele a příjemce použít tak, aby odkazovala na nový obor názvů premium.

Prostoje, které se mají aplikace je omezen na čas potřebný k aktualizaci položky DNS tak, aby odkazoval na názvový prostor úrovně premium. Výpadek je přibližně 5 minut.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Je nutné provést změny konfigurace v průběhu migrace?
Ne, nejsou žádné změny kódu nebo konfigurace potřebné k migraci. Automaticky se namapuje připojovací řetězec, který aplikace odesílatele a příjemce použít pro přístup k standardní Namespace tak, aby fungoval jako alias pro obor názvů úrovně premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co se stane, když jsem migraci přerušit?
Migrace může být přerušena buď pomocí `Abort` příkazu nebo pomocí webu Azure portal. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>portál Azure

![Přerušit tok – zrušení synchronizace][]
![přerušit tok - přerušit dokončení][]

Při migraci byl přerušen, zruší proces kopírování entity (témata, odběry a filtry) od standardu pro obor názvů úrovně premium a zruší párování.

Připojovací řetězec není aktualizován tak, aby odkazoval na názvový prostor úrovně premium. Vaše stávající aplikace i nadále fungovat stejným způsobem jako před zahájením migrace.

Ale ho nebude odstranění entit v oboru názvů úrovně premium nebo odstranit názvový prostor úrovně premium. Odstraňte ručně entity, pokud jste se rozhodli pokračovat v migraci.

>[!IMPORTANT]
> Pokud se rozhodnete k migraci přerušit, odstraňte premium Namespace, které bylo zřízené pro migraci tak, aby se vám neúčtují poplatky za prostředky.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nechci se nyní máte k vyprázdnění zpráv. Co mám udělat?

Můžou existovat zprávy, které jsou odeslané aplikací odesílatele a uložena do úložiště na standardní Namespace při migraci probíhá a těsně před plánovaným migraci potvrdit.

Během migrace není skutečná data/datovou část zprávy ze standardní zkopírován do oboru názvů úrovně premium. Zprávy muset ručně vyprázdnit a pak posílají do oboru názvů úrovně premium.

Pokud migrujete během intervalu plánované údržby a údržbu, a nechcete ručně vyprázdnit a odesílat zprávy, postupujte takto:

1. Zastavte aplikace odesílatele. Aplikace příjemce bude zpracovávat zprávy, které jsou v současnosti v oboru názvů standard a vyprázdní frontu.
1. Po front a předplatných ve standardní Namespace jsou prázdné, postupujte podle kroků je popsán výše k provedení migrace od standardu pro obor názvů úrovně premium.
1. Po dokončení migrace můžete restartovat aplikace odesílatele.
1. Odesílateli a příjemci budou nyní automaticky připojit s oborem názvů premium.

    >[!NOTE]
    > Není nutné zastavit aplikace příjemce pro migraci.
    >
    > Po dokončení migrace aplikací příjemce se odpojit od obor názvů standard a automaticky připojit k oboru názvů úrovně premium.

## <a name="next-steps"></a>Další postup

* Další informace o [rozdíly zasílání zpráv úrovně standard a premium](./service-bus-premium-messaging.md).
* Další informace o [obnovení aspekty vysoké dostupnosti a geografické po havárii pro Service Bus úrovně premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

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
