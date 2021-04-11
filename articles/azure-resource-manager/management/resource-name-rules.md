---
title: Omezení pojmenování prostředků
description: Zobrazuje pravidla a omezení pro pojmenování prostředků Azure.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: a1dbe409eb5479c4027834cd33e095d12c31e238
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210189"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Omezení a pravidla pojmenování prostředků Azure

Tento článek shrnuje pravidla a omezení pro vytváření prostředků Azure. Doporučení k pojmenování prostředků najdete v tématu [Doporučené konvence pojmenování a označování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Tento článek obsahuje seznam prostředků podle oboru názvů poskytovatele prostředků. Seznam způsobů, jak poskytovatelé prostředků odpovídají službám Azure, najdete v tématu [poskytovatelé prostředků pro služby Azure](azure-services-resource-providers.md).

V názvech prostředků se nerozlišují malá a velká písmena, pokud není uvedeno ve sloupci platné znaky.

V následujících tabulkách pojem alfanumerický označuje:

* **a** až **z** (malá písmena)
* **A** až **Z** (velká písmena)
* **0** až **9** (čísla)

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | servery | skupina prostředků | 3–63 | Malá písmena a číslice.<br><br>Začínat malým písmenem. |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | service | global | 1-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem a končí alfanumerickým znakem. |
> | služba/rozhraní API | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/problémy | api | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/problémy/přílohy | issue | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/problémy/komentáře | issue | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/operace | api | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/operace/značky | operation | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/verze | api | 1–80 | Alfanumerické znaky, podtržítka a spojovníky.<br><br>Zahajte a končí alfanumerickým znakem nebo podtržítkem. |
> | služba/rozhraní API/schémata | api | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/tagDescriptions | api | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API/značky | api | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/rozhraní API – sady verzí | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/authorizationServers | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/back-endy | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/certifikáty | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/Diagnostika | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/skupiny | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | služba/skupiny/Uživatelé | group | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/identityProviders | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/protokolovací nástroje | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/oznámení | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/oznámení/recipientEmails | oznámení | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/openidConnectProviders | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/zásady | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/produkty | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/produkty/rozhraní API | product | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/produkty/skupiny | product | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/produkty/značky | product | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/vlastnosti | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/předplatná | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/značky | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/šablony | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |
> | Služba/uživatelé | service | 1-256 | Nejde použít:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | configurationStores | skupina prostředků | 5-50 | Alfanumerické znaky, podtržítka a spojovníky. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | počtu | rozsah přiřazení | 1–90 | Alfanumerické znaky, tečky, podtržítka, spojovníky a závorky.<br><br>Nemůže končit tečkou. |
> | policyAssignments | rozsah přiřazení | zobrazované jméno 1-128<br><br>název prostředku 1-64<br><br>1-24 název prostředku v oboru skupiny pro správu | Zobrazovaný název může obsahovat libovolné znaky.<br><br>Název prostředku nemůže obsahovat `%` a nemůže končit tečkou nebo mezerou. |
> | policyDefinitions | rozsah definice | zobrazované jméno 1-128<br><br>název prostředku 1-64 | Zobrazovaný název může obsahovat libovolné znaky.<br><br>Název prostředku nemůže obsahovat `%` a nemůže končit tečkou nebo mezerou. |
> | policySetDefinitions | rozsah definice | zobrazované jméno 1-128<br><br>název prostředku 1-64<br><br>1-24 název prostředku v oboru skupiny pro správu | Zobrazovaný název může obsahovat libovolné znaky.<br><br>Název prostředku nemůže obsahovat `%` a nemůže končit tečkou nebo mezerou.  |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | automationAccounts | Skupina prostředků & oblast <br>(Viz poznámka níže) | 6-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem a končí alfanumerickým znakem. |
> | automationAccounts/certifikáty | účet služby Automation | 1-128 | Nejde použít:<br> `<>*%&:\?.+/` <br><br>Nemůže končit mezerou.  |
> | automationAccounts/připojení | účet služby Automation | 1-128 | Nejde použít:<br> `<>*%&:\?.+/` <br><br>Nemůže končit mezerou. |
> | automationAccounts/přihlašovací údaje | účet služby Automation | 1-128 | Nejde použít:<br> `<>*%&:\?.+/` <br><br>Nemůže končit mezerou. |
> | automationAccounts/Runbooky | účet služby Automation | 1–63 | Alfanumerické znaky, podtržítka a spojovníky.<br><br>Začíná písmenem.  |
> | automationAccounts/plány | účet služby Automation | 1-128 | Nejde použít:<br> `<>*%&:\?.+/` <br><br>Nemůže končit mezerou. |
> | automationAccounts/proměnné | účet služby Automation | 1-128 | Nejde použít:<br> `<>*%&:\?.+/` <br><br>Nemůže končit mezerou. |
> | automationAccounts/sledovací procesy | účet služby Automation | 1–63 |  Alfanumerické znaky, podtržítka a spojovníky.<br><br>Začíná písmenem. |
> | automationAccounts nebo Webhooky | účet služby Automation | 1-128 | Nejde použít:<br> `<>*%&:\?.+/` <br><br>Nemůže končit mezerou. |

> [!NOTE]
> Názvy účtů Automation jsou jedinečné pro jednotlivé oblasti a skupiny prostředků. Názvy pro odstraněné účty Automation nemusí být okamžitě k dispozici.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | batchAccounts | Oblast | 3–24 | Malá písmena a číslice. |
> | batchAccounts/aplikace | účet Batch | 1-64 | Alfanumerické znaky, podtržítka a spojovníky. |
> | batchAccounts/certifikáty | účet Batch | 5-45 | Alfanumerické znaky, podtržítka a spojovníky. |
> | batchAccounts/fondy | účet Batch | 1-64 | Alfanumerické znaky, podtržítka a spojovníky. |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Malá písmena a číslice.<br><br>Začínat malým písmenem. |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | botServices | global | 2–64 |  Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |
> | botServices/kanály | Robot – služba | 2–64 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |
> | botServices/připojení | Robot – služba | 2–64 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |
> | enterpriseChannels | skupina prostředků | 2–64 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Redis | global | 1–63 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. Po sobě jdoucí spojovníky nejsou povolené. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumerické znaky |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | uživatelů | skupina prostředků | 1-260 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |
> | profily/koncové body | global | 1-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | certificateOrders | skupina prostředků | 3-30 | Alfanumerické znaky. |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | skupina prostředků | 2–64 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | availabilitySets | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Konec s alfanumerickým nebo podtržítkem. |
> | diskEncryptionSets | skupina prostředků | 1–80 | Alfanumerické znaky a podtržítka. |
> | disků | skupina prostředků | 1–80 | Alfanumerické znaky a podtržítka. |
> | Galerie | skupina prostředků | 1–80 | Alfanumerické znaky a tečky.<br><br>Začíná a končí alfanumerickými znaky. |
> | Galerie/aplikace | galerie | 1–80 | Alfanumerické znaky, spojovníky a tečky.<br><br>Začíná a končí alfanumerickými znaky. |
> | Galerie/aplikace/verze | aplikace | 32-bitové celé číslo | Čísla a tečky. |
> | Galerie/image | galerie | 1–80 | Alfanumerické znaky, podtržítka, spojovníky a tečky.<br><br>Začíná a končí alfanumerickými znaky. |
> | Galerie/image/verze | image | 32-bitové celé číslo | Čísla a tečky. |
> | images | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Konec s alfanumerickým nebo podtržítkem. |
> | snímky | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Konec s alfanumerickým nebo podtržítkem. |
> | virtualMachines | skupina prostředků | 1-15 (Windows)<br>1-64 (Linux)<br><br>Viz poznámka níže. | Nelze použít mezeru ani tyto znaky:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Virtuální počítače s Windows nemůžou zahrnovat tečku ani končit pomlčkou.<br><br>Virtuální počítače se systémem Linux nemůžou končit tečkou nebo spojovníkem. |
> | virtualMachineScaleSets | skupina prostředků | 1-15 (Windows)<br>1-64 (Linux)<br><br>Viz poznámka níže. | Nelze použít mezeru ani tyto znaky:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Nelze začínat podtržítkem. Nemůže končit tečkou nebo spojovníkem. |

> [!NOTE]
> Virtuální počítače Azure mají dva odlišné názvy: název prostředku a název hostitele. Když na portálu vytvoříte virtuální počítač, použije se stejná hodnota pro oba názvy. Omezení v předchozí tabulce jsou pro název hostitele. Název vlastního prostředku může mít až 64 znaků.

## <a name="microsoftcommunication"></a>Microsoft. Communication

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | communicationServices | global | 1–63 | Alfanumerické znaky, spojovníky a podtržítka. |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | containerGroups | skupina prostředků | 1–63 | Malá písmena, číslice a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. Po sobě jdoucí pomlčky nejsou povolené. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | registr | global | 5-50 | Alfanumerické znaky. |
> | Registry/buildTasks | registry | 5-50 | Alfanumerické znaky. |
> | Registry/buildTasks/kroky | úloha sestavení | 5-50 | Alfanumerické znaky. |
> | Registry/replikace | registry | 5-50 | Alfanumerické znaky. |
> | Registry/scopeMaps | registry | 5-50 | Alfanumerické znaky, spojovníky a podtržítka. |
> | Registry/úlohy | registry | 5-50 | Alfanumerické znaky, spojovníky a podtržítka. |
> | Registry a tokeny | registry | 5-50 | Alfanumerické znaky, spojovníky a podtržítka. |
> | Registry a Webhooky | registry | 5-50 | Alfanumerické znaky. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | managedClusters | skupina prostředků | 1–63 | Alfanumerické znaky, podtržítka a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |
> | openShiftManagedClusters | skupina prostředků | 1-30 | Alfanumerické znaky. |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Hubs | skupina prostředků | 1-64 | Alfanumerické znaky.<br><br>Začíná písmenem.  |
> | rozbočovače/authorizationPolicies | zdroj | 1-50 | Alfanumerické znaky, podtržítka a tečky.<br><br>Začíná a končí alfanumerickými znaky. |
> | rozbočovače/konektory | zdroj | 1-128 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | rozbočovače/konektory/mapování | konektor | 1-128 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | centra/interakce | zdroj | 1-128 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | centra/klíčový ukazatel výkonu | zdroj | 1-512 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | centra/odkazy | zdroj | 1-512 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | rozbočovače/předpovědi | zdroj | 1-512 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | centra/profily | zdroj | 1-128 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | rozbočovače/relationshipLinks | zdroj | 1-512 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | rozbočovače/vztahy | zdroj | 1-512 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | rozbočovače/roleAssignments | zdroj | 1-128 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |
> | centra/zobrazení | zdroj | 1-512 | Alfanumerické znaky a podtržítka.<br><br>Začíná písmenem. |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | přidružení | skupina prostředků | 1-180 | Nejde použít:<br>`%&\\?/`<br><br>Nemůže končit tečkou nebo mezerou. |
> | resourceProviders | skupina prostředků | 3-64 | Nejde použít:<br>`%&\\?/`<br><br>Nemůže končit tečkou nebo mezerou. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | úlohy | skupina prostředků | 3–24 | Alfanumerické znaky, pomlčky, podtržítka a tečky. |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | pracovní prostory | skupina prostředků | 3-30 | Alfanumerické znaky, podtržítka a spojovníky |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | továrny | global | 3–63 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |
> | továrny/toky dat | instalací | 1-260 | Nejde použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte s alfanumerickými znaky. |
> | továrny/datové sady | instalací | 1-260 | Nejde použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte s alfanumerickými znaky. |
> | továrny/integrationRuntimes | instalací | 3–63 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |
> | továrny/linkedservices | instalací | 1-260 | Nejde použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte s alfanumerickými znaky. |
> | továrny/kanály | instalací | 1-260 | Nejde použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte s alfanumerickými znaky. |
> | továrny/triggery | instalací | 1-260 | Nejde použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte s alfanumerickými znaky. |
> | továrny/triggery/rerunTriggers | signálu | 1-260 | Nejde použít:<br>`<>*#.%&:\\+?/`<br><br>Začněte s alfanumerickými znaky. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | global | 3–24 | Malá písmena a číslice. |
> | účty/computePolicies | account | 3-60 | Alfanumerické znaky, spojovníky a podtržítka. |
> | účty/dataLakeStoreAccounts | account | 3–24 | Malá písmena a číslice. |
> | účty/firewallRules | account | 3-50 | Alfanumerické znaky, spojovníky a podtržítka. |
> | účty/storageAccounts | account | 3-60 | Alfanumerické znaky, spojovníky a podtržítka. |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | global | 3–24 | Malá písmena a číslice. |
> | účty/firewallRules | account | 3-50 | Alfanumerické znaky, spojovníky a podtržítka. |
> | účty/virtualNetworkRules | account | 3-50 | Alfanumerické znaky, spojovníky a podtržítka. |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | services | skupina prostředků | 2-62 | Alfanumerické znaky, spojovníky, tečky a podtržítka.<br><br>Začněte s alfanumerickými znaky. |
> | služby a projekty | service | 2-57 | Alfanumerické znaky, spojovníky, tečky a podtržítka.<br><br>Začněte s alfanumerickými znaky. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | servery | global | 3–63 | Malá písmena, spojovníky a číslice.<br><br>Nelze začínat ani končit spojovníkem. |
> | servery/databáze | servery | 1–63 | Alfanumerické znaky a spojovníky. |
> | servery/firewallRules | servery | 1-128 | Alfanumerické znaky, spojovníky a podtržítka. |
> | servery/virtualNetworkRules | servery | 1-128 | Alfanumerické znaky a spojovníky. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | servery | global | 3–63 | Malá písmena, spojovníky a číslice.<br><br>Nelze začínat ani končit spojovníkem. |
> | servery/databáze | servery | 1–63 | Alfanumerické znaky a spojovníky. |
> | servery/firewallRules | servery | 1-128 | Alfanumerické znaky, spojovníky a podtržítka. |
> | servery/virtualNetworkRules | servery | 1-128 | Alfanumerické znaky a spojovníky. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | servery | global | 3–63 | Malá písmena, spojovníky a číslice.<br><br>Nelze začínat ani končit spojovníkem. |
> | servery/databáze | servery | 1–63 | Alfanumerické znaky a spojovníky. |
> | servery/firewallRules | servery | 1-128 | Alfanumerické znaky, spojovníky a podtržítka. |
> | servery/virtualNetworkRules | servery | 1-128 | Alfanumerické znaky a spojovníky. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanumerické znaky a spojovníky.<br><br>Nemůže končit spojovníkem. |
> | IotHubs/certifikáty | Centrum IoT | 1-64 | Alfanumerické znaky, spojovníky, tečky a podtržítka. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerické znaky, spojovníky, tečky a podtržítka. |
> | provisioningServices | skupina prostředků | 3-64 | Alfanumerické znaky a spojovníky.<br><br>Konec s alfanumerickými znaky. |
> | provisioningServices/certifikáty | provisioningServices | 1-64 | Alfanumerické znaky, spojovníky, tečky a podtržítka. |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Labs | skupina prostředků | 1-50 | Alfanumerické znaky, podtržítka a spojovníky. |
> | Labs/CustomImages | laboratoř | 1–80 | Alfanumerické znaky, podtržítka, spojovníky a závorky. |
> | laboratoře/vzorce | laboratoř | 1–80 | Alfanumerické znaky, podtržítka, spojovníky a závorky. |
> | Labs/VirtualMachines | laboratoř | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. Nemůžou být všechna čísla. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-44 | Malá písmena, číslice a spojovníky.<br><br>Začněte s malým písmenem nebo číslicí. |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | doménu | skupina prostředků | 3-50 | Alfanumerické znaky a spojovníky. |
> | domény a témata | doména | 3-50 | Alfanumerické znaky a spojovníky. |
> | eventSubscriptions | skupina prostředků | 3-64 | Alfanumerické znaky a spojovníky. |
> | popisující | skupina prostředků | 3-50 | Alfanumerické znaky a spojovníky. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | existující | skupina prostředků | 6-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Končit písmenem nebo číslicí. |
> | obsažené | global | 6-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Končit písmenem nebo číslicí. |
> | obory názvů/autorizačních pravidel | namespace | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí písmenem nebo číslicí. |
> | obory názvů/disasterRecoveryConfigs | namespace | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí písmenem nebo číslicí. |
> | obory názvů/eventhubs | namespace | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí písmenem nebo číslicí. |
> | obory názvů/eventhubs/autorizačních pravidel | centrum událostí | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí písmenem nebo číslicí. |
> | obory názvů/eventhubs/consumergroups | centrum událostí | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí písmenem nebo číslicí. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | existující | global | 3-59 | Alfanumerické znaky a spojovníky<br><br>Začíná a končí písmenem nebo číslicí. |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | úlohy | skupina prostředků | 2–64 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | actionGroups | skupina prostředků | 1-260 | Nejde použít:<br>`/&%\?` <br><br>Nemůže končit mezerou nebo tečkou.  |
> | konstrukční | skupina prostředků | 1-260 | Nejde použít:<br>`%&\?/` <br><br>Nemůže končit mezerou nebo tečkou.  |
> | scheduledQueryRules | skupina prostředků | 1-260 | Nejde použít:<br>`*<>%{}&:\\?/#` <br><br>Nemůže končit mezerou nebo tečkou.  |
> | metricAlerts | skupina prostředků | 1-260 | Nejde použít:<br>`*#&+:<>?@%{}\/` <br><br>Nemůže končit mezerou nebo tečkou.  |
> | Upozorněníprotokoluaktivit | skupina prostředků | 1-260 | Nejde použít:<br>`<>*%{}&:\\?+/#` <br><br>Nemůže končit mezerou nebo tečkou.  |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Malá písmena, číslice a spojovníky.<br><br>Začněte s malým písmenem nebo číslicí. |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | trezory | global | 3–24 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Končit písmenem nebo číslicí. Nemůže obsahovat po sobě jdoucí spojovníky. |
> | trezory/tajné klíče | Trezor | 1-127 | Alfanumerické znaky a spojovníky. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | existující | global | 4-22 | Malá písmena a číslice.<br><br>Začíná písmenem. |
> | /Clusters/databáze | cluster | 1-260 | Alfanumerické znaky, spojovníky, mezery a tečky. |
> | /Clusters/databáze/dataconnections | database | 1-40 | Alfanumerické znaky, spojovníky, mezery a tečky. |
> | /Clusters/databáze/eventhubconnections | database | 1-40 | Alfanumerické znaky, spojovníky, mezery a tečky. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | integrationAccounts | skupina prostředků | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts/sestavení | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts / batchConfigurations | účet pro integraci | 1-20 | Alfanumerické znaky. |
> | integrationAccounts/certifikáty | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts/Maps | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts/partneři | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts / rosettanetprocessconfigurations | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts/schémata | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationAccounts/relace | účet pro integraci | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |
> | integrationServiceEnvironments | skupina prostředků | 1–80 | Alfanumerické znaky, spojovníky, tečky a podtržítka. |
> | integrationServiceEnvironments/Inspirujte | prostředí integrační služby | 1–80 | Alfanumerické znaky, spojovníky, tečky a podtržítka. |
> | Zpracovávaný | skupina prostředků | 1–80 | Alfanumerické znaky, pomlčky, podtržítka, tečky a závorky. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | commitmentPlans | skupina prostředků | 1-260 | Nejde použít:<br>`<>*%&:?+/\\`<br><br>Nemůže končit mezerou. |
> | webServices | skupina prostředků | 1-260 | Nejde použít:<br>`<>*%&:?+/\\`<br><br>Nemůže končit mezerou. |
> | pracovní prostory | skupina prostředků | 1-260 | Nejde použít:<br>`<>*%&:?+/\\`<br><br>Nemůže končit mezerou. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | pracovní prostory | skupina prostředků | 3-33 | Alfanumerické znaky a spojovníky. |
> | pracovní prostory a výpočetní prostředky | pracovní prostor | 2–16 | Alfanumerické znaky a spojovníky. |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | userAssignedIdentities | skupina prostředků | 3-128 | Alfanumerické znaky, spojovníky a podtržítka<br><br>Začíná písmenem nebo číslicí. |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | accounts | skupina prostředků | 1-98 (pro název skupiny prostředků a název účtu) | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | MediaServices | skupina prostředků | 3–24 | Malá písmena a číslice. |
> | MediaServices/liveEvents | Media Service | 1-32 | Alfanumerické znaky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |
> | MediaServices/liveEvents/liveOutputs | Živá událost | 1-256 | Alfanumerické znaky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |
> | MediaServices/starají | Media Service | 1-24 | Alfanumerické znaky a spojovníky.<br><br>Začněte s alfanumerickými znaky. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | applicationGateways | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | applicationSecurityGroups | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | azureFirewalls | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Konec s alfanumerickým nebo podtržítkem. |
> | bastionHosts | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | připojení | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | dnsZones | skupina prostředků | 1-63 znaků<br><br>2 až 34 popisky<br><br>Každý popisek je sada znaků oddělená tečkou. Například **contoso.com** má 2 popisky. | Každý popisek může obsahovat alfanumerické znaky, podtržítka a spojovníky.<br><br>Jednotlivé štítky jsou oddělené tečkou. |
> | expressRouteCircuits | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | firewallPolicies | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | firewallPolicies / ruleGroups | Zásady brány firewall | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | frontDoors | global | 5-64 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |
> | frontdoorWebApplicationFirewallPolicies | skupina prostředků | 1-128 | Alfanumerické znaky.<br><br>Začíná písmenem. |
> | loadBalancers | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | loadBalancers/inboundNatRules | Load Balancer | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | localNetworkGateways | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | networkInterfaces | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | networkSecurityGroups | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | networkSecurityGroups/securityRules | Skupina zabezpečení sítě | 1–80 |  Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | networkWatchers | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | privateDnsZones | skupina prostředků | 1-63 znaků<br><br>2 až 34 popisky<br><br>Každý popisek je sada znaků oddělená tečkou. Například **contoso.com** má 2 popisky. | Každý popisek může obsahovat alfanumerické znaky, podtržítka a spojovníky.<br><br>Jednotlivé štítky jsou oddělené tečkou. |
> | privateDnsZones / virtualNetworkLinks | privátní zóna DNS | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | publicIPAddresses | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | publicIPPrefixes | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | routeFilters | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | routeFilters / routeFilterRules | filtr tras | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | routeTables | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | routeTables/trasy | směrovací tabulka | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | serviceEndpointPolicies | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | trafficmanagerprofiles | global | 1–63 | Alfanumerické znaky, spojovníky a tečky.<br><br>Začíná a končí alfanumerickými znaky. |
> | virtualNetworkGateways | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | virtualNetworks | skupina prostředků | 2–64 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | virtualnetworks/podsítě | virtuální síť | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | virtualNetworks/virtualNetworkPeerings | virtuální síť | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | virtualWans | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | vpnGateways | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | vpnGateways / vpnConnections | VPN Gateway | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |
> | vpnSites | skupina prostředků | 1–80 | Alfanumerické znaky, podtržítka, tečky a spojovníky.<br><br>Začněte s alfanumerickými znaky. Koncové alfanumerické znaky nebo podtržítka. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | obsažené | global | 6-50 | Alfanumerické znaky a spojovníky<br><br>Začíná písmenem. Konec s alfanumerickými znaky. |
> | obory názvů/autorizačních pravidel | namespace | 1-256 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Zahajte alfanumerické znaky. |
> | obory názvů/notificationHubs | namespace | 1-260 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Zahajte alfanumerické znaky. |
> | obory názvů/notificationHubs/autorizačních pravidel | Centrum oznámení | 1-256 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Zahajte alfanumerické znaky. |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | existující | skupina prostředků | 4-63 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |
> | pracovní prostory | global | 4-63 | Alfanumerické znaky a spojovníky.<br><br>Začíná a končí alfanumerickými znaky. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Řešení | pracovní prostor | – | Pro řešení vytvořená společností Microsoft musí být název ve vzoru:<br>`SolutionType(WorkspaceName)`<br><br>Pro řešení vytvořená třetími stranami musí být název ve vzoru:<br>`SolutionType[WorkspaceName]`<br><br>Platný název je například:<br>`AntiMalware(contoso-IT)`<br><br>Typ řešení rozlišuje velká a malá písmena. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | řídicí panely | skupina prostředků | 3-160 | Alfanumerické znaky a spojovníky.<br><br>Chcete-li použít zakázané znaky, přidejte značku s názvem **skrytý a** název řídicího panelu, který chcete použít. Portál zobrazí tento název při zobrazení řídicího panelu. |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | workspaceCollections | oblast | 3–63 | Alfanumerické znaky a spojovníky.<br><br>Nelze začínat spojovníkem. Nelze použít po sobě jdoucí spojovníky. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | schopností | oblast | 3–63 | Malá písmena nebo číslice<br><br>Začínat malým písmenem. |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | trezory | skupina prostředků | 2-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. |
> | trezory/backupPolicies | Trezor | 3-150 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Nemůže končit spojovníkem. |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | obsažené | global | 6-50 | Alfanumerické znaky a spojovníky.<br><br>Začněte písmenem. Končit písmenem nebo číslicí. |
> | obory názvů/autorizačních pravidel | namespace | 1-50 |  Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí alfanumerickými znaky. |
> | obory názvů/HybridConnections | namespace | 1-260 | Alfanumerické znaky, tečky, pomlčky, podtržítka a lomítka.<br><br>Začíná a končí alfanumerickými znaky. |
> | obory názvů/HybridConnections/autorizačních pravidel | hybridní připojení | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí alfanumerickými znaky. |
> | obory názvů/WcfRelays | namespace | 1-260 | Alfanumerické znaky, tečky, pomlčky, podtržítka a lomítka.<br><br>Začíná a končí alfanumerickými znaky. |
> | obory názvů/WcfRelays/autorizačních pravidel | WCF Relay | 1-50 | Alfanumerické znaky, tečky, pomlčky a podtržítka.<br><br>Začíná a končí alfanumerickými znaky. |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | nasazení | skupina prostředků | 1-64 | Alfanumerické znaky, podtržítka, kulaté závorky, spojovníky a tečky. |
> | ResourceGroups | předplatné | 1–90 | Alfanumerické znaky, podtržítka, závorky, spojovníky, tečky a znaky Unicode, které odpovídají [dokumentaci regulárního výrazu](/rest/api/resources/resourcegroups/createorupdate).<br><br>Nemůže končit tečkou. |
> | tagName | prostředek | 1-512 | Nejde použít:<br>`<>%&\?/` |
> | tagName/tagValues | název značky | 1-256 | Všechny znaky. |
> | templateSpecs | skupina prostředků | 1–90 | Alfanumerické znaky, podtržítka, kulaté závorky, spojovníky a tečky. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | obsažené | global | 6-50 | Alfanumerické znaky a spojovníky.<br><br>Začněte písmenem. Končit písmenem nebo číslicí.<br><br>Další informace najdete v tématu [Vytvoření oboru názvů](/rest/api/servicebus/create-namespace). |
> | obory názvů/autorizačních pravidel | namespace | 1-50 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Začněte a ukončete pomocí alphnumeric. |
> | obory názvů/disasterRecoveryConfigs | global | 6-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Konec s alfanumerickými znaky. |
> | obory názvů/migrationConfigurations | namespace |  | Měla by být vždy **$Default**. |
> | obory názvů/fronty | namespace | 1-260 | Alfanumerické znaky, tečky, pomlčky, podtržítka a lomítka.<br><br>Začíná a končí alfanumerickými znaky. |
> | obory názvů/fronty/autorizačních pravidel | fronta | 1-50 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Začněte a ukončete pomocí alphnumeric. |
> | obory názvů/témata | namespace | 1-260 | Alfanumerické znaky, tečky, pomlčky, podtržítka a lomítka.<br><br>Začíná a končí alfanumerickými znaky. |
> | obory názvů/témata/autorizačních pravidel | téma | 1-50 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Začněte a ukončete pomocí alphnumeric. |
> | obory názvů/témata/předplatná | téma | 1-50 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Začněte a ukončete pomocí alphnumeric. |
> | obory názvů/témata/předplatná/pravidla | předplatné | 1-50 | Alfanumerické znaky, tečky, spojovníky a podtržítka.<br><br>Začněte a ukončete pomocí alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | existující | oblast | 4-23 | Malá písmena, číslice a spojovníky.<br><br>Začínat malým písmenem. Ukončí s malým písmenem nebo číslicí. |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | signalR | global | 3–63 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Končit písmenem nebo číslicí.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | managedInstances | global | 1–63 | Malá písmena, číslice a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. <br><br> Nemůže obsahovat žádné speciální znaky, například `@` . |
> | servery | global | 1–63 | Malá písmena, číslice a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. |
> | servery/správci | server |  | Musí být `ActiveDirectory` . |
> | servery/databáze | server | 1-128 | Nejde použít:<br>`<>*%&:\/?`<br><br>Nemůže končit tečkou nebo mezerou. |
> | servery/databáze/syncGroups | database | 1-150 | Alfanumerické znaky, spojovníky a podtržítka. |
> | servery/elasticPools | server | 1-128 | Nejde použít:<br>`<>*%&:\/?`<br><br>Nemůže končit tečkou nebo mezerou. |
> | servery/failoverGroups | global | 1–63 | Malá písmena, číslice a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. |
> | servery/firewallRules | server | 1-128 | Nejde použít:<br>`<>*%&:;\/?`<br><br>Nemůže končit tečkou. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3–24 | Malá písmena a číslice. |
> | storageAccounts/blobServices | účet úložiště |  | Musí být `default` . |
> | storageAccounts/blobServices/kontejnery | účet úložiště | 3–63 | Malá písmena, číslice a spojovníky.<br><br>Začněte s malým písmenem nebo číslicí. Nelze použít po sobě jdoucí spojovníky. |
> | storageAccounts/služby | účet úložiště |  | Musí být `default` . |
> | storageAccounts/služby/služby/sdílené složky | účet úložiště | 3–63 | Malá písmena, číslice a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. Nelze použít po sobě jdoucí spojovníky. |
> | storageAccounts/managementPolicies | účet úložiště |  | Musí být `default` . |
> | blob | kontejner | 1–1024 | Všechny znaky adresy URL, rozlišovat velikost písmen |
> | fronta | účet úložiště | 3–63 | Malá písmena, číslice a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. Nelze použít po sobě jdoucí spojovníky. |
> | tabulka | účet úložiště | 3–63 | Alfanumerické znaky.<br><br>Začíná písmenem. |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | storageSyncServices | skupina prostředků | 1-260 | Alfanumerické znaky, mezery, tečky, spojovníky a podtržítka.<br><br>Nemůže končit tečkou nebo mezerou. |
> | storageSyncServices / syncGroups | Služba synchronizace úložiště | 1-260 | Alfanumerické znaky, mezery, tečky, spojovníky a podtržítka.<br><br>Nemůže končit tečkou nebo mezerou. |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | jednatel | skupina prostředků | 2-50 | Alfanumerické znaky a spojovníky.<br><br>Začíná písmenem. Konec s alfanumerickými znaky. |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | streamingjobs | skupina prostředků | 3–63 | Alfanumerické znaky, spojovníky a podtržítka. |
> | streamingjobs/funkce | úloha streamování | 3–63 | Alfanumerické znaky, spojovníky a podtržítka. |
> | streamingjobs/vstupy | úloha streamování | 3–63 | Alfanumerické znaky, spojovníky a podtržítka. |
> | streamingjobs/výstupy | úloha streamování | 3–63 | Alfanumerické znaky, spojovníky a podtržítka. |
> | streamingjobs/transformace | úloha streamování | 3–63 | Alfanumerické znaky, spojovníky a podtržítka. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | Environment | skupina prostředků | 1–90 | Nejde použít:<br>`'<>%&:\?/#` |
> | prostředí/accessPolicies | environment | 1–90 | Nejde použít:<br> `'<>%&:\?/#` |
> | prostředí/eventSources | environment | 1–90 | Nejde použít:<br>`'<>%&:\?/#` |
> | prostředí/referenceDataSets | environment | 3–63 | Alfanumerické znaky |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Entita | Rozsah | Délka | Platné znaky |
> | --- | --- | --- | --- |
> | certifikáty | skupina prostředků | 1-260 | Nejde použít:<br>`/` <br><br>Nemůže končit mezerou nebo tečkou.  | 
> | serverových farem | skupina prostředků | 1-40 | Alfanumerické znaky a spojovníky. |
> | místa | globální nebo na doménu. Viz poznámka níže. | 2-60 | Obsahuje alfanumerické znaky a spojovníky.<br><br>Nelze začínat ani končit spojovníkem. |
> | lokality/sloty | webovém | 2-59 | Alfanumerické znaky a spojovníky. |

> [!NOTE]
> Web musí mít globálně jedinečnou adresu URL. Při vytváření webu, který používá plán hostování, je adresa URL `http://<app-name>.azurewebsites.net` . Název aplikace musí být globálně jedinečný. Když vytvoříte web, který používá App Service Environment, musí být název aplikace jedinečný v rámci [domény pro App Service Environment](../../app-service/environment/using-an-ase.md#app-access). V obou případech je adresa URL webu globálně jedinečná.
>
> Azure Functions mají stejná pravidla a omezení pro pojmenování jako Microsoft. Web/Web.

## <a name="next-steps"></a>Další kroky

Doporučení k pojmenování prostředků najdete v tématu [připraveno: Doporučené konvence pojmenování a označování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
