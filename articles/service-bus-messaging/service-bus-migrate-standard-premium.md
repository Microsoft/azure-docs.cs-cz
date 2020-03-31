---
title: Migrace oborů názvů Azure Service Bus – standardní na prémiovou
description: Průvodce umožňující migraci stávajících oborů standardních názvů Azure Service Bus na premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385023"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrace existujících standardních oborů názvů Azure Service Bus na úroveň premium

Dříve Azure Service Bus nabízela obory názvů jenom na standardní úrovni. Obory názvů jsou víceklientská nastavení, která jsou optimalizována pro prostředí s nízkou propustností a vývojářská prostředí. Úroveň premium nabízí vyhrazené prostředky pro obor názvů pro předvídatelnou latenci a zvýšenou propustnost za pevnou cenu. Úroveň premium je optimalizována pro prostředí s vysokou propustností a produkčníprostředí, které vyžadují další podnikové funkce.

Tento článek popisuje, jak migrovat existující obory názvů úrovně standardu na úroveň premium.  

>[!WARNING]
> Migrace je určena pro standardní obory názvů Service Bus, které mají být upgradovány na úroveň premium. Nástroj pro migraci nepodporuje downgrading.

Některé z bodů na vědomí:

- Tato migrace se má stát na místě, což znamená, že existující aplikace odesílatele a příjemce **nevyžadují žádné změny kódu nebo konfigurace**. Existující připojovací řetězec automaticky přejde na nový obor názvů premium.
- Obor názvů **premium** by měl mít **žádné entity** v něm pro migraci úspěšné.
- Všechny **entity** ve standardním oboru názvů jsou během procesu migrace **zkopírovány** do oboru názvů premium.
- Migrace podporuje **1 000 entit na jednotku zasílání zpráv** na prémiové úrovni. Chcete-li zjistit, kolik jednotek zasílání zpráv potřebujete, začněte s počtem entit, které máte v aktuálním standardním oboru názvů.
- Nemůžete přímo migrovat ze **základní úrovně** na **úroveň premium**, ale můžete tak učinit nepřímo přechodem ze základní na standardní první a pak ze standardu na prémii v dalším kroku.

## <a name="migration-steps"></a>Kroky migrace

Některé podmínky jsou přidruženy k procesu migrace. Seznamte se s následujícími kroky, abyste snížili možnost chyb. Tyto kroky popisují proces migrace a podrobné podrobnosti jsou uvedeny v následujících částech.

1. Vytvořte nový obor názvů premium.
1. Spárujte standardní a prémiové obory názvů mezi sebou.
1. Synchronizujte (copy-over) entity ze standardu do oboru názvů premium.
1. Pozařte migraci.
1. Entity vyprázdnění ve standardním oboru názvů pomocí názvu oboru názvů po migraci.
1. Odstraňte standardní obor názvů.

>[!IMPORTANT]
> Po migraci byla potvrzena, přístup ke starému oboru názvů standardní a vyprázdnit fronty a odběry. Po vyprázdnění zpráv mohou být odeslány do nového oboru názvů premium, který mají být zpracovány aplikacemi příjemce. Po vyprázdnění fronty a odběry, doporučujeme odstranit starý standardní obor názvů.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrace pomocí Azure CLI nebo PowerShellu

Chcete-li migrovat standardní obor názvů Service Bus na prémii pomocí nástroje Azure CLI nebo PowerShell, postupujte takto.

1. Vytvořte nový obor názvů Service Bus premium. Můžete odkazovat na [šablony Azure Resource Manager](service-bus-resource-manager-namespace.md) nebo použít portál [Azure](service-bus-create-namespace-portal.md). Nezapomeňte vybrat **prémii** pro parametr **serviceBusSku.**

1. Chcete-li zjednodušit příkazy pro migraci, nastavte následující proměnné prostředí.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Alias/název po migraci (post_migration_dns_name) bude použit pro přístup ke staré standardní aplikaci oboru názvů po migraci. Pomocí tohoto vyprázdnění fronty a odběry a potom odstranit obor názvů.

1. Spárujte standardní a prémiové obory názvů a spusťte synchronizaci pomocí následujícího příkazu:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Zkontrolujte stav migrace pomocí následujícího příkazu:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migrace je považována za dokončenou, když se zobrazí následující hodnoty:

    * MigrationState = "Aktivní"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Úspěšné"

    Tento příkaz také zobrazí konfiguraci migrace. Zkontrolujte, zda jsou hodnoty nastaveny správně. Zkontrolujte také obor názvů premium na portálu, abyste zajistili, že byly vytvořeny všechny fronty a témata a že odpovídají tomu, co existovalo ve standardním oboru názvů.

1. Potvrzení migrace provedením následujícího příkazu:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrace pomocí portálu Azure

Migrace pomocí portálu Azure má stejný logický tok jako migrace pomocí příkazů. Podle těchto kroků migrujte pomocí portálu Azure.

