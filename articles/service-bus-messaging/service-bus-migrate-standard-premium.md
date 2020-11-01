---
title: Migrace Azure Service Bus oborů názvů – Standard na Premium
description: Průvodce pro povolení migrace stávajících oborů názvů Azure Service Bus Standard do úrovně Premium
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "85340745"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrace stávajících oborů názvů Azure Service Bus Standard do úrovně Premium

Dřív Azure Service Bus nabízet obory názvů jenom na úrovni Standard. Obory názvů jsou nastavení pro více tenantů, která jsou optimalizovaná pro prostředí s nízkou propustností a vývojářem. Úroveň Premium nabízí vyhrazené prostředky na obor názvů pro předvídatelné latenci a vyšší propustnost za pevnou cenu. Úroveň Premium je optimalizovaná pro vysokou propustnost a produkční prostředí, která vyžadují další podnikové funkce.

Tento článek popisuje, jak migrovat existující obory názvů úrovně Standard na úroveň Premium.  

>[!WARNING]
> Migrace je určená pro Service Bus standardní obory názvů, které se mají upgradovat na úroveň Premium. Nástroj pro migraci nepodporuje downgrade.

Některé body, které je potřeba poznamenat:

- Tato migrace by měla být provedena, což znamená, že stávající aplikace odesílatele a přijímače **nevyžadují žádné změny kódu nebo konfigurace** . Existující připojovací řetězec bude automaticky ukazovat na nový obor názvů Premium.
- Obor názvů **Premium** by neměl obsahovat **žádné entity** , aby migrace proběhla úspěšně.
- Všechny **entity** v oboru názvů Standard jsou během procesu migrace **zkopírovány** do oboru názvů Premium.
- Migrace podporuje **1 000 entit na jednu jednotku zasílání zpráv** na úrovni Premium. Pokud chcete zjistit, kolik jednotek pro zasílání zpráv potřebujete, začněte s počtem entit, které máte v aktuálním oboru názvů Standard.
- Nemůžete migrovat přímo z úrovně **Basic** na **úroveň Premium** , ale můžete to provést nepřímo migrací z úrovně Basic na standard a potom z standardu na prémii v dalším kroku.

## <a name="migration-steps"></a>Kroky migrace

K procesu migrace jsou přidružené některé podmínky. Seznamte se s následujícími kroky, abyste snížili pravděpodobnost chyb. Tyto kroky popisují proces migrace a podrobné informace jsou uvedené v následujících oddílech.

1. Vytvořte nový obor názvů Premium.
1. Párovat obory názvů Standard a Premium na sebe navzájem.
1. Proveďte synchronizaci entit (kopírování) z úrovně Standard do oboru názvů Premium.
1. Potvrďte migraci.
1. Vyprázdnit entity v oboru názvů Standard pomocí názvu po migraci oboru názvů.
1. Odstraňte obor názvů Standard.

>[!IMPORTANT]
> Po potvrzení migrace přejděte ke starému standardnímu oboru názvů a vyprázdněte fronty a odběry. Po vyprázdnění zpráv je možné je odeslat do nového oboru názvů Premium, aby je mohl zpracovat aplikace příjemce. Po vyprázdnění front a předplatných doporučujeme odstranit starý obor názvů Standard.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrace pomocí Azure CLI nebo PowerShellu

Pokud chcete migrovat obor názvů Service Bus Standard do úrovně Premium pomocí Azure CLI nebo nástroje PowerShell, postupujte podle těchto kroků.

1. Vytvořte nový obor názvů Service Bus Premium. Můžete odkazovat na [šablony Azure Resource Manager](service-bus-resource-manager-namespace.md) nebo [použít Azure Portal](service-bus-create-namespace-portal.md). Nezapomeňte vybrat možnost **Premium** pro parametr **serviceBusSku** .

1. Nastavte následující proměnné prostředí, aby se zjednodušily příkazy migrace.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Pro přístup k starému standardnímu oboru názvů po migraci se použije alias nebo název (post_migration_dns_name) po migraci. Tuto akci použijte pro vyprázdnění front a odběrů a pak obor názvů odstraňte.

1. Spárujte obory názvů Standard a Premium a spusťte synchronizaci pomocí následujícího příkazu:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Stav migrace zkontrolujete pomocí následujícího příkazu:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migrace se považuje za dokončenou, když se zobrazí následující hodnoty:

    * MigrationState = "aktivní"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "úspěch"

    Tento příkaz také zobrazí konfiguraci migrace. Zkontrolujte, zda jsou hodnoty správně nastaveny. Zkontrolujte také obor názvů Premium na portálu, abyste se ujistili, že byly vytvořeny všechny fronty a témata a aby odpovídaly tomu, co existovaly v oboru názvů Standard.

1. Potvrďte migraci provedením následujícího příkazu Complete:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrace pomocí Azure Portal

Migrace pomocí Azure Portal má stejný logický tok jako migrace pomocí příkazů. Při migraci pomocí Azure Portal postupujte podle těchto kroků.

