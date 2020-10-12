---
title: Zabránit anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB
titleSuffix: Azure Storage
description: Naučte se analyzovat anonymní požadavky na účet úložiště a zabránit anonymnímu přístupu pro celý účet úložiště nebo pro jednotlivé kontejnery.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 0ed8b04353c50bff53d074ebdb1efa2a286c8e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086568"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Zabránit anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB

Anonymní veřejný přístup pro čtení kontejnerů a objektů BLOB v Azure Storage je pohodlný způsob, jak sdílet data, ale může také představovat bezpečnostní riziko. Je důležité spravovat anonymní přístup v rozumném zájmu a pochopit, jak vyhodnocovat anonymní přístup k datům. Provozní složitost, lidská chyba nebo škodlivý útok na data, která jsou veřejně přístupná, můžou vést k nákladným porušením dat. Společnost Microsoft doporučuje povolit anonymní přístup pouze v případě, že je to nutné pro váš scénář aplikace.

Ve výchozím nastavení jsou veřejný přístup k datům objektů BLOB vždycky zakázaný. Výchozí konfigurace účtu úložiště ale umožňuje uživateli s příslušnými oprávněními nakonfigurovat veřejný přístup ke kontejnerům a objektům blob v účtu úložiště. Pro zvýšení zabezpečení můžete zakázat veškerý veřejný přístup k účtu úložiště bez ohledu na nastavení veřejného přístupu pro jednotlivé kontejnery. Zakázáním veřejného přístupu k účtu úložiště znemožníte uživateli povolit veřejný přístup ke kontejneru v účtu. Microsoft doporučuje, abyste zakázali veřejný přístup k účtu úložiště, pokud to váš scénář nevyžaduje. Nepovolení veřejného přístupu pomáhá zabránit narušením dat způsobeným nežádoucím anonymním přístupem.

Když zakážete přístup k veřejnému objektu BLOB pro účet úložiště, Azure Storage odmítne všechny anonymní požadavky na tento účet. Po povolení veřejného přístupu pro účet není možné kontejnery tohoto účtu následně nakonfigurovat pro veřejný přístup. Všechny kontejnery, které již byly nakonfigurovány pro veřejný přístup, již nebudou přijímat anonymní požadavky. Další informace najdete v tématu [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](anonymous-read-access-configure.md).

V tomto článku se dozvíte, jak pomocí architektury přetáhnout (detekce a napravit audit-audit) průběžně spravovat veřejný přístup k účtům úložiště.

## <a name="detect-anonymous-requests-from-client-applications"></a>Detekovat anonymní požadavky z klientských aplikací

Když zakážete veřejný přístup pro čtení pro účet úložiště, riskujete tím odmítnutí požadavků na kontejnery a objekty blob, které jsou aktuálně nakonfigurované pro veřejný přístup. Nepovolení veřejného přístupu pro účet úložiště přepíše nastavení veřejného přístupu pro jednotlivé kontejnery v tomto účtu úložiště. Pokud pro účet úložiště není povolený veřejný přístup, všechny budoucí anonymní požadavky na daný účet selžou.

Aby bylo možné pochopit, jakým způsobem může mít veřejný přístup vliv na klientské aplikace, společnost Microsoft doporučuje povolit protokolování a metriky pro tento účet a analyzovat vzory anonymních požadavků během časového intervalu. Pomocí metrik určete počet anonymních požadavků na účet úložiště a pomocí protokolů určete, ke kterým kontejnerům se má anonymní přístup.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Monitorování anonymních požadavků pomocí Průzkumník metrik

Pokud chcete sledovat anonymní požadavky na účet úložiště, použijte Azure Průzkumník metrik v Azure Portal. Další informace o Průzkumník metrik najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md).

Pomocí těchto kroků můžete vytvořit metriku, která bude sledovat anonymní požadavky:

1. Na webu Azure Portal přejděte na svůj účet úložiště. V části **monitorování** vyberte **metriky**.
1. Vyberte **Přidat metriku**. V dialogovém okně **metrika** zadejte následující hodnoty:
    1. Ponechte pole obor nastavené na název účtu úložiště.
    1. Nastavte **obor názvů metriky** na *BLOB*. Tato metrika hlásí požadavky pouze na úložiště objektů BLOB.
    1. Nastavte pole **metrika** na hodnotu *Transactions*.
    1. Nastavte **agregační** pole na *součet*.

    V nové metrikě se zobrazí součet počtu transakcí s úložištěm objektů BLOB v daném časovém intervalu. Výsledná metrika se zobrazí, jak je znázorněno na následujícím obrázku:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat metriku tak, aby součet transakcí objektů BLOB":::