1. V nabídce **Navigace** v levém podokně vyberte **možnost Migrovat na prémii**. Kliknutím na tlačítko **Začínáme** přenesete na další stránku.
    ![Úvodní stránka migrace][]

1. Dokončit **instalaci**.
   ![Instalační obor názvů][]
   1. Vytvořte a přiřaďte obor názvů premium k migraci existujícího standardního oboru názvů.
        ![Instalační obor názvů – vytvoření oboru názvů premium][]
   1. Zvolte **název po migraci**. Tento název použijete pro přístup ke standardnímu oboru názvů po dokončení migrace.
        ![Instalační obor názvů – vyberte název po migraci][]
   1. Chcete-li pokračovat, vyberte **možnost Další.**
1. Synchronizujte entity mezi standardními a prémiovými obory názvů.
    ![Instalační obor názvů – synchronizační entity – start][]

   1. Vyberte **Spustit synchronizaci,** chcete-li začít synchronizovat entity.
   1. Chcete-li v dialogovém okně potvrdit a spustit synchronizaci, vyberte **ano.**
   1. Počkejte, až bude synchronizace dokončena. Stav je k dispozici na stavovém řádku.
        ![Instalační obor názvů – synchronizační entity – průběh][]
        >[!IMPORTANT]
        > Pokud potřebujete migraci z nějakého důvodu přerušit, zkontrolujte tok přerušení v části Nejčastější dotazy v tomto dokumentu.
   1. Po dokončení synchronizace vyberte **Další** v dolní části stránky.

1. Zkontrolujte změny na souhrnné stránce. Chcete-li přepnout obory názvů a dokončit migraci, vyberte **možnost Dokončit migraci.**
    ![Přepnout obor názvů - přepínat nabídku][]  
    Po dokončení migrace se zobrazí stránka s potvrzením.
    ![Přepnout obor názvů – úspěch][]

## <a name="caveats"></a>Upozornění

Některé funkce poskytované úrovní Azure Service Bus Standard nejsou podporovány úrovní Azure Service Bus Premium. Ty jsou od návrhu, protože úroveň premium nabízí vyhrazené prostředky pro předvídatelnou propustnost a latenci.

Zde je seznam funkcí, které nejsou podporovány Premium a jejich zmírnění -

### <a name="express-entities"></a>Expresní entity

   Expresní entity, které nezavazují žádná data zpráv do úložiště, nejsou v premium podporovány. Vyhrazené prostředky poskytují významné zlepšení propustnosta při zajištění, že data jsou trvalá, jak se očekává od jakéhokoli systému zasílání zpráv rozlehlé sítě.

   Během migrace bude v oboru názvů Premium vytvořena jakákoli vyslýchaná entita ve standardním oboru názvů jako neexpresní entita.

   Pokud používáte šablony Azure Resource Manager (ARM), ujistěte se, že odeberete příznak "enableExpress" z konfigurace nasazení, aby se vaše automatizované pracovní postupy spouštěli bez chyb.

### <a name="partitioned-entities"></a>Dělené entity

   Dělené entity byly podporovány na úrovni Standard, aby byla zajištěna lepší dostupnost v nastavení s více tenanty. S poskytováním vyhrazených prostředků, které jsou k dispozici pro obor názvů na úrovni Premium, to již není potřeba.

   Během migrace je v oboru názvů Standard vytvořena jakákoli rozdělená entita v oboru názvů Standard jako entita bez oddílů.

   Pokud vaše šablona ARM nastaví 'enablePartitioning' na 'true' pro konkrétní frontu nebo téma, pak bude ignorována makléřem.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-happens-when-the-migration-is-committed"></a>Co se stane, když je migrace potvrzena?

Po migraci je potvrzena, připojovací řetězec, který ukázal na standardní obor názvů bude ukazovat na obor názvů premium.

Aplikace odesílatele a příjemce se odpojí od standardního oboru názvů a automaticky se znovu připojí k oboru názvů premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Co mám dělat po dokončení standardní migrace premium?

Standardní migrace premium zajišťuje, že metadata entity, jako jsou témata, odběry a filtry, jsou zkopírovány ze standardního oboru názvů do oboru názvů premium. Data zprávy, která byla potvrzena do standardního oboru názvů, nejsou zkopírována ze standardního oboru názvů do oboru názvů premium.

Standardní obor názvů může mít některé zprávy, které byly odeslány a potvrzeny v průběhu migrace. Ručně vyprázdněte tyto zprávy ze standardního oboru názvů a ručně je odešlete do prémiového oboru názvů. Chcete-li zprávy ručně vyprázdnit, použijte konzolovou aplikaci nebo skript, který vyčerpává standardní entity oboru názvů pomocí názvu DNS po migraci, který jste zadali v příkazech migrace. Odešlete tyto zprávy do oboru názvů premium tak, aby mohly být zpracovány příjemci.

