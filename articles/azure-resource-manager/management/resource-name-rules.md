---
title: Omezení pojmenování prostředků
description: Zobrazuje pravidla a omezení pro pojmenování prostředků Azure.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 580b7efd26d5729baf236b59452f63483c003e44
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366226"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Omezení a pravidla pojmenování prostředků Azure

Tento článek shrnuje pravidla pojmenování a omezení pro prostředky Azure. Doporučení týkající se pojmenování prostředků naleznete v [tématu Připraveno: Doporučené konvence pojmenování a označování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Názvy prostředků nerozlišují malá a velká písmena, pokud nejsou výslovně uvedeny ve sloupci platné znaky.

V následujících tabulkách se termínem alfanumerický rozumí:

* **a** přes **z** (malá písmena)
* **A** až **Z** (velká písmena)
* **0** až **9** (čísla)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Servery | skupina prostředků | 3–63 | Malá písmena a čísla.<br><br>Začněte s písmenem malá písmena. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | služba | global | 1-50 | Alfanumerické.<br><br>Začněte s dopisem. |
> | služba / apis | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / problémy | rozhraní api | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / problémy / přílohy | Problém | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / problémy / komentáře | Problém | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / operace | rozhraní api | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / operace / tagy | Operace | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / zprávy | rozhraní api | 1–80 | Alfanumerické, podtržítka a spojovníky.<br><br>Začátek a konec alfanumerickým nebo podtržítkem. |
> | služba / apis / schémata | rozhraní api | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / tagPopisy | rozhraní api | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / apis / tagy | rozhraní api | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / api-version-sets | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / autorizaceServery | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / back-endy | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / certifikáty | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | servis / diagnostika | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / skupiny | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / skupiny / uživatelé | group | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / identitaPoskytovatelé | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / dřevorubci | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / oznámení | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / oznámení / příjemceE-maily | oznámení | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / openidConnectProviders | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / politiky | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | servis / produkty | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | servis / produkty / apis | product | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | servis / produkty / skupiny | product | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / produkty / tagy | product | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / vlastnosti | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / předplatné | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / tagy | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / šablony | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |
> | služba / uživatelé | služba | 1-256 | Nelze použít:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurace

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | configurationStores | skupina prostředků | 5-50 | Alfanumerické, podtržítka a spojovníky. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Zámky | rozsah postoupení | 1–90 | Alfanumerické, tečky, podtržítka, pomlčky a závorky.<br><br>Nemůže skončit v periodě. |
> | přiřazení zásad | rozsah postoupení | 1-128 zobrazovaný název<br><br>1-260 název prostředku | Zobrazovaný název může obsahovat libovolné znaky.<br><br>Název prostředku nemůže `%` obsahovat a nemůže končit tečkou nebo mezerou. |
> | definice politik | rozsah definice | 1-128 zobrazovaný název<br><br>1-260 název prostředku | Zobrazovaný název může obsahovat libovolné znaky.<br><br>Název prostředku nemůže `%` obsahovat a nemůže končit tečkou nebo mezerou. |
> | policySetDefinitions | rozsah definice | 1-128 zobrazovaný název<br><br>1-260 název prostředku | Zobrazovaný název může obsahovat libovolné znaky.<br><br>Název prostředku nemůže `%` obsahovat a nemůže končit tečkou nebo mezerou.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | automationAccounts | skupina prostředků | 6-50 | Alfanumerické a spojovníky.<br><br>Začněte písmenem a zakončite alfanumerickým. |
> | automationAccounts / certifikáty | účet automatizace | 1-128 | Nelze použít:<br> `<>*%&:\?.+/` <br><br>Nemůže skončit prostorem.  |
> | automationAccounts / připojení | účet automatizace | 1-128 | Nelze použít:<br> `<>*%&:\?.+/` <br><br>Nemůže skončit prostorem. |
> | automationAccounts / pověření | účet automatizace | 1-128 | Nelze použít:<br> `<>*%&:\?.+/` <br><br>Nemůže skončit prostorem. |
> | automationAccounts / runbooks | účet automatizace | 1–63 | Alfanumerické, podtržítka a spojovníky.<br><br>Začněte s dopisem.  |
> | automationÚčty / plány | účet automatizace | 1-128 | Nelze použít:<br> `<>*%&:\?.+/` <br><br>Nemůže skončit prostorem. |
> | automationAccounts / proměnné | účet automatizace | 1-128 | Nelze použít:<br> `<>*%&:\?.+/` <br><br>Nemůže skončit prostorem. |
> | automationAccounts / pozorovatelé | účet automatizace | 1–63 |  Alfanumerické, podtržítka a spojovníky.<br><br>Začněte s dopisem. |
> | automationAccounts / webhooky | účet automatizace | 1-128 | Nelze použít:<br> `<>*%&:\?.+/` <br><br>Nemůže skončit prostorem. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | dávkové účty | Region (Oblast) | 3–24 | Malá písmena a čísla. |
> | dávkové účty / aplikace | dávkový účet | 1-64 | Alfanumerické, podtržítka a spojovníky. |
> | dávkové účty / certifikáty | dávkový účet | 5-45 | Alfanumerické, podtržítka a spojovníky. |
> | dávkové účty / fondy | dávkový účet | 1-64 | Alfanumerické, podtržítka a spojovníky. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | blockchainČlenové | global | 2-20 | Malá písmena a čísla.<br><br>Začněte s písmenem malá písmena. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | botServices | global | 2–64 |  Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. |
> | botServices / kanály | bot služby | 2–64 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. |
> | botServices / Připojení | bot služby | 2–64 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. |
> | podnikové kanály | skupina prostředků | 2–64 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Redis | global | 1–63 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. Po sobě jdoucí spojovníky nejsou povoleny. |
> | Redis / firewallPravidla | Redis | 1-256 | Alfanumerické |

## <a name="microsoftcdn"></a>Soubor Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Profily | skupina prostředků | 1-260 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |
> | profily / koncové body | global | 1-50 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | certificateObjednávky | skupina prostředků | 3-30 | Alfanumerické. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | skupina prostředků | 2–64 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | dostupnostSady | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerickým nebo podtržítkem. |
> | diskové šifrovací sady | skupina prostředků | 1–80 | Alfanumerické a podtržítka. |
> | Disky | skupina prostředků | 1–80 | Alfanumerické a podtržítka. |
> | Galerie | skupina prostředků | 1–80 | Alfanumerické a tečky.<br><br>Začátek a konec alfanumerickým. |
> | galerie / aplikace | galerie | 1–80 | Alfanumerické, spojovníky a tečky.<br><br>Začátek a konec alfanumerickým. |
> | galerie / aplikace/verze | aplikace | 32bitové celé číslo | Čísla a tečky. |
> | galerie / obrázky | galerie | 1–80 | Alfanumerické, spojovníky a tečky.<br><br>Začátek a konec alfanumerickým. |
> | galerie / obrázky / verze | image | 32bitové celé číslo | Čísla a tečky. |
> | images | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerickým nebo podtržítkem. |
> | snímky | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerickým nebo podtržítkem. |
> | virtualMachines | skupina prostředků | 1-15 (Windows)<br>1-64 (Linux)<br><br>Viz poznámka níže. | Nelze použít:<br> `\/""[]:|<>+=;,?*@&`<br><br>Nemůžu začít podtržítkem. Nelze končit tečkou nebo pomlčkou. |
> | virtualMachineScaleSets | skupina prostředků | 1-15 (Windows)<br>1-64 (Linux)<br><br>Viz poznámka níže. | Nelze použít:<br> `\/""[]:|<>+=;,?*@&`<br><br>Nemůžu začít podtržítkem. Nelze končit tečkou nebo pomlčkou. |

> [!NOTE]
> Virtuální počítače Azure mají dva odlišné názvy: název prostředku a název hostitele. Při vytváření virtuálního počítače na portálu se pro oba názvy používá stejná hodnota. Omezení v předchozí tabulce jsou pro název hostitele. Název vlastního prostředku může mít až 64 znaků.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | containerGroups | skupina prostředků | 1–63 | Malá písmena, čísla a pomlčky.<br><br>Nelze začínat nebo končit pomlčkou. Po sobě jdoucí pomlčky nejsou povolené. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Rejstříků | global | 5-50 | Alfanumerické. |
> | registrů / buildTasks | registry | 5-50 | Alfanumerické. |
> | registry / buildTasks/steps | sestavení úkolu | 5-50 | Alfanumerické. |
> | registry / replikace | registry | 5-50 | Alfanumerické. |
> | registry / scopeMapy | registry | 5-50 | Alfanumerické, spojovníky a podtržítka. |
> | registrů / úkolů | registry | 5-50 | Alfanumerické, spojovníky a podtržítka. |
> | registry / tokeny | registry | 5-50 | Alfanumerické, spojovníky a podtržítka. |
> | registry / webhooky | registry | 5-50 | Alfanumerické. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | spravované clustery | skupina prostředků | 1–63 | Alfanumerické, podtržítka a spojovníky.<br><br>Začátek a konec alfanumerickým. |
> | openShiftManagedClusters | skupina prostředků | 1-30 | Alfanumerické. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Rozbočovače | skupina prostředků | 1-64 | Alfanumerické.<br><br>Začněte s dopisem.  |
> | rozbočovače / autorizacePolitiky | Rozbočovač | 1-50 | Alfanumerické, podtržítka a tečky.<br><br>Začátek a konec alfanumerickým. |
> | rozbočovače / konektory | Rozbočovač | 1-128 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / konektory/mapování | konektor | 1-128 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | náboje / interakce | Rozbočovač | 1-128 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / kpi | Rozbočovač | 1-512 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / odkazy | Rozbočovač | 1-512 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / předpovědi | Rozbočovač | 1-512 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / profily | Rozbočovač | 1-128 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / relationshipLinks | Rozbočovač | 1-512 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / vztahy | Rozbočovač | 1-512 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / roleÚkoly | Rozbočovač | 1-128 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |
> | rozbočovače / zobrazení | Rozbočovač | 1-512 | Alfanumerické a podtržítka.<br><br>Začněte s dopisem. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Sdružení | skupina prostředků | 1-180 | Nelze použít:<br>`%&\\?/`<br><br>Nelze skončit tečkou nebo mezerou. |
> | resourceProviders | skupina prostředků | 3-64 | Nelze použít:<br>`%&\\?/`<br><br>Nelze skončit tečkou nebo mezerou. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Úlohy | skupina prostředků | 3–24 | Alfanumerické, spojovníky, podtržítka a tečky. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | pracovní prostory | skupina prostředků | 3-30 | Alfanumerické, podtržítka a spojovníky |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Továrny | global | 3–63 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |
> | továrny / toky dat | Továrna | 1-260 | Nelze použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte alfanumerickým. |
> | továrny / datové sady | Továrna | 1-260 | Nelze použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte alfanumerickým. |
> | továrny / integraceRuntimes | Továrna | 3–63 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |
> | továrny / linkedservices | Továrna | 1-260 | Nelze použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte alfanumerickým. |
> | továrny / potrubí | Továrna | 1-260 | Nelze použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte alfanumerickým. |
> | továrny / spouští | Továrna | 1-260 | Nelze použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte alfanumerickým. |
> | továrny / spouští / rerunTriggers | Aktivační událost | 1-260 | Nelze použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte alfanumerickým. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | global | 3–24 | Malá písmena a čísla. |
> | účty / výpočetní politiky | account | 3-60 | Alfanumerické, spojovníky a podtržítka. |
> | účty / dataLakeStoreAccounts | account | 3–24 | Malá písmena a čísla. |
> | účty / firewallRules | account | 3-50 | Alfanumerické, spojovníky a podtržítka. |
> | účty / úložištěÚčty | account | 3-60 | Alfanumerické, spojovníky a podtržítka. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | global | 3–24 | Malá písmena a čísla. |
> | účty / firewallRules | account | 3-50 | Alfanumerické, spojovníky a podtržítka. |
> | účty / virtualNetworkRules | account | 3-50 | Alfanumerické, spojovníky a podtržítka. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | services | skupina prostředků | 2-62 | Alfanumerické, spojovníky, tečky a podtržítka.<br><br>Začněte alfanumerickým. |
> | služby / projekty | služba | 2-57 | Alfanumerické, spojovníky, tečky a podtržítka.<br><br>Začněte alfanumerickým. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Servery | global | 3–63 | Malá písmena, pomlčky a čísla.<br><br>Nelze začínat nebo končit pomlčkou. |
> | servery / databáze | Servery | 1–63 | Alfanumerické a spojovníky. |
> | servery / firewallRules | Servery | 1-128 | Alfanumerické, spojovníky a podtržítka. |
> | servery / virtualNetworkRules | Servery | 1-128 | Alfanumerické a spojovníky. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Servery | global | 3–63 | Malá písmena, pomlčky a čísla.<br><br>Nelze začínat nebo končit pomlčkou. |
> | servery / databáze | Servery | 1–63 | Alfanumerické a spojovníky. |
> | servery / firewallRules | Servery | 1-128 | Alfanumerické, spojovníky a podtržítka. |
> | servery / virtualNetworkRules | Servery | 1-128 | Alfanumerické a spojovníky. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Servery | global | 3–63 | Malá písmena, pomlčky a čísla.<br><br>Nelze začínat nebo končit pomlčkou. |
> | servery / databáze | Servery | 1–63 | Alfanumerické a spojovníky. |
> | servery / firewallRules | Servery | 1-128 | Alfanumerické, spojovníky a podtržítka. |
> | servery / virtualNetworkRules | Servery | 1-128 | Alfanumerické a spojovníky. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanumerické a spojovníky.<br><br>Nelze končit pomlčkou. |
> | IotHubs / certifikáty | Centrum IoT | 1-64 | Alfanumerické, spojovníky, tečky a podtržítka. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerické, spojovníky, tečky a podtržítka. |
> | provisioningServices | skupina prostředků | 3-64 | Alfanumerické a spojovníky.<br><br>Konec alfanumerickým. |
> | provisioningServices / certifikáty | provisioningServices | 1-64 | Alfanumerické, spojovníky, tečky a podtržítka. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Labs | skupina prostředků | 1-50 | Alfanumerické, podtržítka a spojovníky. |
> | laboratoře / customimages | Laboratoře | 1–80 | Alfanumerické, podtržítka, spojovníky a závorky. |
> | laboratoře / vzorce | Laboratoře | 1–80 | Alfanumerické, podtržítka, spojovníky a závorky. |
> | laboratoře / virtualmachines | Laboratoře | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. Nemůžou to být všechna čísla. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | Malá písmena, čísla a pomlčky.<br><br>Začněte s malé písmeno nebo číslo. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Domény | skupina prostředků | 3-50 | Alfanumerické a spojovníky. |
> | domény / témata | doména | 3-50 | Alfanumerické a spojovníky. |
> | eventSubscriptions | skupina prostředků | 3-64 | Alfanumerické a spojovníky. |
> | Témata | skupina prostředků | 3-50 | Alfanumerické a spojovníky. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Clustery | skupina prostředků | 6-50 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Konec písmenem nebo číslem. |
> | Obory názvů | global | 6-50 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Konec písmenem nebo číslem. |
> | obory názvů / Autorizační pravidla | namespace | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec písmenem nebo číslem. |
> | obory názvů / disasterRecoveryConfigs | namespace | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec písmenem nebo číslem. |
> | jmenné prostory / eventhubs | namespace | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec písmenem nebo číslem. |
> | obory názvů / eventhubs / authorizationRules | centrum událostí | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec písmenem nebo číslem. |
> | jmenné prostory / eventhubs / spotřebitelské skupiny | centrum událostí | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec písmenem nebo číslem. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Clustery | global | 3-59 | Alfanumerické a spojovníky<br><br>Začátek a konec písmenem nebo číslem. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Úlohy | skupina prostředků | 2–64 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Součásti | skupina prostředků | 1-260 | Nelze použít:<br>`%&\?/` <br><br>Nemůže skončit s prostorem nebo tečkou.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Malá písmena, čísla a pomlčky.<br><br>Začněte s malé písmeno nebo číslo. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Klenby | global | 3–24 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Konec písmenem nebo číslicí. Nesmí obsahovat po sobě jdoucí spojovníky. |
> | trezory / tajemství | Trezor | 1-127 | Alfanumerické a spojovníky. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Clustery | global | 4-22 | Malá písmena a čísla.<br><br>Začněte s dopisem. |
> | /clustery / databáze | cluster | 1-260 | Alfanumerické, spojovníky, mezery a tečky. |
> | /clustery / databáze / dataConnections | database | 1-40 | Alfanumerické, spojovníky, mezery a tečky. |
> | /clusters / databáze / eventhubconnections | database | 1-40 | Alfanumerické, spojovníky, mezery a tečky. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | integraceÚčty | skupina prostředků | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integraceÚčty / sestavy | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integrationAccounts / batchKonfigurace | účet pro integraci | 1-20 | Alfanumerické. |
> | integraceÚčty / certifikáty | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integraceÚčty / mapy | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integraceÚčty / partneři | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integrationAccounts / rosettanetprocessconfigurations integrationAccounts / rosettanetprocessconfigurations integrationAccounts / rosettanetprocessconfigurations integrationAccounts | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integraceÚčty / schémata | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integraceÚčty / relace | účet pro integraci | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |
> | integrationServiceEnvironments | skupina prostředků | 1–80 | Alfanumerické, spojovníky, tečky a podtržítka. |
> | integrationServiceEnvironments / managedApis | prostředí integračních služeb | 1–80 | Alfanumerické, spojovníky, tečky a podtržítka. |
> | Pracovní postupy | skupina prostředků | 1–80 | Alfanumerické, spojovníky, podtržítka, tečky a závorky. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | commitmentPlans | skupina prostředků | 1-260 | Nelze použít:<br>`<>*%&:?+/\\`<br><br>Nemůže skončit mezerou. |
> | Webservices | skupina prostředků | 1-260 | Nelze použít:<br>`<>*%&:?+/\\`<br><br>Nemůže skončit mezerou. |
> | pracovní prostory | skupina prostředků | 1-260 | Nelze použít:<br>`<>*%&:?+/\\`<br><br>Nemůže skončit mezerou. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | pracovní prostory | skupina prostředků | 3-33 | Alfanumerické a spojovníky. |
> | pracovní prostory / počítá | Pracovní prostor | 2-16 | Alfanumerické a spojovníky. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | userAssignedIdentities | skupina prostředků | 3-128 | Alfanumerické, spojovníky a podtržítka<br><br>Začněte písmenem nebo číslem. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | skupina prostředků | 1-98 (pro název skupiny prostředků a název účtu) | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | mediální služby | skupina prostředků | 3–24 | Malá písmena a čísla. |
> | mediaservices / liveEvents | Mediální služba | 1-32 | Alfanumerické a spojovníky.<br><br>Začněte alfanumerickým. |
> | mediaservices / liveEvents / liveOutputs | Živá událost | 1-256 | Alfanumerické a spojovníky.<br><br>Začněte alfanumerickým. |
> | mediaservices / streamingKoncové body | Mediální služba | 1-24 | Alfanumerické a spojovníky.<br><br>Začněte alfanumerickým. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | applicationGateways | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | applicationSecurityGroups | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | azureFirewalls | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerickým nebo podtržítkem. |
> | bastionHosts | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | Připojení | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | dnsZóny | skupina prostředků | 1-63 znaků<br><br>2 až 34 štítků<br><br>Každý popisek je sada znaků oddělených tečkou. Například **contoso.com** má 2 popisky. | Každý popisek může obsahovat alfanumerické prvky, podtržítka a spojovníky.<br><br>Každý popisek je oddělen tečkou. |
> | expressRouteCircuits | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | firewallZásady | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | firewallZásady / skupiny pravidel | zásady brány firewall | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | přední dveře | global | 5-64 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |
> | vynakládače | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | loadBalancers / inboundNatRules | vyrovnávání zatížení | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | localNetworkGateways | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | síťová rozhraní | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | síťSecuritygroups | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | networkSecurityGroups / securityRules | skupina zabezpečení sítě | 1–80 |  Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | networkWatchers | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | privátnídnszóny | skupina prostředků | 1-63 znaků<br><br>2 až 34 štítků<br><br>Každý popisek je sada znaků oddělených tečkou. Například **contoso.com** má 2 popisky. | Každý popisek může obsahovat alfanumerické prvky, podtržítka a spojovníky.<br><br>Každý popisek je oddělen tečkou. |
> | privateDnsZones / virtualNetworkLinks | privátní zóna DNS | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | publicIPAdresy | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | publicIPPrefixes | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | routeFilters | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | routeFilters / routeFilterRules | filtr trasy | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | routeTables | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | routeTables / trasy | směrovací tabulka | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | serviceEndpointPolicies | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | trafficmanagerprofiles | global | 1–63 | Alfanumerické, spojovníky a tečky.<br><br>Začátek a konec alfanumerickým. |
> | virtualNetworkGateways | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | virtualNetworks | skupina prostředků | 2–64 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | virtuální sítě / podsítě | virtuální síť | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | virtualNetworks / virtualNetworkPeerings | virtuální síť | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | virtualWans | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | vpnBrány | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | vpnGateways / vpnConnections | VPN Gateway | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |
> | vpnWe | skupina prostředků | 1–80 | Alfanumerické, podtržítka, tečky a pomlčky.<br><br>Začněte alfanumerickým. Konec alfanumerického nebo podtržítka. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Obory názvů | global | 6-50 | Alfanumerické a spojovníky<br><br>Začátek a konec alfanumerickým. |
> | obory názvů / Autorizační pravidla | namespace | 1-256 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Spusťte alfanumerický. |
> | jmenné prostory / notificationHubs | namespace | 1-260 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Spusťte alfanumerický. |
> | jmenné prostory / notificationHubs / AuthorizationRules | centrum oznámení | 1-256 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Spusťte alfanumerický. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Clustery | skupina prostředků | 4-63 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |
> | pracovní prostory | skupina prostředků | 4-63 | Alfanumerické a spojovníky.<br><br>Začátek a konec alfanumerickým. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | řídicí panely | skupina prostředků | 3-160 | Alfanumerické a spojovníky.<br><br>Chcete-li používat znaky s omezeným přístupem, přidejte značku s názvem **skrytý název** s názvem řídicího panelu, který chcete použít. Portál zobrazí tento název při zobrazení řídicího panelu. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | kolekce pracovního prostoru | oblast | 3–63 | Alfanumerické a spojovníky.<br><br>Nelze začít pomlčkou. Nelze použít po sobě jdoucí spojovníky. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Kapacity | oblast | 3–63 | Malá písmena nebo čísla<br><br>Začněte s písmenem malá písmena. |

## <a name="microsoftrecoveryservices"></a>Služby Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Klenby | skupina prostředků | 2-50 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. |
> | trezory / backupPolicies | Trezor | 3-150 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Nelze končit pomlčkou. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Obory názvů | global | 6-50 | Alfanumerické a spojovníky.<br><br>Začněte písmenem. Konec písmenem nebo číslem. |
> | obory názvů / Autorizační pravidla | namespace | 1-50 |  Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alfanumerickým. |
> | obory názvů / HybridConnections | namespace | 1-260 | Alfanumerické, tečky, pomlčky, podtržítka a lomítka.<br><br>Začátek a konec alfanumerickým. |
> | obory názvů / HybridConnections/authorizationRules | hybridní připojení | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alfanumerickým. |
> | jmenné prostory / WcfRelays | namespace | 1-260 | Alfanumerické, tečky, pomlčky, podtržítka a lomítka.<br><br>Začátek a konec alfanumerickým. |
> | jmenné prostory / WcfRelays / authorizationRules | Wcf relé | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alfanumerickým. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Nasazení | skupina prostředků | 1-64 | Alfanumerické, podtržítka, závorky, spojovníky a tečky. |
> | skupiny prostředků | předplatné | 1–90 | Alfanumerické znaky, podtržítka, závorky, pomlčky, tečky a znaky unicode, které odpovídají [dokumentaci regulárního výrazu](/rest/api/resources/resourcegroups/createorupdate).<br><br>Nemůžu skončit tečkou. |
> | tagNames | prostředek | 1-512 | Nelze použít:<br>`<>%&\?/` |
> | tagNames / tagValues | název značky | 1-256 | Všechny znaky. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Obory názvů | global | 6-50 | Alfanumerické a spojovníky.<br><br>Začněte písmenem. Konec písmenem nebo číslem.<br><br>Další informace naleznete v [tématu Vytvoření oboru názvů](/rest/api/servicebus/create-namespace). |
> | obory názvů / Autorizační pravidla | namespace | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alphnumeric. |
> | obory názvů / disasterRecoveryConfigs | global | 6-50 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Konec alfanumerickým. |
> | obory názvů / migraceKonfigurace | namespace |  | By měla být vždy **$default**. |
> | jmenné prostory / fronty | namespace | 1-260 | Alfanumerické, tečky, pomlčky, podtržítka a lomítka.<br><br>Začátek a konec alfanumerickým. |
> | obory názvů / fronty / autorizační pravidla | fronta | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alphnumeric. |
> | jmenné prostory / témata | namespace | 1-260 | Alfanumerické, tečky, pomlčky, podtržítka a lomítka.<br><br>Začátek a konec alfanumerickým. |
> | jmenné prostory / témata / authorizationRules | téma | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alphnumeric. |
> | jmenovky / témata / odběry | téma | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alphnumeric. |
> | jmenovky / témata / odběry / pravidla | předplatné | 1-50 | Alfanumerické, tečky, pomlčky a podtržítka.<br><br>Začátek a konec alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Clustery | oblast | 4-23 | Malá písmena, čísla a pomlčky.<br><br>Začněte s písmenem malá písmena. Konec s malé písmeno nebo číslo. |

## <a name="microsoftsignalrservice"></a>Služba Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | signalR | global | 3–63 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Konec písmenem nebo číslem.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | spravované instance | global | 1–63 | Malá písmena, čísla a pomlčky.<br><br>Nelze začínat nebo končit pomlčkou. |
> | Servery | global | 1–63 | Malá písmena, čísla a pomlčky.<br><br>Nelze začínat nebo končit pomlčkou. |
> | servery / správci | server |  | Musí `ActiveDirectory`být . |
> | servery / databáze | server | 1-128 | Nelze použít:<br>`<>*%&:\/?`<br><br>Nelze skončit tečkou nebo mezerou. |
> | servery / databáze / syncGroups | database | 1-150 | Alfanumerické, spojovníky a podtržítka. |
> | servery / elastickébazény | server | 1-128 | Nelze použít:<br>`<>*%&:\/?`<br><br>Nelze skončit tečkou nebo mezerou. |
> | servery / skupiny s podporou převzetí služeb při selhání | global | 1–63 | Malá písmena, čísla a pomlčky.<br><br>Nelze začínat nebo končit pomlčkou. |
> | servery / firewallRules | server | 1-128 | Nelze použít:<br>`<>*%&:;\/?`<br><br>Nemůžu skončit tečkou. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3–24 | Malá písmena a čísla. |
> | storageAccounts / blobServices | účet úložiště |  | Musí `default`být . |
> | storageAccounts / blobServices / kontejnery | účet úložiště | 3–63 | Malá písmena, čísla a pomlčky.<br><br>Začněte s malé písmeno nebo číslo. Nelze použít po sobě jdoucí spojovníky. |
> | storageAccounts / fileServices | účet úložiště |  | Musí `default`být . |
> | storageAccounts / fileServices / shares | účet úložiště | 3–63 | Malá písmena, čísla a pomlčky.<br><br>Nelze začínat nebo končit pomlčkou. Nelze použít po sobě jdoucí spojovníky. |
> | úložištěÚčty / managementPolitiky | účet úložiště |  | Musí `default`být . |
> | blob | kontejner | 1–1024 | Všechny znaky URL, rozlišování malých a velkých písmen |
> | fronta | účet úložiště | 3–63 | Malá písmena, čísla a pomlčky.<br><br>Nelze začínat nebo končit pomlčkou. Nelze použít po sobě jdoucí spojovníky. |
> | tabulka | účet úložiště | 3–63 | Alfanumerické.<br><br>Začněte s dopisem. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | storageSyncServices | skupina prostředků | 1-260 | Alfanumerické, mezery, tečky, pomlčky a podtržítka.<br><br>Nelze skončit tečkou nebo mezerou. |
> | storageSyncServices / syncGroups | služba synchronizace úložiště | 1-260 | Alfanumerické, mezery, tečky, pomlčky a podtržítka.<br><br>Nelze skončit tečkou nebo mezerou. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Manažeři | skupina prostředků | 2-50 | Alfanumerické a spojovníky.<br><br>Začněte s dopisem. Konec alfanumerickým. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | streamováníúlohy | skupina prostředků | 3–63 | Alfanumerické, spojovníky a podtržítka. |
> | streamingjobs / funkce | streamování úlohy | 3–63 | Alfanumerické, spojovníky a podtržítka. |
> | streamingjobs / vstupy | streamování úlohy | 3–63 | Alfanumerické, spojovníky a podtržítka. |
> | streamingjobs / výstupy | streamování úlohy | 3–63 | Alfanumerické, spojovníky a podtržítka. |
> | streamingjobs / transformace | streamování úlohy | 3–63 | Alfanumerické, spojovníky a podtržítka. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Prostředí | skupina prostředků | 1–90 | Nelze použít:<br>`'<>%&:\?/#` |
> | prostředí / přístupPolitiky | environment | 1–90 | Nelze použít:<br> `'<>%&:\?/#` |
> | prostředí / eventSources | environment | 1–90 | Nelze použít:<br>`'<>%&:\?/#` |
> | prostředí / referenceDataSets | environment | 3–63 | Alfanumerické |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | serverové farmy | skupina prostředků | 1-40 | Alfanumerické a spojovníky. |
> | Stránky | global | 2-60 | Obsahuje alfanumerické a spojovníky.<br><br>Nelze začínat nebo končit pomlčkou. |
> | stránky / sloty | Stránky | 2-59 | Alfanumerické a spojovníky. |

## <a name="next-steps"></a>Další kroky

Doporučení týkající se pojmenování prostředků naleznete v [tématu Připraveno: Doporučené konvence pojmenování a označování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