1. V **navigační** nabídce v levém podokně vyberte **migrovat na Premium** . Kliknutím na tlačítko **Začínáme** můžete pokračovat na další stránku.
    ![Cílová stránka migrace][]

1. Dokončete **Nastavení** .
   ![Nastavit obor názvů][]
   1. Vytvořte a přiřaďte obor názvů Premium pro migraci stávajícího oboru názvů Standard na.
        ![Nastavení oboru názvů – vytvoření oboru názvů Premium][]
   1. Vyberte **název následné migrace** . Tento název použijete pro přístup ke standardnímu oboru názvů po dokončení migrace.
        ![Nastavení oboru názvů – vybrat název následné migrace][]
   1. Pokračujte výběrem **příkazu ' Next '** .
1. Synchronizace entit mezi obory názvů Standard a Premium.
    ![Nastavení oboru názvů – synchronizační entity – začátek][]

   1. Vyberte **Spustit synchronizaci** a začněte synchronizovat entity.
   1. V dialogovém okně vyberte **Ano** a potvrďte a spusťte synchronizaci.
   1. Počkejte na dokončení synchronizace. Stav je k dispozici na stavovém řádku.
        ![Nastavení oboru názvů – synchronizační entity – průběh][]
        >[!IMPORTANT]
        > Pokud potřebujete migraci z nějakého důvodu přerušit, přečtěte si prosím tok přerušení v části Nejčastější dotazy v tomto dokumentu.
   1. Po dokončení synchronizace vyberte v dolní části stránky **Další** .

1. Zkontrolujte změny na stránce Souhrn. Vyberte **dokončit migraci** pro přepínání oborů názvů a dokončení migrace.
    ![Přepnout nabídku oboru názvů – přepínač][]  
    Po dokončení migrace se zobrazí stránka potvrzení.
    ![Přepnout obor názvů – úspěch][]

## <a name="caveats"></a>Upozornění

Některé funkce, které poskytuje Azure Service Bus úrovně Standard, nejsou podporovány Azure Service Bus úrovně Premium. Jedná se o návrh, protože úroveň Premium nabízí vyhrazené prostředky pro předvídatelné propustnost a latenci.

Tady je seznam funkcí, které Premium nepodporují, a jejich zmírnění –

### <a name="express-entities"></a>Expresní entity

   Expresní entity, které nepotvrzují žádná data zpráv do úložiště, nejsou v úrovni Premium podporované. Vyhrazené prostředky poskytovaly výrazné zlepšení propustnosti a zároveň zajišťují, že data jsou trvalá od libovolného podnikového systému zasílání zpráv.

   Během migrace se v oboru názvů Premium vytvoří každá z vašich expresních entit jako neexpresní entita.

   Pokud používáte šablony Azure Resource Manager (ARM), nezapomeňte z konfigurace nasazení odebrat příznak ' enableExpress ', aby byly automatizované pracovní postupy spouštěny bez chyb.

### <a name="partitioned-entities"></a>Dělené entity

   Rozdělené entity byly podporovány na úrovni Standard, aby poskytovaly lepší dostupnost při instalaci s více klienty. Díky zajištění dostupnosti vyhrazených prostředků na obor názvů na úrovni Premium už to není potřeba.

   Během migrace se v oboru názvů Premium vytvoří libovolná entita s dělenou entitou jako entita, která není rozdělená na oddíly.

   Pokud šablona ARM nastaví pro konkrétní frontu nebo téma hodnotu ' enablePartitioning ' na ' true ', bude ji zprostředkovatel ignorovat.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-happens-when-the-migration-is-committed"></a>Co se stane, když se migrace potvrdí?

Po potvrzení migrace se připojovací řetězec, který ukazuje na standardní obor názvů, nasměruje na obor názvů Premium.

Aplikace odesílatel a přijímač se z oboru názvů Standard odpojí a automaticky se znovu připojí k oboru názvů Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Co mám dělat po dokončení migrace na úrovni Standard na Premium?

Migrace Standard na Premium zajišťuje, že metadata entit, jako jsou témata, předplatná a filtry, se zkopírují z oboru názvů Standard do oboru názvů Premium. Data zprávy, která byla potvrzena na standardní obor názvů, se nekopírují z oboru názvů Standard do oboru názvů Premium.

Obor názvů Standard může obsahovat zprávy, které byly odeslány a potvrzeny během migrace. Ručně vyprázdněte tyto zprávy ze standardního oboru názvů a ručně je odešlete do oboru názvů Premium. Chcete-li ručně vyprázdnit zprávy, použijte konzolovou aplikaci nebo skript, který vyprázdní standardní entity oboru názvů pomocí názvu DNS po migraci, který jste zadali v příkazech migrace. Odešlete tyto zprávy do oboru názvů Premium, aby mohly být zpracovány přijímači.

Po vyprázdnění zpráv odstraňte standardní obor názvů.