Po vyprázdnění zpráv odstraňte standardní obor názvů.

>[!IMPORTANT]
> Po odstranění zpráv ze standardního oboru názvů odstraňte standardní obor názvů. To je důležité, protože připojovací řetězec, který původně odkazoval na standardní obor názvů, nyní odkazuje na obor názvů premium. Standardní obor názvů již nebudete potřebovat. Odstranění standardního oboru názvů, který jste migrovali, pomáhá snížit pozdější nejasnosti.

### <a name="how-much-downtime-do-i-expect"></a>Kolik prostojů očekávám?

Proces migrace má snížit očekávané prostoje aplikací. Prostoje je snížena pomocí připojovacího řetězce, který odesílatel a příjemce aplikace použít k přejděte na nový obor názvů premium.

Prostoje, které je zkušený aplikace je omezena na čas potřebný k aktualizaci položky DNS přejděte na obor názvů premium. Prostoje je přibližně 5 minut.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Musím při migraci provádět nějaké změny konfigurace?

Ne, k migraci nejsou potřeba žádné změny kódu nebo konfigurace. Připojovací řetězec, který aplikace odesílatele a příjemce používají pro přístup ke standardnímu oboru názvů, je automaticky namapován tak, aby působil jako alias pro obor názvů premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co se stane, když migraci přeruším?

Migrace může být přerušena pomocí `Abort` příkazu nebo pomocí portálu Azure.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>portál Azure

![Přerušit tok][]
![- přerušit synchronizaci toku přerušení - přerušení dokončení][]

Při přerušení procesu migrace přeruší proces kopírování entit (témata, odběry a filtry) ze standardu do oboru názvů premium a přeruší párování.

Připojovací řetězec není aktualizován tak, aby ukazoval na obor názvů premium. Stávající aplikace nadále fungují stejně jako před zahájením migrace.

Neodstraní však entity v oboru názvů premium ani neodstraní obor názvů premium. Pokud jste se rozhodli nepokračovat v migraci, odstraňte entity ručně.

>[!IMPORTANT]
> Pokud se rozhodnete migraci přerušit, odstraňte prémiový obor názvů, který jste pro migraci zřídí, abyste za prostředky neúčtovali poplatky.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nechci ty zprávy vypouštět. Co mám udělat?

Mohou existovat zprávy, které jsou odesílány aplikacemi odesílatele a potvrzeny do úložiště ve standardním oboru názvů během migrace a těsně před odesláním migrace.

Během migrace se skutečná data zprávy/datové části nezkopírují ze standardu do oboru názvů premium. Zprávy musí být ručně vyprázdněny a poté odeslány do oboru názvů premium.

Pokud však můžete migrovat během plánovaného okna údržby nebo údržby a nechcete je ručně vyprázdnit a odeslat, postupujte takto:

1. Zastavte aplikace odesílatele. Aplikace příjemce zpracují zprávy, které jsou aktuálně ve standardním oboru názvů, a vyprázdní frontu.
1. Poté, co fronty a odběry ve standardním oboru názvů jsou prázdné, postupujte podle postupu, který je popsán dříve provést migraci ze standardu do oboru názvů premium.
1. Po dokončení migrace můžete restartovat aplikace odesílatele.
1. Odesílatelé a příjemci se nyní automaticky připojí k oboru názvů premium.

    >[!NOTE]
    > Není třeba zastavit aplikace přijímače pro migraci.
    >
    > Po dokončení migrace se aplikace přijímače odpojí od standardního oboru názvů a automaticky se připojí k oboru názvů premium.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [rozdílech mezi standardním a prémiovým zasíláním zpráv](./service-bus-premium-messaging.md).
* Seznamte se s aspekty vysoké [dostupnosti a geografického zotavení po havárii pro service bus premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Úvodní stránka migrace]: ./media/service-bus-standard-premium-migration/1.png
[Instalační obor názvů]: ./media/service-bus-standard-premium-migration/2.png
[Instalační obor názvů – vytvoření oboru názvů premium]: ./media/service-bus-standard-premium-migration/3.png
[Instalační obor názvů – vyberte název po migraci]: ./media/service-bus-standard-premium-migration/4.png
[Instalační obor názvů – synchronizační entity – start]: ./media/service-bus-standard-premium-migration/5.png
[Instalační obor názvů – synchronizační entity – průběh]: ./media/service-bus-standard-premium-migration/8.png
[Přepnout obor názvů - přepínat nabídku]: ./media/service-bus-standard-premium-migration/9.png
[Přepnout obor názvů – úspěch]: ./media/service-bus-standard-premium-migration/12.png

[Přerušit tok - přerušit synchronizaci]: ./media/service-bus-standard-premium-migration/abort1.png
[Přerušit tok - přerušení dokončeno]: ./media/service-bus-standard-premium-migration/abort3.png
