---
title: Monitorování testu s ověřováním služby Azure Stack jako služba | Dokumentace Microsoftu
description: Monitorování testu s ověřováním služby Azure Stack jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 553d2a0e4bf6b23f5d8ab200f533d9245bf72d36
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286580"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Monitor testu s ověřováním služby Azure Stack jako služba

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Spuštění testu je možné monitorovat zobrazením **operace** stránka sady testů, které jsou v průběhu nebo byla dokončena. Tato stránka obsahuje podrobnosti o stavu testu a jeho operace.

## <a name="monitor-a-test"></a>Monitor testu

1. Vyberte řešení.

2. Vyberte **spravovat** na kteroukoli dlaždici pracovního postupu.

3. Klikněte na pracovním postupu, otevřete její stránku Souhrn testu.

4. Rozbalte položku místní nabídky **[...]**  pro všechny testovací sady instance.

5. Vyberte **zobrazit operace**

![Alternativní text](media\image4.png)

Pro testy, které jsou spuštěny, protokoly je kliknutím na stáhnout na stránce souhrnu testovací **stažení protokolů** v kontextové nabídce testu **[...]** . Partneři služby Azure Stack můžete použít tyto protokoly pro ladění problémů pro neúspěšné testy.

## <a name="open-the-test-pass-summary"></a>Otevřít souhrn průchodu testu

1. Otevřete portál. 
2. Vyberte název existujícího řešení, která obsahuje dříve běhu ani do plánovaných testů.

    ![Správa úspěšných testů](media/managetestpasses.png)

3. Vyberte **spravovat** v **úspěšných testů** panelu.
4. Vyberte průchodu testu otevřete souhrn průchodu testu. Název testu, můžete zkontrolovat datum vytvoření, spuštění, jak dlouho trvalo testu a výsledek (úspěšných nebo neúspěšných).
5. Vyberte [ **...  .** ].

### <a name="test-pass-summary"></a>Souhrn průchodu testu

| Sloupec | Popis |
| --- | --- |
| Název testu | Název testu. Odkazuje na počet ověření. |
| Vytvořeno | Čas, kdy byla vytvořena průchodu testu. |
| Spuštěno | Čas, kdy se poslední test běžel. |
| Doba trvání | Čas dobu, jakou trvalo průchodu testů ke spuštění. |
| Status | Výsledek (Succeeded nebo Failed) pro průchod rest. |
| Název agenta | Plně kvalifikovaný název domény agenta. |
| Celkový počet operací | Celkový počet operací se pokusil při průchodu testu. |
| Úspěšné operace | Počet operací, které předávají při průchodu testu. |
|  Neúspěšné operace | Počet operací, které se nezdařilo. |

### <a name="group-columns-in-the-test-pass-summary"></a>Skupiny sloupců v testu předat souhrnu

Můžete vybrat a přetáhněte sloupec do hlavičky k vytvoření skupiny na základě hodnoty sloupce.

## <a name="reschedule-a-test"></a>Plánovanou zkoušku přeplánovat testu

1. [Otevřít souhrn průchodu testu](#open-the-test-pass-summary).
2. Vyberte **plánovanou zkoušku přeplánovat** přeplánovat průchodu testu.
3. Zadejte heslo správce cloudu pro vaši instanci služby Azure Stack.
4. Zadejte řetězec připojení úložiště diagnostiky, které jste definovali při nastavování účtu.
5. Plánovanou zkoušku přeplánujte testu.

## <a name="cancel-a-test"></a>Zrušení testu

1. [Otevřít souhrn průchodu testu](#open-the-test-pass-summary).
2. Vyberte **zrušit**.

## <a name="get-test-information"></a>Získání informací o testu

1. [Otevřít souhrn průchodu testu](#open-the-test-pass-summary).
2. Vyberte **zobrazení informací o** přeplánovat průchodu testu.

**Informace o testu**

| Název | Popis |
| -- | -- |
| Název testu | Název testu, například výrobce OEM aktualizaci na RC ověření Azure Stack 1806. |
| Testovací verze | Verze testu, například 5.1.4.0. |
| Vydavatel | Test vydavatele, jako je Microsoft. |
| Kategorie | Kategorie testů, jako například **funkční** nebo **spolehlivost**. |
| Cíl služby | Služby, testování, jako je například virtuálních počítačů |
| Popis | Popis testu. |
| Odhadovaná doba trvání (minuty) | Dlouhá doba v minutách, po kterou trvalo spuštění testu. |
| Odkazy | Odkaz na sledování problémů Githubu. |

## <a name="get-test-parameters"></a>Získat testovací parametry

1. [Otevřít souhrn průchodu testu](#open-the-test-pass-summary).
2. Vyberte **zobrazit parametry** přeplánovat průchodu testu.

**Parametry**

| Název | Popis |
| -- | -- |
| Název testu | Název testu, například oemupdate1806test. |
| Testovací verze | Verze rest, například 5.1.4.0. |
| ID instance testu | Identifikátor GUID identifikující konkrétní instanci testu, například 20b20645 b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | Název účtu používat jako správce cloudu, například **cloudadmin**. |
| DiagnosticsContainerName | ID kontejneru diagnostiky, například 04dd3815 5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Získání operací testování s

1. [Otevřít souhrn průchodu testu](#open-the-test-pass-summary).
2. Vyberte **zobrazit operace** přeplánovat průchodu testu. Otevře se podokno Souhrn operací.

## <a name="get-test-logs"></a>Získání protokolů testu

1. [Otevřít souhrn průchodu testu](#open-the-test-pass-summary).
2. Vyberte **stažení protokolů** přeplánovat průchodu testu.  
    Soubor zip s názvem ReleaseYYYY-MM-DD.zip obsahující protokoly, soubory ke stažení.

## <a name="next-steps"></a>Další postup

- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