>[!IMPORTANT]
> Po vyprázdnění zpráv z oboru názvů Standard odstraňte standardní obor názvů. To je důležité, protože připojovací řetězec, který původně odkazoval na standardní obor názvů, teď odkazuje na obor názvů Premium. Obor názvů Standard už nebudete potřebovat. Odstranění standardního oboru názvů, který jste migrovali, pomáhá snižovat pozdější nejasnost.

### <a name="how-much-downtime-do-i-expect"></a>Kolik výpadků se očekává?

Proces migrace je určený ke snížení předpokládaných výpadků aplikací. Výpadky se snižují pomocí připojovacího řetězce, který aplikace odesílatel a přijímač používá k odkazování na nový obor názvů Premium.

Výpadek, ke kterému dochází aplikace, je omezený na dobu, po kterou je potřeba aktualizovat položku DNS, aby odkazovala na obor názvů Premium. Výpadek je přibližně 5 minut.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Musím během migrace dělat změny v konfiguraci?

Ne, neexistují žádné změny kódu nebo konfigurace potřebné k provedení migrace. Připojovací řetězec, který aplikace odesílatele a přijímače používají pro přístup ke standardnímu oboru názvů, je automaticky namapován na fungovat jako alias oboru názvů Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co se stane po přerušení migrace?

Migraci můžete zrušit buď pomocí `Abort` příkazu, nebo pomocí Azure Portal.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>portál Azure

![Přerušený tok – přerušení synchronizace přerušené ][]
 ![ – přerušení dokončeno][]

Po přerušení procesu migrace přeruší proces kopírování entit (témat, odběrů a filtrů) z úrovně Standard do oboru názvů Premium a přeruší párování.

Připojovací řetězec se neaktualizoval tak, aby odkazoval na obor názvů Premium. Stávající aplikace budou i nadále fungovat stejně jako před zahájením migrace.

Neodstraňují ale entity v oboru názvů Premium ani neodstraní obor názvů Premium. Pokud jste se rozhodli Nepřesouvat vpřed s migrací, odstraňte entity ručně.

>[!IMPORTANT]
> Pokud se rozhodnete migraci přerušit, odstraňte obor názvů Premium, který jste zřídili pro migraci, abyste se za prostředky neúčtovali.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nechci zprávy vyprázdnit. Co mám udělat?

Může se stát, že aplikace odesílatele pošle zprávy a odešlou je do úložiště v oboru názvů Standard během migrace a těsně před potvrzením migrace.

Během migrace se skutečná data zprávy nebo datová část nekopírují z úrovně Standard do oboru názvů Premium. Zprávy je nutné ručně vyprázdnit a pak je odeslat do oboru názvů Premium.

Pokud však můžete provést migraci během plánované údržby nebo údržbu okna a nechcete zprávy ručně vyprázdnit a odeslat, postupujte podle následujících kroků:

1. Zastavte aplikace odesílatele. Aplikace příjemce budou zpracovávat zprávy, které jsou aktuálně ve standardním oboru názvů a vyprázdní frontu.
1. Po vyprázdnění front a předplatných ve standardním oboru názvů použijte postup, který je popsaný výše, a spusťte migraci z úrovně Standard na obor názvů Premium.
1. Po dokončení migrace můžete restartovat aplikace odesílatele.
1. Odesílatelé a příjemci se teď budou automaticky připojovat k oboru názvů Premium.

    >[!NOTE]
    > Nemusíte zastavovat aplikace příjemce pro migraci.
    >
    > Po dokončení migrace se aplikace přijímače odpojí ze standardního oboru názvů a automaticky se připojí k oboru názvů Premium.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [rozdílech mezi zasíláním zpráv na úrovni Standard a Premium](./service-bus-premium-messaging.md).
* Seznamte se s [aspekty vysoké dostupnosti a Geo-Disaster pro obnovení Service Bus Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Cílová stránka migrace]: ./media/service-bus-standard-premium-migration/1.png
[Nastavit obor názvů]: ./media/service-bus-standard-premium-migration/2.png
[Nastavení oboru názvů – vytvoření oboru názvů Premium]: ./media/service-bus-standard-premium-migration/3.png
[Nastavení oboru názvů – vybrat název následné migrace]: ./media/service-bus-standard-premium-migration/4.png
[Nastavení oboru názvů – synchronizační entity – začátek]: ./media/service-bus-standard-premium-migration/5.png
[Nastavení oboru názvů – synchronizační entity – průběh]: ./media/service-bus-standard-premium-migration/8.png
[Přepnout nabídku oboru názvů – přepínač]: ./media/service-bus-standard-premium-migration/9.png
[Přepnout obor názvů – úspěch]: ./media/service-bus-standard-premium-migration/12.png

[Přerušení toku – přerušení synchronizace]: ./media/service-bus-standard-premium-migration/abort1.png
[Přerušení toku – přerušení dokončeno]: ./media/service-bus-standard-premium-migration/abort3.png
