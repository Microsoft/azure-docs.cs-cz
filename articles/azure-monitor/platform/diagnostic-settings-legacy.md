---
title: Shromažďování protokolu aktivit Azure s diagnostickým nastavením – Azure Monitor | Dokumenty společnosti Microsoft
description: Pomocí diagnostických nastavení přepošlete protokoly aktivit Azure do protokolů monitorování Azure, úložiště Azure nebo centra událostí Azure.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096905"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Aktualizace na shromažďování a export protokolu aktivit Azure
[Protokol aktivit Azure](platform-logs-overview.md) je protokol [platformy,](platform-logs-overview.md) který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Metoda odesílání položek protokolu aktivit do [centra událostí nebo účtu úložiště](activity-log-export.md) nebo do [pracovního prostoru Log Analytics](activity-log-collect.md) se změnila na použití [diagnostických nastavení](diagnostic-settings.md). Tento článek popisuje rozdíl mezi metodami a jak vymazat starší nastavení v rámci přípravy na změnu nastavení diagnostiky.


## <a name="differences-between-methods"></a>Rozdíly mezi metodami

### <a name="advantages"></a>Výhody
Použití diagnostického nastavení má následující výhody oproti současným metodám:

- Konzistentní metoda pro shromažďování všech protokolů platformy.
- Shromažďovat protokol aktivit přes více předplatných a klientů.
- Kolekce filtrů shromažďovat pouze protokoly pro určité kategorie.
- Shromažďovat všechny kategorie protokolu aktivit. Některé kategorie nejsou shromažďovány pomocí starší metody.
- Rychlejší latence pro přihlášce protokolu. Předchozí metoda má latenci přibližně 15 minut, zatímco nastavení diagnostiky přidá pouze asi 1 minutu.

### <a name="considerations"></a>Požadavky
Před povolením této funkce zvažte následující podrobnosti kolekce protokolu aktivit pomocí diagnostických nastavení.

- Nastavení uchovávání informací pro shromažďování protokolu aktivit do úložiště Azure bylo odebráno, což znamená, že data budou uložena po neomezenou dobu, dokud je neodeberete.
- V současné době můžete pouze vytvořit nastavení diagnostiky na úrovni předplatného pomocí portálu Azure. Chcete-li použít jiné metody, jako je prostředí PowerShell nebo CLI, můžete vytvořit šablonu Správce prostředků.


### <a name="differences-in-data"></a>Rozdíly v datech
Diagnostická nastavení shromažďují stejná data jako předchozí metody používané ke shromažďování protokolu aktivit s následujícími aktuálními rozdíly:

Následující sloupce byly odebrány. Náhrada za tyto sloupce jsou v jiném formátu, takže budete muset upravit dotazy protokolu, které je používají. Ve schématu se stále mohou zobrazit odebrané sloupce, ale nebudou naplněny daty.

| Odstraněný sloupec | Náhradní sloupec |
|:---|:---|
| Status aktivity    | ActivityStatusValue    |
| ActivitySubstatus | Hodnota ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

Byl přidán nový sloupec:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> V některých případech mohou být hodnoty v těchto sloupcích velkými písmeny. Pokud máte dotaz, který obsahuje tyto sloupce, měli byste použít [=~ operátor](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) provést porovnání malá a velká písmena.

## <a name="work-with-legacy-settings"></a>Práce se staršími nastaveními
Starší nastavení pro shromažďování protokolu aktivit bude i nadále fungovat, pokud nechcete nahradit diagnostické nastavení. Ke správě profilu protokolu pro odběr použijte následující metodu.

1. Z nabídky **Azure Monitor** na webu Azure Portal vyberte **protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)


Podrobnosti o používání starších metod kolekce naleznete v následujících článcích.

- [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitoru](activity-log-collect.md)
- [Shromažďování protokolů aktivit Azure do Azure Monitoru napříč tenanty Azure Active Directory](activity-log-collect-tenants.md)
- [Export protokolu aktivit Azure do úložiště nebo Centra událostí Azure](activity-log-export.md)

## <a name="disable-existing-settings"></a>Zakázání existujících nastavení
Před povolením pomocí diagnostických nastavení byste měli zakázat existující kolekci aktivity. Povolení obou může mít za následek duplicitní data.

### <a name="disable-collection-into-log-analytics-workspace"></a>Zakázat kolekci do pracovního prostoru Log Analytics

1. Otevřete nabídku **pracovních prostorů Analýzy protokolů** na portálu Azure a vyberte pracovní prostor pro shromažďování protokolu aktivit.
2. V části **Zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte protokol aktivit **Azure**.
3. Klikněte na předplatné, které chcete odpojit.
4. Po zobrazení příkazu K potvrzení volby klikněte na **tlačítko Odpojit** a potom **na Ano.**

### <a name="disable-log-profile"></a>Zakázat profil protokolu

1. Pomocí postupu popsaného v [části Práce se staršími nastaveními](#work-with-legacy-settings) otevřete starší nastavení.
2. Zakažte libovolnou aktuální kolekci do center úložiště nebo událostí.



## <a name="activity-log-monitoring-solution"></a>Řešení monitorování protokolu aktivit
Řešení monitorování Azure Log Analytics obsahuje více dotazů protokolu a zobrazení pro analýzu záznamů protokolu aktivit v pracovním prostoru Log Analytics. Toto řešení používá data protokolu shromážděná v pracovním prostoru Log Analytics a bude pokračovat v práci bez jakýchkoli změn, pokud shromažďujete protokol aktivit pomocí diagnostických nastavení. Podrobnosti o tomto řešení najdete v [tématu Řešení monitorování protokolů protokolů aktivit.](activity-log-collect.md#activity-logs-analytics-monitoring-solution)

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](../../azure-resource-manager/management/view-activity-logs.md)
* [Další informace o diagnostických nastaveních](diagnostic-settings.md)