1. V dalším kroku vyberte tlačítko **Přidat filtr** a vytvořte filtr pro tuto metriku pro anonymní požadavky.
1. V dialogovém okně **Filtr** zadejte následující hodnoty:
    1. Nastavte hodnotu **vlastnosti** na *ověřování*.
    1. Nastavte pole **operátor** na znaménko rovná se (=).
    1. Pole **hodnoty** nastavte na *Anonymous (anonymní*).
1. V pravém horním rohu vyberte časový interval, za který chcete metriku zobrazit. Můžete také určit, jak podrobně má být agregace požadavků, zadáním intervalů kdekoli od 1 minuty po 1 měsíc.

Po nakonfigurování metriky se v grafu začnou zobrazovat anonymní požadavky. Následující obrázek ukazuje anonymní požadavky agregované za posledních třicet minut.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat metriku tak, aby součet transakcí objektů BLOB":::

Můžete také nakonfigurovat pravidlo výstrahy, které vás upozorní, když se na váš účet úložiště vytvoří určitý počet anonymních požadavků. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analýza protokolů pro identifikaci kontejnerů, které přijímají anonymní požadavky

Protokoly Azure Storage zaznamenávají informace o požadavcích provedených proti účtu úložiště, včetně způsobu autorizace žádosti. Protokoly můžete analyzovat a určit, které kontejnery přijímají anonymní požadavky.

Pokud chcete protokolovat požadavky na účet Azure Storage za účelem vyhodnocení anonymních požadavků, můžete použít Azure Storage přihlášení Azure Monitor (Preview). Další informace najdete v tématu [monitorování Azure Storage](../common/monitor-storage.md).

