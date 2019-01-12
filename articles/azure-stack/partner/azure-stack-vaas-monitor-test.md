---
title: Monitorování a správa testů na portálu Azure Stack VaaS | Dokumentace Microsoftu
description: Monitorování a správa testů na portálu Azure Stack VaaS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 4107b18046b57d5b02fcd9d88be1752922735a08
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245578"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorování a správa testů na portálu VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Po naplánování testů proti vašeho řešení Azure Stack, se začne ověření jako služba (VaaS) hlásí stav spuštění testu. Tyto informace jsou k dispozici na portálu VaaS spolu s akce, jako je změna plánu a ruší se testy.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Přejděte na stránku Souhrn testů pracovního postupu

1. Na řídicím panelu řešení vyberte existující řešení, která má alespoň jeden pracovní postup.

    ![Dlaždice pracovního postupu](media/tile_all-workflows.png)

1. Vyberte **spravovat** na dlaždici pracovního postupu. Na další stránce uvedeny vytvořených pro vybrané řešení pracovních postupů.

1. Vyberte název pracovního postupu a otevřete její souhrn testu.

## <a name="change-workflow-parameters"></a>Změna parametrů pracovního postupu

Každý typ pracovního postupu umožňuje upravit [parametry testu](azure-stack-vaas-parameters.md#test-parameters) zadali při vytvoření pracovního postupu.

1. Na stránce Souhrn testů, vyberte **upravit** tlačítko.

1. Zadejte nové hodnoty podle [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](azure-stack-vaas-parameters.md).

1. Vyberte **odeslat** uložit hodnoty.

> [!NOTE]
> V **průchodu testů** pracovního postupu, je potřeba dokončit výběr testů a přejděte na stránku revize až pak můžete uložit nové hodnoty parametrů.

### <a name="add-tests-test-pass-only"></a>Přidat testy (pouze průchodu testů)

V **průchodu testů** pracovní postupy, jak **přidat testy** a **upravit** tlačítka umožňují naplánovat nové testy v pracovním postupu.

> [!TIP]
> Vyberte **přidat testy** pouze chcete naplánovat nové testy a není potřeba upravit parametry pro **průchodu testů** pracovního postupu.

## <a name="managing-test-instances"></a>Správa testu instancí

Pro spuštění neoficiální (například **průchodu testů** pracovního postupu), na stránce souhrnu testy uvádí testy naplánované proti řešení Azure Stack.

Pro spuštění oficiální (například **ověření** pracovních postupů), na stránce souhrnu testy seznamy testů, které jsou potřebné k dokončení ověření řešení Azure Stack. Testy pro ověření jsou naplánovány na této stránce.

Každá instance plánovaný test se zobrazí následující informace:

| Sloupec | Popis |
| --- | --- |
| Název testu | Název a verzi testu. |
| Kategorie | Účel testu. |
| Vytvořeno | Čas naplánovala testu. |
| Spuštěno | Čas, ve kterém test spustil provádění pořadí. |
| Doba trvání | Délka času, který spustil test. |
| Status | Stát nebo výsledek testu. Předběžné spuštění nebo probíhá stavy jsou: `Pending`, `Running`. Terminálu stavy jsou: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Název agenta | Název agenta, který spustil test. |
| Celkový počet operací | Celkový počet operací se pokusil během testu. |
| Úspěšné operace | Počet operací, které byly úspěšné během testu. |
|  Neúspěšné operace | Počet operací, které selhaly během testu. |

### <a name="actions"></a>Akce

Každá instance testu obsahuje seznam dostupných akcí, které můžete provést po kliknutí na její kontextovou nabídku **[...]**  v tabulce testovací instance.

#### <a name="view-information-about-the-test-definition"></a>Zobrazit informace o definici testu

Vyberte **zobrazení informací o** v místní nabídce, chcete-li zobrazit obecné informace o definici testu. To je sdílen každý testovací instance se stejným názvem a verzí.

| Vlastnost testu | Popis |
| -- | -- |
| Název testu | Název testu. |
| Testovací verze | Verzi testu. |
| Vydavatel | Vydavatel testu. |
| Kategorie |  Účel testu. |
| Cíl služby | Azure Stack služby testování. |
| Popis | Popis testu. |
| Odhadovaná doba trvání (minuty) | Očekávanou dobu běhu testu. |
| Odkazy | Příslušné informace o testu nebo body kontaktu. |

#### <a name="view-test-instance-parameters"></a>Zobrazit testovací instance parametry

Vyberte **zobrazit parametry** v místní nabídce zobrazíte parametry zadané pro testovací instance v naplánovaný čas. Citlivé řetězce, jako jsou hesla, nejsou zobrazeny. Tato akce je dostupná pouze pro testy, které jste naplánovali.

Toto okno obsahuje následující metadata pro všechny instance testu:

| Vlastnost instance testu | Popis |
| -- | -- |
| Název testu | Název testu. |
| Testovací verze | Verzi testu. |
| ID instance testu | Identifikátor GUID identifikující konkrétní instanci testu. |

#### <a name="view-test-instance-operations"></a>Zobrazit testovací instance operace

Vyberte **zobrazit operace** z kontextu nabídku, chcete-li zobrazit podrobný stav operace provádí během testu. Tato akce je dostupná pouze pro testy, které jste naplánovali.

![zobrazení operací](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Stažení protokolů pro instanci test byl dokončen

Vyberte **stažení protokolů** v místní nabídce ke stažení `.zip` souboru výstupu protokolů během byly spuštění testu. Tato akce je dostupná pouze pro testy, které dokončily, to znamená, test se stavem buď `Cancelled`, `Failed`, `Aborted`, nebo `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Testovací instance plánovanou zkoušku přeplánovat nebo naplánovat v testu

Plánování testů ze stránky správy závisí na typu pracovního postupu v části spuštění testu.

##### <a name="test-pass-workflow"></a>Pracovní postup průchodu testu

V pracovním postupu průchodu testů **přeplánování** testovací instance opakovaně používá stejnou sadu parametrů jako původní testovací instance a *nahradí* původní výsledek, včetně jeho protokolů. Je potřeba znovu zadat citlivé řetězce, jako jsou hesla, když jste plánovanou zkoušku přeplánovat.

1. Vyberte **plánovanou zkoušku přeplánovat** z místní nabídky otevřete příkazový řádek pro přeplánování testovací instance.

1. Zadejte všechny příslušné parametry.

1. Vyberte **odeslat** plánovanou zkoušku přeplánovat testovací instance a nahraďte existující instanci.

##### <a name="validation-workflows"></a>Ověřovací pracovní postupy

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Zrušit testovací instance

Naplánovaný test může zrušit, pokud je jeho stav `Pending` nebo `Running`.  

1. Vyberte **zrušit** v místní nabídce otevřete výzvu pro zrušení instance testu.

1. Vyberte **odeslat** zrušíte testovací instance.

## <a name="next-steps"></a>Další postup

- [Řešení potíží s ověření jako služba](azure-stack-vaas-troubleshoot.md)