Azure Storage přihlášení Azure Monitor podporuje použití dotazů protokolu k analýze dat protokolu. K dotazování protokolů můžete použít pracovní prostor Azure Log Analytics. Další informace o dotazech protokolu najdete v tématu [kurz: Začínáme s Log Analytics dotazy](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Vytvoření nastavení diagnostiky v Azure Portal

Pokud chcete protokolovat data Azure Storage pomocí Azure Monitor a analyzovat je pomocí Azure Log Analytics, musíte nejdřív vytvořit nastavení diagnostiky, které indikuje, jaké typy požadavků a pro které služby úložiště chcete data protokolovat. Chcete-li v Azure Portal vytvořit nastavení diagnostiky, postupujte takto:

1. Zaregistrujte se [Azure Storage přihlašování Azure monitor ve verzi Preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Vytvořte nový pracovní prostor Log Analytics v předplatném, které obsahuje váš Azure Storage účet. Po nakonfigurování protokolování účtu úložiště budou protokoly k dispozici v pracovním prostoru Log Analytics. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části monitorování vyberte **nastavení diagnostiky (Preview)**.
1. Vyberte **objekt BLOB** pro protokolování požadavků provedených proti úložišti objektů BLOB.
1. Vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název pro nastavení diagnostiky.
1. V části **Podrobnosti o kategorii**v části **protokol** vyberte typy požadavků, které se mají protokolovat. Všechny anonymní požadavky budou přečteny, takže vyberte **StorageRead** pro zachycení anonymních požadavků.
1. V části **Podrobnosti o cíli**vyberte **Odeslat do Log Analytics**. Vyberte své předplatné a Log Analytics pracovní prostor, který jste vytvořili dříve, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat metriku tak, aby součet transakcí objektů BLOB":::

Po vytvoření nastavení diagnostiky se požadavky na účet úložiště následně protokolují podle tohoto nastavení. Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik prostředků v Azure](../../azure-monitor/platform/diagnostic-settings.md).

Odkaz na pole, která jsou k dispozici v Azure Storage protokoly v Azure Monitor, najdete v tématu [protokoly prostředků (Preview)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Dotazy na protokoly pro anonymní požadavky

Protokoly Azure Storage v Azure Monitor zahrnují typ ověřování, který se použil k vytvoření požadavku na účet úložiště. V dotazu protokolu vyfiltrujte vlastnost **AuthenticationType** , aby se zobrazily anonymní požadavky.

Pokud chcete pro anonymní požadavky na úložiště objektů BLOB načíst protokoly za posledních 7 dní, otevřete pracovní prostor Log Analytics. Dále vložte následující dotaz do nového dotazu protokolu a spusťte ho:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Můžete také nakonfigurovat pravidlo výstrahy na základě tohoto dotazu, které vás upozorní na anonymní požadavky. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Opravit anonymní veřejný přístup

Po vyhodnocení anonymních požadavků na kontejnery a objekty blob ve vašem účtu úložiště můžete provést akci pro omezení nebo zabránění veřejnému přístupu. Pokud můžou být některé kontejnery v účtu úložiště dostupné pro veřejný přístup, můžete nakonfigurovat nastavení veřejného přístupu pro každý kontejner v účtu úložiště. Tato možnost poskytuje nejpřesnější kontrolu nad veřejným přístupem. Další informace najdete v tématu [Nastavení úrovně veřejného přístupu pro kontejner](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Pro zvýšení zabezpečení můžete zakázat veřejný přístup pro celý účet úložiště. Nastavení veřejného přístupu pro účet úložiště přepíše jednotlivá nastavení kontejnerů v daném účtu. Když zakážete veřejný přístup k účtu úložiště, všechny kontejnery, které jsou nakonfigurované tak, aby povolovaly veřejný přístup, už nebudou přístupné anonymně. Další informace najdete v tématu [Povolení nebo zakázání veřejného přístupu pro čtení pro účet úložiště](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Pokud váš scénář vyžaduje, aby určité kontejnery byly dostupné pro veřejný přístup, může být vhodné přesunout tyto kontejnery a jejich objekty blob do účtů úložiště, které jsou vyhrazené pro veřejný přístup. Pak můžete zakázat veřejný přístup pro všechny ostatní účty úložiště.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Ověřte, že veřejný přístup k objektu BLOB není povolený.

Pokud chcete ověřit, jestli je veřejný přístup k určitému objektu BLOB zakázaný, můžete se pokusit o stažení objektu BLOB přes jeho adresu URL. Pokud se stahování podaří, je objekt BLOB stále veřejně dostupný. Pokud objekt BLOB není veřejně přístupný, protože pro účet úložiště je povolený veřejný přístup, zobrazí se chybová zpráva s oznámením, že v tomto účtu úložiště není povolený veřejný přístup.

Následující příklad ukazuje, jak použít PowerShell k pokusu o stažení objektu BLOB prostřednictvím jeho adresy URL. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Ověřte, že změna nastavení veřejného přístupu kontejneru není povolená.

Pokud chcete ověřit, že nastavení veřejného přístupu kontejneru se nedá změnit, když je pro účet úložiště zakázaný veřejný přístup, můžete se pokusit změnit nastavení. Změna nastavení veřejného přístupu kontejneru se nezdaří, pokud pro účet úložiště není povolený veřejný přístup.

Následující příklad ukazuje, jak použít PowerShell k pokusu o změnu nastavení veřejného přístupu kontejneru. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Ověřte, že vytvoření kontejneru s povoleným veřejným přístupem není povolené.

Pokud pro účet úložiště není povolený veřejný přístup, nebudete moct vytvořit nový kontejner s povoleným veřejným přístupem. K ověření se můžete pokusit vytvořit kontejner s povoleným veřejným přístupem.

Následující příklad ukazuje, jak použít PowerShell k pokusu o vytvoření kontejneru s povoleným veřejným přístupem. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Podívejte se na nastavení veřejného přístupu pro víc účtů.

Pokud chcete zjistit nastavení veřejného přístupu v rámci sady účtů úložiště s optimálním výkonem, můžete použít Průzkumníka Azure Resource graphu v Azure Portal. Další informace o používání Průzkumníka grafů prostředků najdete v tématu [rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](/azure/governance/resource-graph/first-query-portal).

Když spustíte následující dotaz, v Průzkumníku grafu prostředků se vrátí seznam účtů úložiště a v každém účtu se zobrazí nastavení veřejného přístupu:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

## <a name="use-azure-policy-to-audit-for-compliance"></a>Auditovat dodržování předpisů pomocí Azure Policy

Pokud máte velký počet účtů úložiště, možná budete chtít provést audit, abyste se ujistili, že tyto účty jsou nakonfigurované tak, aby se zabránilo přístupu k veřejnosti. Pokud chcete auditovat sadu účtů úložiště pro dodržování předpisů, použijte Azure Policy. Azure Policy je služba, kterou můžete použít k vytváření, přiřazování a správě zásad, které používají pravidla pro prostředky Azure. Azure Policy vám pomůže zajistit, aby tyto prostředky vyhovovaly vašim firemním standardům a smlouvám o úrovni služeb. Další informace najdete v tématu [přehled Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Vytvoření zásady pomocí efektu auditu

Azure Policy podporuje efekty, které určují, co se stane, když se pravidlo zásad vyhodnocuje proti prostředku. Při auditu se vytvoří upozornění, pokud prostředek není v souladu s předpisy, ale nezastaví požadavek. Další informace o účincích najdete v tématu [pochopení Azure Policy efektů](../../governance/policy/concepts/effects.md).

Pokud chcete vytvořit zásadu s účinkem auditu pro nastavení veřejného přístupu pro účet úložiště s Azure Portal, postupujte takto:

1. V Azure Portal přejděte do služby Azure Policy.
1. V části **vytváření obsahu** vyberte **definice**.
1. Pokud chcete vytvořit novou definici zásady, vyberte **Přidat definici zásady** .
1. V poli **umístění definice** vyberte tlačítko **Další** a určete, kde se nachází prostředek zásad auditu.
1. Zadejte název zásady. Volitelně můžete zadat popis a kategorii.
1. V části **pravidlo zásad**přidejte do části **policyRule** následující definici zásady.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Uložte zásady.

### <a name="assign-the-policy"></a>Přiřazení zásady

V dalším kroku přiřaďte zásadu k prostředku. Rozsah zásad odpovídá tomuto prostředku a jakýmkoli prostředkům pod ním. Další informace o přiřazení zásad najdete v tématu [Azure Policy struktura přiřazení](../../governance/policy/concepts/assignment-structure.md).

Chcete-li přiřadit zásadu k Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte do služby Azure Policy.
1. V části **vytváření obsahu** vyberte **přiřazení**.
1. Vyberte **přiřadit zásadu** a vytvořte nové přiřazení zásady.
1. V poli **obor** vyberte obor přiřazení zásady.
1. V poli **definice zásady** vyberte tlačítko **Další** a pak v seznamu vyberte zásadu, kterou jste definovali v předchozí části.
1. Zadejte název přiřazení zásady. Popis je volitelný.
1. Nechte **vynucení zásad** nastavené na *povoleno*. Toto nastavení nemá žádný vliv na zásady auditu.
1. Vyberte možnost **zkontrolovat + vytvořit** a vytvořte přiřazení.

### <a name="view-compliance-report"></a>Zobrazit sestavu dodržování předpisů

Po přiřazení zásady můžete zobrazit sestavu dodržování předpisů. Sestava dodržování předpisů pro zásady auditu poskytuje informace o tom, které účty úložiště nejsou v souladu se zásadami. Další informace najdete v tématu [získání dat o dodržování zásad](../../governance/policy/how-to/get-compliance-data.md).

Po vytvoření přiřazení zásad může trvat několik minut, než se sestava dodržování předpisů stane dostupnou.

Chcete-li zobrazit sestavu dodržování předpisů v Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte do služby Azure Policy.
1. Vyberte možnost **dodržování předpisů**.
1. Vyfiltrujte výsledky pro název přiřazení zásady, které jste vytvořili v předchozím kroku. V této sestavě se zobrazuje počet prostředků, které nejsou v souladu se zásadami.
1. Můžete přejít k podrobnostem sestavy, kde najdete další podrobnosti, včetně seznamu účtů úložiště, které nedodržují předpisy.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat metriku tak, aby součet transakcí objektů BLOB":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Použití Azure Policy k vymáhání oprávněného přístupu

Azure Policy podporuje zásady správného řízení cloudu tím, že zajistí, že prostředky Azure vyhovují požadavkům a standardům. Pokud chcete zajistit, aby účty úložiště ve vaší organizaci povolovaly jenom autorizované požadavky, můžete vytvořit zásadu, která zabrání vytvoření nového účtu úložiště s nastavením veřejného přístupu, které umožňuje anonymní požadavky. Tato zásada taky zabrání všem změnám v konfiguraci existujícího účtu, pokud nastavení veřejného přístupu pro tento účet není kompatibilní se zásadami.

Zásada vynucení používá efekt odepřít, který brání žádosti, která by vytvořila nebo upravila účet úložiště, aby povolovala veřejný přístup. Další informace o účincích najdete v tématu [pochopení Azure Policy efektů](../../governance/policy/concepts/effects.md).

Chcete-li vytvořit zásadu s efektem odepření pro nastavení veřejného přístupu, které povoluje anonymní požadavky, postupujte podle kroků popsaných v tématu [použití Azure Policy k auditování dodržování předpisů](#use-azure-policy-to-audit-for-compliance), ale v části **policyRule** v definici zásady zadejte následující kód JSON:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Když vytvoříte zásadu s použitím efektu odepřít a přiřadíte ji k oboru, uživatel nemůže vytvořit účet úložiště, který umožňuje veřejný přístup. Ani nemůže uživatel provádět změny konfigurace stávajícího účtu úložiště, který v současné době povoluje veřejný přístup. Při pokusu o provedení dojde k chybě. Nastavení veřejného přístupu pro účet úložiště musí být nastavené na **hodnotu false** , aby bylo možné pokračovat v vytváření nebo konfiguraci účtu.

Následující obrázek ukazuje chybu, ke které dochází, když se pokusíte vytvořit účet úložiště, který umožňuje veřejný přístup (výchozí nastavení pro nový účet), když zásada s efektem odepření vyžaduje, aby byl veřejný přístup zakázán.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat metriku tak, aby součet transakcí objektů BLOB":::

## <a name="next-steps"></a>Další kroky

- [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](anonymous-read-access-configure.md)
- [Anonymní přístup k veřejným kontejnerům a objektům blob pomocí .NET](anonymous-read-access-client.md)
