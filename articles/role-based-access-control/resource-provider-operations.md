---
title: Operace poskytovatele prostředků Azure Resource Manager | Microsoft Docs
description: Seznam operací, které jsou k dispozici pro poskytovatele prostředků Microsoft Azure Správce prostředků
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9d4b4134fa26fd2cb904a862ac16544873bf8bcb
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934465"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager operace poskytovatele prostředků

Tento článek obsahuje seznam operací, které jsou k dispozici pro každého poskytovatele prostředků Azure Resource Manager. Tyto operace se dají použít ve [vlastních rolích](custom-roles.md) k zajištění podrobného [řízení přístupu na základě role (RBAC)](overview.md) k prostředkům v Azure. Řetězce operací mají následující formát: `{Company}.{ProviderName}/{resourceType}/{action}`. Seznam způsobů mapování oborů názvů poskytovatelů prostředků na služby Azure najdete v tématu [přiřazení poskytovatele prostředků ke službě](../azure-resource-manager/azure-services-resource-providers.md).

Operace poskytovatele prostředků se vždycky víjejí. Pokud chcete získat nejnovější operace, použijte seznam operací [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) nebo [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. AAD/domainServices/DELETE | Odstranit doménovou službu |
> | Akce | Microsoft. AAD/domainServices/oucontainer/DELETE | Odstranit kontejner organizační jednotky |
> | Akce | Microsoft. AAD/domainServices/oucontainer/Read | Čtení kontejnerů organizační jednotky |
> | Akce | Microsoft. AAD/domainServices/oucontainer/Write | Zapsat kontejner organizační jednotky |
> | Akce | Microsoft. AAD/domainServices/Read | Čtení doménových služeb |
> | Akce | Microsoft. AAD/domainServices/Write | Zápis doménové služby |
> | Akce | Microsoft. AAD/Locations/operationresults/Read |  |
> | Akce | Microsoft. AAD/Operations/Ready |  |
> | Akce | Microsoft. AAD/registrace/akce | Registrovat doménovou službu |
> | Akce | Microsoft. AAD/zrušit registraci/akce | Zrušit registraci doménové služby |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | microsoft.aadiam/diagnosticsettings/delete | Deleting a diagnostic setting |
> | Akce | microsoft.aadiam/diagnosticsettings/read | Reading a diagnostic setting |
> | Akce | microsoft.aadiam/diagnosticsettings/write | Writing a diagnostic setting |
> | Akce | microsoft.aadiam/diagnosticsettingscategories/read | Reading a diagnostic setting categories |
> | Akce | Microsoft. aadiam/metricDefinitions/Read | Čtení definic metrik na úrovni tenanta |
> | Akce | Microsoft. aadiam/metriky/číst | Čtení metrik na úrovni tenanta |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Addons/operations/read | Načte podporované operace RP. |
> | Akce | Microsoft. addons/Register/Action | Zaregistrovat zadané předplatné pro Microsoft. addons |
> | Akce | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Zobrazí seznam aktuálních informací o plánu podpory pro zadané předplatné. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Odebere zadaný kanonický plán podpory. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/read | Získá zadaný kanonický stav plánu podpory. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/write | Přidá zadaný typ plánu kanonické podpory. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ADHybridHealthService/addsservices/action | Vytvořte novou doménovou strukturu pro tenanta. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Načte všechny servery pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/alerts/read | Získá podrobnosti o výstrahách pro doménovou strukturu, jako je AlertId, datum vyvolání výstrahy, poslední zjištěná výstraha, popis výstrahy, Poslední aktualizace, úroveň výstrahy, stav výstrahy, odkazy na řešení potíží s výstrahami atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/configuration/read | Získá konfiguraci služby pro doménovou strukturu. Příklad: název doménové struktury, funkční úroveň, role FSMO hlavního serveru pro pojmenovávání domén, role FSMO hlavního serveru schémat atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/delete | Odstraní službu a její servery spolu s daty o stavu. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Získá podrobnosti o doménách a lokalitách pro doménovou strukturu. Příklad: stav, aktivní výstrahy, vyřešené výstrahy, vlastnosti, jako je úroveň funkčnosti domény, doménová struktura, hlavní server infrastruktury, primární řadič domény, hlavní server RID atd.  |
> | Akce | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Získá nastavení předvolby pro uživatele pro doménovou strukturu.<br>Příklad: MetricCounterName, jako je ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Nastavení pro grafy uživatelského rozhraní atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Získá souhrn doménové struktury pro danou doménovou strukturu, jako je název doménové struktury, počet domén pod touto doménovou strukturou, počet lokalit a podrobností o lokalitách atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Získá seznam podporovaných metrik pro danou službu.<br>Například uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu za službu ADFS.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomainService.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do Azure AD pro službu ADSync. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | V případě služby toto rozhraní API získá informace o metrikách.<br>Toto rozhraní API se dá použít například k získání informací souvisejících s: uzamčení účtu extranetu, celkový počet neúspěšných žádostí, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu a další pro ADFederation službu.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Toto rozhraní API získá seznam všech integrovaných ADDomainServices pro tenanta Premium. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/read | Získá podrobnosti o službě pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Získá podrobnosti o replikaci pro všechny servery pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Získá počet řadičů domény a jejich chyby replikace, pokud existují. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Načte Úplný seznam řadičů domény spolu s podrobnostmi o replikaci pro danou doménovou strukturu. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Přidejte instanci serveru do služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Během registrace serveru ADDomainService se toto rozhraní API volá, aby se získaly přihlašovací údaje pro nové servery. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Odstraní server pro danou službu a tenanta. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/write | Vytvoří nebo aktualizuje instanci ADDomainService pro tenanta. |
> | Akce | Microsoft. ADHybridHealthService/Configuration/Action | Aktualizuje konfiguraci tenanta. |
> | Akce | Microsoft. ADHybridHealthService/Configuration/Read | Přečte konfiguraci tenanta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/write | Vytvoří konfiguraci tenanta. |
> | Akce | Microsoft. ADHybridHealthService/logs/obsah/číst | Načte obsah protokolů instalace a registrace agenta uložených v objektu BLOB. |
> | Akce | Microsoft. ADHybridHealthService/logs/číst | Získá protokoly instalace a registrace agenta pro tenanta. |
> | Akce | Microsoft.ADHybridHealthService/operations/read | Získá seznam operací podporovaných systémem. |
> | Akce | Microsoft.ADHybridHealthService/register/action | Zaregistruje poskytovatele prostředků ADHybrid Health Service a povolí vytvoření prostředku ADHybrid Health Service. |
> | Akce | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Získá seznam dostupných oblastí, které používá DevOps k podpoře incidentů zákazníků. |
> | Akce | Microsoft. ADHybridHealthService/Reports/BadPassword/Read | Získá seznam špatných pokusů o zadání hesla pro všechny uživatele ve službě Active Directory služba FS (Federation Service). |
> | Akce | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Získá identifikátor URI pro SAS objektu BLOB obsahující stav a konečný výsledek nově zařazené úlohy ve frontě pro frekvenci špatného uživatelského jména nebo hesla na základě ID uživatele a počtu identifikátorů hesel za den pro daného tenanta. |
> | Akce | Microsoft. ADHybridHealthService/Reports/consentedtodevopstenants/Read | Získá seznam odsouhlasených klientů DevOps. Obvykle se používá pro zákaznickou podporu. |
> | Akce | Microsoft.ADHybridHealthService/reports/isdevops/read | Načte hodnotu, která označuje, jestli je tenant DevOps nebo ne. |
> | Akce | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Aktualizuje ID uživatele (objectID) pro vybraného tenanta operace vývoj. |
> | Akce | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Získá vybrané nasazení pro daného tenanta. |
> | Akce | Microsoft. ADHybridHealthService/Reports/tenantassigneddeployment/Read | Dané ID tenanta Získá umístění úložiště tenanta. |
> | Akce | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Získá geografické umístění, ze kterého budou k datům přicházet. |
> | Akce | Microsoft. ADHybridHealthService/Services/Action | Aktualizuje instanci služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrahy pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrahy pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Vzhledem k názvu funkce ověří, jestli má služba všechno, co je potřeba k použití této funkce. |
> | Akce | Microsoft.ADHybridHealthService/services/delete | Odstraní instanci služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/services/exporterrors/read | Získá chyby exportu pro danou synchronizační službu. |
> | Akce | Microsoft.ADHybridHealthService/services/exportstatus/read | Získá stav exportu pro danou službu. |
> | Akce | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Vrátí zpětnou vazbu k dané službě a serveru. |
> | Akce | Microsoft. ADHybridHealthService/Services/metricmetadata/Read | Získá seznam podporovaných metrik pro danou službu.<br>Například uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu za službu ADFS.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomainService.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do Azure AD pro službu ADSync. |
> | Akce | Microsoft. ADHybridHealthService/Services/metriky/skupiny/průměr/čtení | Pro danou službu získá toto rozhraní API průměrnou metriku pro danou službu.<br>Toto rozhraní API se dá použít například k získání informací souvisejících s: uzamčení účtu extranetu, celkový počet neúspěšných žádostí, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu a další pro ADFederation službu.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Akce | Microsoft. ADHybridHealthService/Services/metriky/skupiny/číst | V případě služby toto rozhraní API získá informace o metrikách.<br>Toto rozhraní API se dá použít například k získání informací souvisejících s: uzamčení účtu extranetu, celkový počet neúspěšných žádostí, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu a další pro ADFederation službu.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Akce | Microsoft. ADHybridHealthService/Services/metriky/skupiny/Sum/Read | Rozhraní API pro danou službu získá agregované zobrazení metrik pro danou službu.<br>Toto rozhraní API se dá použít například k získání informací souvisejících s: uzamčení účtu extranetu, celkový počet neúspěšných žádostí, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu a další pro ADFederation službu.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Akce | Microsoft. ADHybridHealthService/Services/monitoringconfiguration/Write | Přidá nebo aktualizuje konfiguraci monitorování pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Získá konfigurace monitorování pro danou službu. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Přidejte nebo aktualizujte konfigurace monitorování pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/premiumcheck/read | Toto rozhraní API získá seznam všech integrovaných služeb pro tenanta Premium. |
> | Akce | Microsoft.ADHybridHealthService/services/read | Přečte instance služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Načte všechny identifikátory URI pro sestavu rizikových IP adres za posledních 7 dnů. |
> | Akce | Microsoft. ADHybridHealthService/Services/Reports/Details/Read | Získá sestavu prvních 50 uživatelů s chybami špatného hesla z posledních 7 dnů. |
> | Akce | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Generuje sestavu rizikových IP adres a vrátí identifikátor URI, na který odkazuje. |
> | Akce | Microsoft. ADHybridHealthService/Services/Servicemembers/Action | Vytvoří instanci serveru ve službě. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Přečte výstrahy pro server. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Během registrace serveru se toto rozhraní API volá, aby se získaly přihlašovací údaje pro nové servery. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Pro daný server toto rozhraní API získá seznam DataType, které jsou odesílány servery a nejnovější čas pro každé nahrání. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/delete | Odstraní instanci serveru ve službě. |
> | Akce | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/Read | Získá podrobnosti o chybě exportu synchronizace pro danou synchronizační službu. |
> | Akce | Microsoft. ADHybridHealthService/Services/Servicemembers/metriky/skupiny/číst | V případě služby toto rozhraní API získá informace o metrikách.<br>Toto rozhraní API se dá použít například k získání informací souvisejících s: uzamčení účtu extranetu, celkový počet neúspěšných žádostí, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu a další pro ADFederation službu.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Akce | Microsoft. ADHybridHealthService/Services/Servicemembers/metriky/číst | Získá seznam konektorů a názvy profilů spuštění pro daného člena služby a služby. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/read | Přečte instanci serveru ve službě. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Načte konfiguraci služby pro daného tenanta. |
> | Akce | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/Read | Získá stav seznamu povolených funkcí pro daného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/services/write | Vytvoří instanci služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků ADHybrid Health Service. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Advisor/Configurations/Ready | Získat konfigurace |
> | Akce | Microsoft. Advisor/Configurations/Write | Vytvoří nebo aktualizuje konfiguraci. |
> | Akce | Microsoft. Advisor/generateRecommendations/Action | Získá stav doporučení pro generování doporučení. |
> | Akce | Microsoft. Advisor/generateRecommendations/Read | Získá stav doporučení pro generování doporučení. |
> | Akce | Microsoft. Advisor/metadata/číst | Získat metadata |
> | Akce | Microsoft. Advisor/Operations/Ready | Načte operace pro Microsoft Advisor. |
> | Akce | Microsoft. Advisor/doporučení/k dispozici/akce | Nové doporučení je dostupné v Microsoft Advisoru. |
> | Akce | Microsoft. Advisor/doporučení/číst | Čte doporučení |
> | Akce | Microsoft. Advisor/doporučení/potlačení/odstranění | Odstraní potlačení. |
> | Akce | Microsoft. Advisor/doporučení/potlačení/čtení | Získá potlačení. |
> | Akce | Microsoft. Advisor/doporučení/potlačení/zápis | Vytvoří nebo aktualizuje potlačení. |
> | Akce | Microsoft. Advisor/registr/Action | Zaregistruje předplatné pro Microsoft Advisor. |
> | Akce | Microsoft. Advisor/potlačit/odstranit | Odstraní potlačení. |
> | Akce | Microsoft. Advisor/potlačení/čtení | Získá potlačení. |
> | Akce | Microsoft. Advisor/potlačení/zápis | Vytvoří nebo aktualizuje potlačení. |
> | Akce | Microsoft. Advisor/zrušit registraci/akce | Zruší registraci předplatného služby Microsoft Advisor. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. AlertsManagement/actionRules/DELETE | Odstraní pravidlo akce v daném předplatném. |
> | Akce | Microsoft. AlertsManagement/actionRules/Read | Získejte všechna pravidla akcí pro vstupní filtry. |
> | Akce | Microsoft.AlertsManagement/actionRules/write | Vytvořit nebo aktualizovat pravidlo akce v daném předplatném |
> | Akce | Microsoft. AlertsManagement/Alerts/changestate/Action | Změňte stav výstrahy. |
> | Akce | Microsoft. AlertsManagement, výstrahy/Diagnostika/číst | Získat veškerou diagnostiku výstrahy |
> | Akce | Microsoft. AlertsManagement/Alerts/History/číst | Získat historii výstrahy |
> | Akce | Microsoft. AlertsManagement/výstrahy/čtení | Získá všechny výstrahy pro vstupní filtry. |
> | Akce | Microsoft.AlertsManagement/alertsList/read | Získat všechny výstrahy pro vstupní filtry v rámci předplatných |
> | Akce | Microsoft. AlertsManagement/alertsMetaData/Read | Pro vstupní parametr získáte metadata metadat výstrahy. |
> | Akce | Microsoft.AlertsManagement/alertsSummary/read | Získání souhrnu výstrah |
> | Akce | Microsoft. AlertsManagement/alertsSummaryList/Read | Získat souhrn výstrah v rámci předplatných |
> | Akce | Microsoft.AlertsManagement/Operations/read | Přečte poskytnuté operace. |
> | Akce | Microsoft.AlertsManagement/register/action | Zaregistruje předplatné pro správu výstrah společnosti Microsoft. |
> | Akce | Microsoft. AlertsManagement/smartDetectorAlertRules/DELETE | Odstranit pravidlo výstrahy inteligentního detektoru v daném předplatném |
> | Akce | Microsoft. AlertsManagement/smartDetectorAlertRules/Read | Získat všechna pravidla upozornění inteligentního detektoru pro vstupní filtry |
> | Akce | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Vytvoří nebo aktualizuje pravidlo výstrahy inteligentního detektoru v daném předplatném. |
> | Akce | Microsoft. AlertsManagement/smartGroups/changestate/Action | Změna stavu inteligentní skupiny |
> | Akce | Microsoft. AlertsManagement/smartGroups/History/Read | Získat historii inteligentní skupiny |
> | Akce | Microsoft.AlertsManagement/smartGroups/read | Získání všech inteligentních skupin pro vstupní filtry |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Kontroluje, zda je zadaný název Analysis Server platný a nepoužívá se. |
> | Akce | Microsoft. AnalysisServices/Locations/operationresults/Read | Načte informace o výsledku zadané operace. |
> | Akce | Microsoft.AnalysisServices/locations/operationstatuses/read | Načte informace o stavu zadané operace. |
> | Akce | Microsoft. AnalysisServices/Operations/Read | Načte informace o operacích. |
> | Akce | Microsoft. AnalysisServices/Register/Action | Zaregistruje poskytovatele prostředků Analysis Services. |
> | Akce | Microsoft.AnalysisServices/servers/delete | Odstraní Analysis Server. |
> | Akce | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Vypíše stav brány přidružené k serveru. |
> | Akce | Microsoft. AnalysisServices/servery/číst | Načte informace o zadaném Analysis Server. |
> | Akce | Microsoft. AnalysisServices/servery/pokračovat/akce | Obnoví Analysis Server. |
> | Akce | Microsoft. AnalysisServices/servery/SKU/číst | Načíst dostupné informace o SKU pro server |
> | Akce | Microsoft. AnalysisServices/servery/pozastavit/akce | Pozastaví Analysis Server. |
> | Akce | Microsoft. AnalysisServices/servery/Write | Vytvoří nebo aktualizuje zadaný Analysis Server. |
> | Akce | Microsoft. AnalysisServices/SKU/číst | Načte informace o SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. ApiManagement/checkNameAvailability/Read | Zkontroluje, jestli je zadaný název služby k dispozici. |
> | Akce | Microsoft.ApiManagement/operations/read | Načte všechny operace rozhraní API, které jsou k dispozici pro prostředek Microsoft. ApiManagement. |
> | Akce | Microsoft. ApiManagement/Register/Action | Registrace předplatného pro poskytovatele prostředků Microsoft. ApiManagement |
> | Akce | Microsoft. ApiManagement/Reports/Read | Získejte sestavy agregované podle časových období, geografické oblasti, vývojářů, produktů, rozhraní API, operací, předplatných a byRequest. |
> | Akce | Microsoft. ApiManagement/Service/API/DELETE | Odstraní zadané rozhraní API instance služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/API/Diagnostics/DELETE | Odstraní zadanou diagnostiku z rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Diagnostics/Read | Vypíše veškerou diagnostiku rozhraní API. nebo získá podrobnosti diagnostiky pro rozhraní API zadané jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/API/Diagnostics/Write | Vytvoří novou diagnostiku pro rozhraní API nebo aktualizuje stávající. nebo aktualizuje podrobnosti diagnostiky pro rozhraní API zadané jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/Attachments/Attachments/DELETE | Odstraní zadaný komentář z problému. |
> | Akce | Microsoft. ApiManagement/Service/rozhraní API/problémy/přílohy/číst | Vypíše všechny přílohy problému přidruženého k zadanému rozhraní API. nebo získá podrobnosti o příloze problému pro rozhraní API zadané jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/Attachments/Write | Vytvoří novou přílohu pro daný problém v rozhraní API nebo aktualizuje stávající. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/Comments/DELETE | Odstraní zadaný komentář z problému. |
> | Akce | Microsoft. ApiManagement/Service/API/problémy/komentáře/čtení | Zobrazí seznam všech komentářů k problému souvisejícímu se zadaným rozhraním API. nebo získá podrobnosti poznámky k problému pro rozhraní API zadané jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/Comments/Write | Vytvoří nový komentář k problému v rozhraní API nebo aktualizuje stávající. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/DELETE | Odstraní zadaný problém z rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/Read | Zobrazí seznam všech problémů přidružených k zadanému rozhraní API. nebo získá podrobnosti problému pro rozhraní API zadané jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/API/probléms/Write | Vytvoří nový problém pro rozhraní API nebo aktualizuje stávající. nebo aktualizuje stávající problém pro rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/DELETE | Odstraní zadanou operaci v rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/policies/DELETE | Odstraní konfiguraci zásad na operaci rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/policies/Ready | Získejte seznam konfigurace zásad na úrovni operace rozhraní API. nebo si na úrovni operace rozhraní API získejte konfiguraci zásad. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/policies/Write | Vytvoří nebo aktualizuje konfiguraci zásad pro úroveň operace rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Policy/DELETE | Odstranit konfiguraci zásad na úrovni provozu |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Policy/Read | Získat konfiguraci zásad na úrovni provozu |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Policy/Write | Vytvořit konfiguraci zásad na úrovni provozu |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Read | Vypíše kolekci operací pro zadané rozhraní API. nebo získá podrobné informace o operaci rozhraní API zadané jeho identifikátorem. |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odpojte značku od operace. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Tags/Read | Zobrazí seznam všech značek přidružených k operaci. nebo získat značku přidruženou k operaci. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Tags/Write | Přiřaďte k operaci značku. |
> | Akce | Microsoft. ApiManagement/Service/API/Operations/Write | Vytvoří novou operaci v rozhraní API nebo aktualizuje stávající. nebo aktualizuje podrobnosti operace v rozhraní API určeném identifikátorem. |
> | Akce | Microsoft.ApiManagement/service/apis/operationsByTags/read | Vypíše kolekci operací přidružených k značkám. |
> | Akce | Microsoft. ApiManagement/Service/API/policies/DELETE | Odstraní konfiguraci zásad v rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/policies/Read | Získejte konfiguraci zásad na úrovni rozhraní API. nebo získejte konfiguraci zásad na úrovni rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/policies/Write | Vytvoří nebo aktualizuje konfiguraci zásad pro rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Policy/DELETE | Odstranění konfigurace zásad na úrovni rozhraní API |
> | Akce | Microsoft. ApiManagement/Service/API/Policy/Read | Získat konfiguraci zásad na úrovni rozhraní API |
> | Akce | Microsoft. ApiManagement/Service/API/Policy/Write | Vytvoření konfigurace zásad na úrovni rozhraní API |
> | Akce | Microsoft. ApiManagement/Service/API/Products/Read | Zobrazí seznam všech produktů, které jsou součástí rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Read | Vypíše všechna rozhraní API instance služby API Management. nebo získá podrobnosti rozhraní API určeného identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/API/releases/DELETE | Odebere všechny verze rozhraní API nebo odstraní určenou verzi v rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/releases/Read | Zobrazí seznam všech verzí rozhraní API.<br>Při vytváření Revize rozhraní API se vytvoří vydaná verze rozhraní API.<br>Verze se používají také k vrácení zpět k předchozím revizím.<br>Výsledky budou stránkovaná a můžou být omezené pomocí parametrů $top a $skip.<br>nebo vrátí podrobnosti verze rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Release/Write | Vytvoří novou verzi rozhraní API. nebo aktualizuje podrobnosti vydání rozhraní API určeného identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/rozhraní API/revize/odstranit | Odebere všechny revize rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/revize/číst | Zobrazí seznam všech revizí rozhraní API. |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/delete | Odstraní konfiguraci schématu v rozhraní API. |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/read | Získejte konfiguraci schématu na úrovni rozhraní API. nebo získáte konfiguraci schématu na úrovni rozhraní API. |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/write | Vytvoří nebo aktualizuje konfiguraci schématu pro rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/tagDescriptions/DELETE | Odstraní popis značky pro rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/tagDescriptions/Read | Zobrazí seznam všech popisů značek v oboru rozhraní API. Model podobný nástroji Swagger – tagDescription je definovaný na úrovni rozhraní API, ale značka může být přiřazená operacím nebo získání popisu značky v oboru rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/tagDescriptions/Write | Vytvoří nebo aktualizuje popis značky v oboru rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Tags/DELETE | Odpojte značku od rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Tags/Read | Zobrazí seznam všech značek přidružených k rozhraní API. nebo získat značku přidruženou k rozhraní API. |
> | Akce | Microsoft. ApiManagement/Service/API/Tags/Write | Přiřaďte k rozhraní API značku. |
> | Akce | Microsoft. ApiManagement/Service/API/Write | Vytvoří nové nebo aktualizuje existující zadané rozhraní API instance služby API Management. nebo aktualizuje zadané rozhraní API instance služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/apisByTags/Read | Obsahuje kolekci rozhraní API přidružených k značkám. |
> | Akce | Microsoft.ApiManagement/service/apiVersionSets/delete | Odstraní konkrétní sadu verzí rozhraní API. |
> | Akce | Microsoft.ApiManagement/service/apiVersionSets/read | Vypíše kolekci sad verzí rozhraní API v zadané instanci služby. nebo získá Podrobnosti sady verzí rozhraní API určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/apiVersionSets/verze/číst | Získat seznam entit verze |
> | Akce | Microsoft.ApiManagement/service/apiVersionSets/write | Vytvoří nebo aktualizuje sadu verzí rozhraní API. nebo aktualizuje podrobnosti rozhraní API VersionSet určeného identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/applynetworkconfigurationupdates/Action | Aktualizuje prostředky Microsoft. ApiManagement spuštěné v Virtual Network k výběru aktualizovaných nastavení sítě. |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/delete | Odstraní konkrétní instanci autorizačního serveru. |
> | Akce | Microsoft. ApiManagement/Service/authorizationServers/listSecrets/Action | Získá tajné kódy pro autorizační Server. |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/read | Vypíše kolekci autorizačních serverů definovaných v rámci instance služby. nebo získá podrobnosti autorizačního serveru bez tajných kódů. |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/write | Vytvoří nový autorizační Server nebo aktualizuje stávající autorizační Server. nebo aktualizuje podrobnosti autorizačního serveru určeného identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/back-end/DELETE | Odstraní zadaný back-end. |
> | Akce | Microsoft. ApiManagement/Service/back-endy/číst | Vypíše kolekci back-endy v zadané instanci služby. nebo získá podrobnosti o back-endu zadaném jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/back-endy/znovu připojit/akce | Oznamuje proxy serveru APIM, aby po uplynutí zadaného časového limitu vytvořil nové připojení k back-endu. Pokud nebyl zadán žádný časový limit, bude použit časový limit 2 minut. |
> | Akce | Microsoft. ApiManagement/Service/back-end/Write | Vytvoří nebo aktualizuje back-end. nebo aktualizuje stávající back-end. |
> | Akce | Microsoft. ApiManagement/Service/Backup/Action | Služba Backup API Management do zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | Akce | Microsoft.ApiManagement/service/caches/delete | Odstraní konkrétní mezipaměť. |
> | Akce | Microsoft. ApiManagement/Service/caches/Read | Vypíše kolekci všech externích mezipamětí v zadané instanci služby. nebo získá podrobnosti mezipaměti určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/caches/Write | Vytvoří nebo aktualizuje externí mezipaměť, která se má použít v instanci služby API Management. nebo aktualizuje podrobnosti mezipaměti určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/Certificates/DELETE | Odstraní konkrétní certifikát. |
> | Akce | Microsoft. ApiManagement/Service/Certificates/Read | Vypíše kolekci všech certifikátů v zadané instanci služby. nebo získá podrobnosti certifikátu určeného jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/Certificates/Write | Vytvoří nebo aktualizuje certifikát používaný k ověřování pomocí back-endu. |
> | Akce | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Odebere zadanou položku obsahu. |
> | Akce | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Vrátí seznam položek obsahu nebo vrátí podrobnosti o položce obsahu. |
> | Akce | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Vytvoří novou položku obsahu nebo aktualizuje zadanou položku obsahu. |
> | Akce | Microsoft.ApiManagement/service/contentTypes/read | Vrátí seznam typů obsahu. |
> | Akce | Microsoft.ApiManagement/service/delete | Odstranit instanci služby API Management |
> | Akce | Microsoft. ApiManagement/Service/Diagnostics/DELETE | Odstraní zadanou diagnostiku. |
> | Akce | Microsoft. ApiManagement/Service/Diagnostics/Read | Zobrazí všechny diagnostiky instance služby API Management. nebo získá podrobnosti diagnostiky určené jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/Diagnostics/Write | Vytvoří novou diagnostiku nebo aktualizuje stávající. nebo aktualizuje podrobnosti diagnostiky určeného identifikátorem. |
> | Akce | Microsoft. ApiManagement/služba/brány/akce | Načte konfiguraci brány. nebo aktualizuje prezenční signál brány. |
> | Akce | Microsoft. ApiManagement/Service/gateways/API/DELETE | Odstraní zadané rozhraní API ze zadané brány. |
> | Akce | Microsoft. ApiManagement/Service/gateways/API/Read | Zobrazí seznam kolekcí rozhraní API přidružených k bráně. |
> | Akce | Microsoft. ApiManagement/Service/gateways/API/Write | Přidá rozhraní API k zadané bráně. |
> | Akce | Microsoft. ApiManagement/Service/gateways/DELETE | Odstraní konkrétní bránu. |
> | Akce | Microsoft. ApiManagement/Service/gateways/hostnameConfigurations/Read | Zobrazuje kolekci konfigurací názvů hostitelů pro zadanou bránu. |
> | Akce | Microsoft. ApiManagement/Service/gateways/Key/Action | Načte klíče brány. |
> | Akce | Microsoft. ApiManagement/služba/brány/číst | Vypíše kolekci bran zaregistrovaných u instance služby. nebo získá podrobnosti o bráně určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/gateways/regeneratePrimaryKey/Action | Znovu vygeneruje klíč primární brány invalidationg všechny tokeny, které se s ním vytvořily. |
> | Akce | Microsoft. ApiManagement/Service/gateways/regenerateSecondaryKey/Action | Znovu vygeneruje klíč sekundární brány invalidationg všechny tokeny, které se s ním vytvořily. |
> | Akce | Microsoft. ApiManagement/Service/gateways/token/Action | Získá autorizační token sdíleného přístupu pro bránu. |
> | Akce | Microsoft. ApiManagement/Service/gateways/Write | Vytvoří nebo aktualizuje bránu, která se má použít v instanci API managementu. nebo aktualizuje podrobnosti o bráně určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/getssotoken/Action | Získá token jednotného přihlašování, který se dá použít k přihlášení k portálu služby API Management starší verze jako správce. |
> | Akce | Microsoft. ApiManagement/Service/Groups/DELETE | Odstraní konkrétní skupinu instance služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/Groups/Read | Vypíše kolekci skupin definovaných v rámci instance služby. nebo získá podrobnosti skupiny určené identifikátorem. |
> | Akce | Microsoft.ApiManagement/service/groups/users/delete | Odebere stávajícího uživatele z existující skupiny. |
> | Akce | Microsoft. ApiManagement/Service/Groups/Users/Read | Zobrazuje kolekci uživatelských entit přidružených ke skupině. |
> | Akce | Microsoft. ApiManagement/Service/Groups/Users/Write | Přidat existujícího uživatele do existující skupiny |
> | Akce | Microsoft. ApiManagement/Service/Groups/Write | Vytvoří nebo aktualizuje skupinu. nebo aktualizuje podrobnosti skupiny určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/identityProviders/DELETE | Odstraní zadanou konfiguraci zprostředkovatele identity. |
> | Akce | Microsoft. ApiManagement/Service/identityProviders/listSecrets/Action | Načte tajné klíče zprostředkovatele identity. |
> | Akce | Microsoft. ApiManagement/Service/identityProviders/Read | Vypíše kolekci zprostředkovatele identity nakonfigurovanou v zadané instanci služby. nebo získá podrobné informace o konfiguraci zprostředkovatele identity bez tajných kódů. |
> | Akce | Microsoft. ApiManagement/Service/identityProviders/Write | Vytvoří nebo aktualizuje konfiguraci IdentityProvider. nebo aktualizuje existující konfiguraci IdentityProvider. |
> | Akce | Microsoft. ApiManagement/Service/problémy/číst | Vypíše kolekci problémů v zadané instanci služby. nebo získá podrobnosti o API Management problému. |
> | Akce | Microsoft. ApiManagement/Service/Locations/NetworkStatus/Read | Získá stav přístupu k síti prostředků, na kterých závisí služba v umístění. |
> | Akce | Microsoft. ApiManagement/Service/protokolovacích/DELETE | Odstraní zadaný protokolovací nástroj. |
> | Akce | Microsoft. ApiManagement/Service/protokolovací nástroje/číst | Vypíše kolekci protokolovacích nástrojů v zadané instanci služby. nebo získá podrobnosti protokolovacího nástroje určeného jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/protokolovacích/Write | Vytvoří nebo aktualizuje protokolovací nástroj. nebo aktualizuje existující protokolovací nástroj. |
> | Akce | Microsoft. ApiManagement/Service/managedeployments/Action | Změna SKU/jednotek, přidání nebo odebrání regionálních nasazení API Management služby |
> | Akce | Microsoft. ApiManagement/Service/namedValues/DELETE | Odstraní konkrétní pojmenovanou hodnotu z instance služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/namedValues/listSecrets/Action | Získá tajné kódy pojmenované hodnoty určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/namedValues/Read | Vypíše kolekci pojmenovaných hodnot definovaných v rámci instance služby. nebo získá podrobnosti o pojmenované hodnotě určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/namedValues/Write | Vytvoří nebo aktualizuje pojmenovanou hodnotu. nebo aktualizuje konkrétní pojmenovanou hodnotu. |
> | Akce | Microsoft. ApiManagement/Service/NetworkStatus/Read | Získá stav přístupu k síti prostředků, na kterých je služba závislá. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/Action | Odešle oznámení zadanému uživateli. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/Read | Zobrazí seznam vlastností definovaných v rámci instance služby. nebo získá podrobnosti oznámení určeného identifikátorem. |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Odebere e-mail ze seznamu oznámení. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Read | Získá seznam e-mailů příjemce oznámení přihlášených k odběru oznámení. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Write | Přidá e-mailovou adresu do seznamu příjemců oznámení. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/recipientUsers/DELETE | Odebere uživatele API Management ze seznamu oznámení. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Read | Získá seznam uživatele příjemce oznámení, který se přihlásí k odběru oznámení. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Write | Přidá uživatele API Management do seznamu příjemců oznámení. |
> | Akce | Microsoft. ApiManagement/Service/Notifications/Write | Vytvoří nebo aktualizuje oznámení vydavatele API Management. |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/delete | Odstraní konkrétního poskytovatele OpenID Connect instance služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/openidConnectProviders/listSecrets/Action | Získá konkrétní tajné kódy poskytovatele OpenID Connect. |
> | Akce | Microsoft. ApiManagement/Service/openidConnectProviders/Read | Seznam všech poskytovatelů OpenId Connect. nebo získá konkrétního poskytovatele OpenID Connect bez tajných klíčů. |
> | Akce | Microsoft. ApiManagement/Service/openidConnectProviders/Write | Vytvoří nebo aktualizuje poskytovatele OpenID Connect. nebo aktualizuje konkrétního poskytovatele OpenID Connect. |
> | Akce | Microsoft. ApiManagement/Service/operationresults/Read | Získá aktuální stav dlouho běžící operace. |
> | Akce | Microsoft. ApiManagement/Service/policies/DELETE | Odstraní konfiguraci globálních zásad služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/policies/Read | Zobrazí seznam všech definic globálních zásad služby API Management. nebo si přečtěte definici globální zásady služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/policies/Write | Vytvoří nebo aktualizuje konfiguraci globálních zásad služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/Policy/DELETE | Odstranění konfigurace zásad na úrovni tenanta |
> | Akce | Microsoft. ApiManagement/Service/Policy/Read | Získat konfiguraci zásad na úrovni tenanta |
> | Akce | Microsoft. ApiManagement/Service/Policy/Write | Vytvoření konfigurace zásad na úrovni tenanta |
> | Akce | Microsoft. ApiManagement/Service/policyDescriptions/Read | Zobrazí seznam všech popisů zásad. |
> | Akce | Microsoft. ApiManagement/Service/policySnippets/Read | Vypíše všechny fragmenty zásad. |
> | Akce | Microsoft. ApiManagement/Service/portalsettings/Read | Získejte nastavení pro přihlášení k portálu nebo získejte nastavení pro registraci portálu nebo získejte nastavení pro delegování portálu. |
> | Akce | Microsoft. ApiManagement/Service/portalsettings/Write | Aktualizujte nastavení přihlášení. nebo vytvořte nebo aktualizujte nastavení přihlášení. nebo aktualizovat nastavení registrace nebo aktualizovat nastavení registrace nebo aktualizovat nastavení delegování. nebo vytvořte nebo aktualizujte nastavení delegování. |
> | Akce | Microsoft. ApiManagement/Service/Products/API/DELETE | Odstraní zadané rozhraní API ze zadaného produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/API/Read | Vypíše kolekci rozhraní API přidružených k produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/API/Write | Přidá rozhraní API k zadanému produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/DELETE | Odstranit produkt |
> | Akce | Microsoft. ApiManagement/Service/Products/Groups/DELETE | Odstraní přidružení mezi určenou skupinou a produktem. |
> | Akce | Microsoft. ApiManagement/Service/Products/Groups/Read | Zobrazuje kolekci skupin pro vývojáře přidružených k zadanému produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/Groups/Write | Přidá přidružení mezi zadanou skupinou vývojářů se zadaným produktem. |
> | Akce | Microsoft. ApiManagement/Service/Products/policies/DELETE | Odstraní konfiguraci zásad v produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/policies/Read | Získejte konfiguraci zásad na úrovni produktu. nebo získejte konfiguraci zásad na úrovni produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/policies/Write | Vytvoří nebo aktualizuje konfiguraci zásad pro daný produkt. |
> | Akce | Microsoft. ApiManagement/Service/Products/Policy/DELETE | Odstranění konfigurace zásad na úrovni produktu |
> | Akce | Microsoft. ApiManagement/Service/Products/Policy/Read | Získat konfiguraci zásad na úrovni produktu |
> | Akce | Microsoft. ApiManagement/Service/Products/Policy/Write | Vytvoření konfigurace zásad na úrovni produktu |
> | Akce | Microsoft. ApiManagement/Service/Products/Read | Vypíše kolekci produktů v zadané instanci služby. nebo získá podrobnosti o produktu určeném jeho identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/Products/Subscriptions/Read | Zobrazí seznam předplatných určených pro daný produkt. |
> | Akce | Microsoft. ApiManagement/Service/Products/Tags/DELETE | Odpojte značku od produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/Tags/Read | Zobrazí seznam všech značek přidružených k produktu. nebo získat značku přidruženou k produktu. |
> | Akce | Microsoft. ApiManagement/Service/Products/Tags/Write | Přiřaďte k produktu značku. |
> | Akce | Microsoft. ApiManagement/Service/Products/Write | Vytvoří nebo aktualizuje produkt. nebo aktualizuje existující podrobnosti o produktu. |
> | Akce | Microsoft. ApiManagement/Service/productsByTags/Read | Vypíše kolekci produktů přidružených k značkám. |
> | Akce | Microsoft. ApiManagement/Service/Properties/DELETE | Odstraní konkrétní vlastnost z instance služby API Management. |
> | Akce | Microsoft. ApiManagement/Service/Properties/listSecrets/Action | Získá tajné kódy vlastnosti určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/Properties/Read | Zobrazí seznam vlastností definovaných v rámci instance služby. nebo získá podrobnosti vlastnosti určené identifikátorem. |
> | Akce | Microsoft. ApiManagement/Service/Properties/Write | Vytvoří nebo aktualizuje vlastnost. nebo aktualizuje konkrétní vlastnost. |
> | Akce | Microsoft. ApiManagement/Service/kvóty/období/čtení | Hodnota čítače získání kvóty pro období |
> | Akce | Microsoft. ApiManagement/Service/kvóty/období/zápis | Nastavit čítač kvót aktuální hodnota |
> | Akce | Microsoft. ApiManagement/Service/kvóty/číst | Získat hodnoty pro kvótu |
> | Akce | Microsoft. ApiManagement/Service/kvóty/Write | Nastavit čítač kvót aktuální hodnota |
> | Akce | Microsoft. ApiManagement/Service/Read | Čtení metadat pro instanci služby API Management Service |
> | Akce | Microsoft. ApiManagement/Service/Regions/Read | Zobrazí seznam všech oblastí Azure, ve kterých služba existuje. |
> | Akce | Microsoft. ApiManagement/Service/Reports/Read | Získání sestavy agregované podle časových období nebo získání sestavy agregované podle geografické oblasti nebo získání sestavy agregované vývojáři<br>nebo získat sestavu agregovanou podle produktů.<br>nebo získat sestavu agregovanou pomocí rozhraní API nebo získat sestavu agregovanou podle operací nebo získat sestavu agregovanou podle předplatného.<br>nebo získávat požadavky na data vytváření sestav |
> | Akce | Microsoft. ApiManagement/Service/Restore/Action | Obnovení služby API Management ze zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | Akce | Microsoft. ApiManagement/Service/Subscriptions/DELETE | Odstraní zadané předplatné. |
> | Akce | Microsoft. ApiManagement/Service/Subscriptions/listSecrets/Action | Získá zadané klíče předplatného. |
> | Akce | Microsoft. ApiManagement/Service/Subscriptions/Read | Zobrazí seznam všech předplatných instance služby API Management. or Gets the specified Subscription entity (without keys). |
> | Akce | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenerates primary key of existing subscription of the API Management service instance. |
> | Akce | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenerates secondary key of existing subscription of the API Management service instance. |
> | Akce | Microsoft.ApiManagement/service/subscriptions/write | Creates or updates the subscription of specified user to the specified product. or Updates the details of a subscription specified by its identifier. |
> | Akce | Microsoft.ApiManagement/service/tagResources/read | Lists a collection of resources associated with tags. |
> | Akce | Microsoft.ApiManagement/service/tags/delete | Deletes specific tag of the API Management service instance. |
> | Akce | Microsoft.ApiManagement/service/tags/read | Lists a collection of tags defined within a service instance. or Gets the details of the tag specified by its identifier. |
> | Akce | Microsoft.ApiManagement/service/tags/write | Creates a tag. or Updates the details of the tag specified by its identifier. |
> | Akce | Microsoft.ApiManagement/service/templates/delete | Reset default API Management email template |
> | Akce | Microsoft.ApiManagement/service/templates/read | Gets all email templates or Gets API Management email template details |
> | Akce | Microsoft.ApiManagement/service/templates/write | Create or update API Management email template or Updates API Management email template |
> | Akce | Microsoft.ApiManagement/service/tenant/delete | Remove policy configuration for the tenant |
> | Akce | Microsoft.ApiManagement/service/tenant/deploy/action | Runs a deployment task to apply changes from the specified git branch to the configuration in database. |
> | Akce | Microsoft.ApiManagement/service/tenant/listSecrets/action | Get tenant access information details |
> | Akce | Microsoft.ApiManagement/service/tenant/operationResults/read | Get list of operation results or Get result of a specific operation |
> | Akce | Microsoft.ApiManagement/service/tenant/read | Get the Global policy definition of the Api Management service. or Get tenant access information details |
> | Akce | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerate primary access key |
> | Akce | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerate secondary access key |
> | Akce | Microsoft.ApiManagement/service/tenant/save/action | Creates commit with configuration snapshot to the specified branch in the repository |
> | Akce | Microsoft.ApiManagement/service/tenant/syncState/read | Get status of last git synchronization |
> | Akce | Microsoft.ApiManagement/service/tenant/validate/action | Validates changes from the specified git branch |
> | Akce | Microsoft.ApiManagement/service/tenant/write | Set policy configuration for the tenant or Update tenant access information details |
> | Akce | Microsoft.ApiManagement/service/updatecertificate/action | Upload SSL certificate for an API Management Service |
> | Akce | Microsoft.ApiManagement/service/updatehostname/action | Setup, update or remove custom domain names for an API Management Service |
> | Akce | Microsoft.ApiManagement/service/users/action | Registrace nového uživatele |
> | Akce | Microsoft.ApiManagement/service/users/confirmations/send/action | Sends confirmation |
> | Akce | Microsoft.ApiManagement/service/users/delete | Deletes specific user. |
> | Akce | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Retrieves a redirection URL containing an authentication token for signing a given user into the developer portal. |
> | Akce | Microsoft.ApiManagement/service/users/groups/read | Lists all user groups. |
> | Akce | Microsoft.ApiManagement/service/users/identities/read | List of all user identities. |
> | Akce | Microsoft.ApiManagement/service/users/keys/read | Get keys associated with user |
> | Akce | Microsoft.ApiManagement/service/users/read | Lists a collection of registered users in the specified service instance. or Gets the details of the user specified by its identifier. |
> | Akce | Microsoft.ApiManagement/service/users/subscriptions/read | Lists the collection of subscriptions of the specified user. |
> | Akce | Microsoft.ApiManagement/service/users/token/action | Gets the Shared Access Authorization Token for the User. |
> | Akce | Microsoft.ApiManagement/service/users/write | Creates or Updates a user. or Updates the details of the user specified by its identifier. |
> | Akce | Microsoft.ApiManagement/service/write | Create a new instance of API Management Service |
> | Akce | Microsoft.ApiManagement/unregister/action | Un-register subscription for Microsoft.ApiManagement resource provider |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Authorization/classicAdministrators/delete | Removes the administrator from the subscription. |
> | Akce | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Gets the administrator operation statuses of the subscription. |
> | Akce | Microsoft.Authorization/classicAdministrators/read | Reads the administrators for the subscription. |
> | Akce | Microsoft.Authorization/classicAdministrators/write | Add or modify administrator to a subscription. |
> | Akce | Microsoft.Authorization/denyAssignments/delete | Delete a deny assignment at the specified scope. |
> | Akce | Microsoft.Authorization/denyAssignments/read | Get information about a deny assignment. |
> | Akce | Microsoft.Authorization/denyAssignments/write | Create a deny assignment at the specified scope. |
> | Akce | Microsoft.Authorization/elevateAccess/action | Udělit volajícímu přístup Správce uživatelských přístupů v oboru tenanta |
> | Akce | Microsoft.Authorization/locks/delete | Delete locks at the specified scope. |
> | Akce | Microsoft.Authorization/locks/read | Gets locks at the specified scope. |
> | Akce | Microsoft.Authorization/locks/write | Add locks at the specified scope. |
> | Akce | Microsoft.Authorization/operations/read | Gets the list of operations |
> | Akce | Microsoft. Authorization/oprávnění/čtení | Zobrazí seznam všech oprávnění, která volající má v daném oboru. |
> | Akce | Microsoft. authorization/policies/audit/Action | Akce provedená v důsledku vyhodnocení Azure Policy s účinkem "Audit" |
> | Akce | Microsoft. authorization/policies/auditIfNotExists/Action | Akce provedená v důsledku vyhodnocení Azure Policy pomocí efektu "auditIfNotExists" |
> | Akce | Microsoft. authorization/policies/Deny/Action | Akce provedená v důsledku vyhodnocení Azure Policy s efektem odepření |
> | Akce | Microsoft. authorization/policies/deployIfNotExists/Action | Akce provedená v důsledku vyhodnocení Azure Policy pomocí efektu "deployIfNotExists" |
> | Akce | Microsoft. Authorization/policyAssignments/DELETE | Odstraní přiřazení zásady v zadaném oboru. |
> | Akce | Microsoft. Authorization/policyAssignments/Read | Získejte informace o přiřazení zásady. |
> | Akce | Microsoft. Authorization/policyAssignments/Write | Vytvoří přiřazení zásady v zadaném oboru. |
> | Akce | Microsoft. Authorization/policyDefinitions/DELETE | Odstraní definici zásady. |
> | Akce | Microsoft. Authorization/policyDefinitions/Read | Získat informace o definici zásady. |
> | Akce | Microsoft. Authorization/policyDefinitions/Write | Vytvořte vlastní definici zásad. |
> | Akce | Microsoft. Authorization/policySetDefinitions/DELETE | Odstraní definici sady zásad. |
> | Akce | Microsoft. Authorization/policySetDefinitions/Read | Získejte informace o definici sady zásad. |
> | Akce | Microsoft. Authorization/policySetDefinitions/Write | Vytvoření vlastní definice sady zásad |
> | Akce | Microsoft. Authorization/providerOperations/Read | Získá operace pro všechny poskytovatele prostředků, které se dají použít v definicích rolí. |
> | Akce | Microsoft.Authorization/roleAssignments/delete | Odstraní přiřazení role v zadaném oboru. |
> | Akce | Microsoft. Authorization/roleAssignments/Read | Získejte informace o přiřazení role. |
> | Akce | Microsoft.Authorization/roleAssignments/write | Vytvoří přiřazení role v zadaném oboru. |
> | Akce | Microsoft. Authorization/roleDefinitions/DELETE | Odstraňte zadanou definici vlastní role. |
> | Akce | Microsoft. Authorization/roleDefinitions/Read | Získejte informace o definici role. |
> | Akce | Microsoft. Authorization/roleDefinitions/Write | Vytvořte nebo aktualizujte definici vlastní role se zadanými oprávněními a přiřazovatelné obory. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Přečtěte si informace o registraci Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapíše požadavek na opětovné vygenerování Azure Automation klíčů DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Certificates/DELETE | Odstraní prostředek certifikátu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Certificates/GetCount/Action | Přečte počet certifikátů. |
> | Akce | Microsoft. Automation/automationAccounts/Certificates/Read | Získá Azure Automation prostředek certifikátu. |
> | Akce | Microsoft. Automation/automationAccounts/Certificates/Write | Vytvoří nebo aktualizuje prostředek certifikátu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/compilationjobs/Read | Přečte kompilaci Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/compilationjobs/Read | Přečte kompilaci Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilaci Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilaci Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Configurations/Content/Read | Přečte konfigurační obsah média. |
> | Akce | Microsoft. Automation/automationAccounts/Configurations/DELETE | Odstraní obsah Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Configurations/GetCount/Action | Přečte počet Azure Automation obsahu DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Configurations/Read | Získá obsah Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Configurations/Write | Zapisuje obsah Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Connections/DELETE | Odstraní prostředek připojení Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Connections/GetCount/Action | Přečte počet připojení. |
> | Akce | Microsoft. Automation/automationAccounts/Connections/Read | Načte prostředek připojení Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Connections/Write | Vytvoří nebo aktualizuje prostředek připojení Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/connectionTypes/DELETE | Odstraní prostředek typu připojení Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/connectionTypes/Read | Získá prostředek typu připojení Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/connectionTypes/Write | Vytvoří prostředek typu připojení Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/přihlašovací údaje/odstranit | Odstraní prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/přihlašovací údaje/GetCount/Action | Načte počet přihlašovacích údajů. |
> | Akce | Microsoft. Automation/automationAccounts/přihlašovací údaje/číst | Získá Azure Automation Asset přihlašovacích údajů. |
> | Akce | Microsoft. Automation/automationAccounts/přihlašovací údaje/zapisovat | Vytvoří nebo aktualizuje prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/DELETE | Odstraní účet Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Odstraní prostředky Hybrid Runbook Worker. |
> | Akce | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Přečte Hybrid Runbook Worker prostředky. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Načte výstup úlohy. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Read | Načte Azure Automation úlohu. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Obnoví Azure Automation úlohu. |
> | Akce | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Získá obsah Azure Automation sady Runbook v době provádění úlohy. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/stop/Action | Zastaví úlohu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Pozastaví úlohu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Jobs/Write | Vytvoří úlohu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/jobSchedules/DELETE | Odstraní plán úlohy Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/jobSchedules/Read | Získá Azure Automation plán úlohy. |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úlohy Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Získá pracovní prostor propojený s účtem Automation. |
> | Akce | Microsoft. Automation/automationAccounts/klíče listkey/Action | Přečte klíče pro účet Automation. |
> | Akce | Microsoft. Automation/automationAccounts/modules/Activities/Read | Získá aktivity Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Module/DELETE | Odstraní modul PowerShellu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/modules/GetCount/Action | Získá počet modulů PowerShellu v rámci účtu Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Module/Read | Načte Azure Automation modul PowerShellu. |
> | Akce | Microsoft. Automation/automationAccounts/modules/Write | Vytvoří nebo aktualizuje modul prostředí Azure Automation PowerShell. |
> | Akce | Microsoft. Automation/automationAccounts/nodeConfigurations/DELETE | Odstraní konfiguraci uzlu Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Přečte obsah konfigurace uzlu Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/nodeConfigurations/Read | Přečte konfiguraci uzlu Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Zapisuje konfiguraci uzlu Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/nodecounts/Read | Načte souhrn počtu uzlů pro zadaný typ. |
> | Akce | Microsoft. Automation/automationAccounts/Nodes/DELETE | Odstraní Azure Automation uzly DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Nodes/Read | Přečte Azure Automation uzly DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Nodes/Reports/Reports/Read | Přečte obsah sestavy Azure Automation DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Nodes/Reports/Read | Přečte Azure Automation sestavy DSC. |
> | Akce | Microsoft. Automation/automationAccounts/Nodes/Write | Vytvoří nebo aktualizuje Azure Automation uzlů DSC. |
> | Akce | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Získá Azure Automation TypeFields |
> | Akce | Microsoft.Automation/automationAccounts/python2Packages/delete | Odstraní balíček Azure Automation Python 2. |
> | Akce | Microsoft. Automation/automationAccounts/python2Packages/Read | Získá Azure Automation balíček Python 2. |
> | Akce | Microsoft. Automation/automationAccounts/python2Packages/Write | Vytvoří nebo aktualizuje balíček Azure Automation Python 2. |
> | Akce | Microsoft.Automation/automationAccounts/python3Packages/delete | Odstraní balíček Azure Automation Python 3. |
> | Akce | Microsoft. Automation/automationAccounts/python3Packages/Read | Získá balíček Azure Automation Python 3. |
> | Akce | Microsoft. Automation/automationAccounts/python3Packages/Write | Vytvoří nebo aktualizuje balíček Azure Automation Python 3. |
> | Akce | Microsoft. Automation/automationAccounts/Read | Získá účet Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/Content/Read | Načte obsah sady Runbook Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/delete | Odstraní Runbook Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Vytvoří obsah konceptu Azure Automation Runbooku. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/koncept/operationResults/číst | Získá výsledky operace konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/koncept/čtení | Načte Azure Automation koncept Runbooku. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Načte testovací úlohu konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/koncept/testJob/Resume/Action | Obnoví testovací úlohu konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/koncept/testJob/stop/Action | Zastaví testovací úlohu konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/koncept/testJob/Suspend/Action | Pozastaví testovací úlohu konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Vytvoří testovací úlohu konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/koncept/undoEdit/Action | Umožňuje vrátit zpět úpravy konceptu sady Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/GetCount/Action | Získá počet Azure Automation sad Runbook. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/Publish/Action | Publikuje koncept Azure Automation Runbooku. |
> | Akce | Microsoft. Automation/automationAccounts/Runbooky/číst | Získá Azure Automation Runbook. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/write | Vytvoří nebo aktualizuje Runbook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Schedules/DELETE | Odstraní prostředek plánování Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Schedules/GetCount/Action | Získá počet plánů Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Schedules/Read | Získá prostředek plánování Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Schedules/Write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/Read | Získá Azure Automation spuštění konfiguračního počítače aktualizace softwaru. |
> | Akce | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/Read | Získá Azure Automation spuštění konfigurace aktualizace softwaru. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Odstraní Azure Automation konfiguraci aktualizace softwaru. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Odstraní Azure Automation konfiguraci aktualizace softwaru. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Získá Azure Automation konfiguraci aktualizace softwaru. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Získá Azure Automation konfiguraci aktualizace softwaru. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Vytvoří nebo aktualizuje konfiguraci Azure Automation aktualizace softwaru. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Vytvoří nebo aktualizuje konfiguraci Azure Automation aktualizace softwaru. |
> | Akce | Microsoft. Automation/automationAccounts/STATISTICS/Read | Získá statistiku Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Získat počítač nasazení aktualizace Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Získá úlohu opravy služby Azure Automation Update Management. |
> | Akce | Microsoft. Automation/automationAccounts/použití/číst | Získá využití Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Variables/DELETE | Odstraní Azure Automation variabilní prostředek. |
> | Akce | Microsoft. Automation/automationAccounts/Variables/číst | Přečte Azure Automation variabilní prostředek. |
> | Akce | Microsoft. Automation/automationAccounts/Variables/Write | Vytvoří nebo aktualizuje prostředek proměnné Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/delete | Odstraní úlohu sledovacího procesu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/sledovací procesy/číst | Získá úlohu sledovacího procesu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/watchs/Start/Action | Spustit úlohu sledovacího procesu Azure Automation |
> | Akce | Microsoft. Automation/automationAccounts/sledovací procesy/zastavení/akce | Zastavení úlohy sledovacího procesu Azure Automation |
> | Akce | Microsoft. Automation/automationAccounts/sledovací procesy/proudy/číst | Získá datový proud úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Odstraní akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/watchs/watcherActions/Read | Získá akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Vytvořit akce úlohy sledovacího procesu Azure Automation |
> | Akce | Microsoft. Automation/automationAccounts/watchs/Write | Vytvoří úlohu sledovacího procesu Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/webhooks/Action | Generuje identifikátor URI pro Azure Automation Webhook. |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/delete | Odstraní Webhook Azure Automation.  |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/read | Přečte Azure Automation Webhook. |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/write | Vytvoří nebo aktualizuje Webhook Azure Automation. |
> | Akce | Microsoft. Automation/automationAccounts/Write | Vytvoří nebo aktualizuje účet Azure Automation. |
> | Akce | Microsoft. Automation/Operations/Read | Získá dostupné operace pro prostředky Azure Automation. |
> | Akce | Microsoft. Automation/Register/Action | Zaregistruje předplatné do Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Azureactivedirectory selhala/b2cDirectories/DELETE | Odstranit prostředek adresáře B2C |
> | Akce | Microsoft.AzureActiveDirectory/b2cDirectories/read | Zobrazit prostředek adresáře B2C |
> | Akce | Microsoft.AzureActiveDirectory/b2cDirectories/write | Vytvořit nebo aktualizovat prostředek adresáře B2C |
> | Akce | Microsoft. Azureactivedirectory selhala/b2ctenants/Read | Zobrazí seznam všech tenantů B2C, ve kterých je uživatel členem. |
> | Akce | Microsoft.AzureActiveDirectory/operations/read | Načte všechny operace rozhraní API, které jsou k dispozici pro poskytovatele prostředků Microsoft. Azureactivedirectory selhala. |
> | Akce | Microsoft. Azureactivedirectory selhala/Register/Action | Registrace předplatného pro poskytovatele prostředků Microsoft. Azureactivedirectory selhala |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AzureStack/Operations/read | Získá vlastnosti operace poskytovatele prostředků. |
> | Akce | Microsoft.AzureStack/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. AzureStack. |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Odstraní předplatné zákazníka Azure Stack. |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/read | Získá vlastnosti předplatného Azure Stack zákazníka. |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/write | Vytvoří nebo aktualizuje předplatné Azure Stack zákazníka. |
> | Akce | Microsoft.AzureStack/registrations/delete | Odstraní registraci Azure Stack. |
> | Akce | Microsoft.AzureStack/registrations/getActivationKey/action | Získá nejnovější aktivační klíč Azure Stack. |
> | Akce | Microsoft. AzureStack/registrs/Products/getprodukt/Action | Načte produkt Azure Stack Marketplace. |
> | Akce | Microsoft. AzureStack/registrs/Products/GetProducts/Action | Načte seznam produktů Azure Stack Marketplace. |
> | Akce | Microsoft.AzureStack/registrations/products/listDetails/action | Načte rozšířené podrobnosti pro produkt Azure Stack Marketplace. |
> | Akce | Microsoft.AzureStack/registrations/products/read | Získá vlastnosti produktu Azure Stack Marketplace. |
> | Akce | Microsoft. AzureStack/registrs/Products/uploadProductLog/Action | Záznam Azure Stack stav a časové razítko produktu Marketplace |
> | Akce | Microsoft.AzureStack/registrations/read | Získá vlastnosti registrace Azure Stack. |
> | Akce | Microsoft.AzureStack/registrations/write | Vytvoří nebo aktualizuje registraci Azure Stack. |
> | Akce | Microsoft. AzureStack/verificationKeys/getCurrentKey/Action | Získá aktuální verzi Azure Stack podepsání veřejného klíče. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Batch/batchAccounts/Applications/DELETE | Odstraní aplikaci. |
> | Akce | Microsoft. Batch/batchAccounts/Applications/Read | Zobrazí seznam aplikací nebo získá vlastnosti aplikace. |
> | Akce | Microsoft. Batch/batchAccounts/aplikace/verze/aktivace/akce | Aktivuje balíček aplikace. |
> | Akce | Microsoft. Batch/batchAccounts/aplikace/verze/odstranit | Odstraní balíček aplikace. |
> | Akce | Microsoft. Batch/batchAccounts/aplikace/verze/číst | Získá vlastnosti balíčku aplikace. |
> | Akce | Microsoft. Batch/batchAccounts/aplikace/verze/zápis | Vytvoří nový balíček aplikace nebo aktualizuje existující balíček aplikace. |
> | Akce | Microsoft. Batch/batchAccounts/Applications/Write | Vytvoří novou aplikaci nebo aktualizuje existující aplikaci. |
> | Akce | Microsoft. Batch/batchAccounts/certificateOperationResults/Read | Získá výsledky dlouhotrvající operace s certifikátem na účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Certificates/cancelDelete/Action | Zruší neúspěšné odstranění certifikátu na účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Certificates/DELETE | Odstraní certifikát z účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Certificates/Read | Zobrazí seznam certifikátů na účtu Batch nebo získá vlastnosti certifikátu. |
> | Akce | Microsoft. Batch/batchAccounts/Certificates/Write | Vytvoří nový certifikát na účtu Batch nebo aktualizuje existující certifikát. |
> | Akce | Microsoft. Batch/batchAccounts/DELETE | Odstraní účet Batch. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/Jobs/DELETE | Odstraní úlohu z účtu Batch. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/Jobs/Read | Vypisuje úlohy na účtu Batch nebo získá vlastnosti úlohy. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/Jobs/Write | Vytvoří novou úlohu na účtu Batch nebo aktualizuje existující úlohu. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/jobSchedules/DELETE | Odstraní plán úlohy z účtu Batch. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Read | Zobrazí plány úloh na účtu Batch nebo získá vlastnosti plánu úlohy. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Write | Vytvoří nový plán úlohy na účtu Batch nebo aktualizuje existující plán úlohy. |
> | Akce | Microsoft. Batch/batchAccounts/klíče listkey/Action | Seznam přístupových klíčů pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/operationResults/read | Získá výsledky dlouhotrvající operace účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/poolOperationResults/Read | Získá výsledky dlouhotrvající operace s fondem na účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Pools/DELETE | Odstraní fond z účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Pools/disableAutoscale/Action | Zakáže automatické škálování fondu účtů Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Pools/Read | Vypíše fondy na účtu Batch nebo získá vlastnosti fondu. |
> | Akce | Microsoft. Batch/batchAccounts/Pools/stopResize/Action | Zastaví probíhající operaci změny velikosti ve fondu účtů Batch. |
> | Akce | Microsoft. Batch/batchAccounts/Pools/Write | Vytvoří nový fond na účtu Batch nebo aktualizuje existující fond. |
> | Akce | Microsoft. Batch/batchAccounts/Read | Zobrazí účty Batch nebo získá vlastnosti účtu Batch. |
> | Akce | Microsoft. Batch/batchAccounts/regeneratekeys/Action | Znovu vygeneruje přístupové klíče pro účet Batch. |
> | Akce | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje přístupové klíče pro účet automatického úložiště nakonfigurovaný pro účet Batch. |
> | Akce | Microsoft.Batch/batchAccounts/write | Vytvoří nový účet Batch nebo aktualizuje existující účet Batch. |
> | Akce | Microsoft. Batch/Locations/accountOperationResults/Read | Získá výsledky dlouhotrvající operace účtu Batch. |
> | Akce | Microsoft. Batch/Locations/checkNameAvailability/Action | Kontroluje, že název účtu je platný a nepoužívá se. |
> | Akce | Microsoft. Batch/umístění/kvóty/číst | Načte kvóty dávky zadaného předplatného v zadané oblasti Azure. |
> | Akce | Microsoft. Batch/Operations/Read | Zobrazí seznam operací dostupných v poskytovateli prostředků Microsoft. batch. |
> | Akce | Microsoft. Batch/registrovat/Action | Zaregistruje předplatné pro poskytovatele prostředků služby Batch a povolí vytváření účtů Batch. |
> | Akce | Microsoft. Batch/zrušit registraci/akce | Zruší registraci předplatného poskytovatele prostředků služby Batch, aby se zabránilo vytváření účtů Batch. |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. fakturace/billingAccounts/Agreements/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingPermissions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/billingPermissions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/Customers/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/faktur/pricesheet/Download/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Move/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Transfer/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/Products/Move/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/Products/Transfer/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/pricesheet/Download/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/Write |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingProfiles/Write |  |
> | Akce | Microsoft. fakturace/billingAccounts/billingSubscriptions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/Customers/billingPermissions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/Customers/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/oddělení/čtení |  |
> | Akce | Microsoft. fakturace/billingAccounts/enrollmentAccounts/billingPermissions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/enrollmentAccounts/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/enrollmentDepartments/billingPermissions/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/Action |  |
> | Akce | Microsoft. fakturace/billingAccounts/Products/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/Read |  |
> | Akce | Microsoft. fakturace/billingAccounts/Write |  |
> | Akce | Microsoft. fakturace/oddělení/čtení |  |
> | Akce | Microsoft. fakturace/faktury/stáhnout/akce | Stáhnout fakturu pomocí odkazu pro stažení ze seznamu |
> | Akce | Microsoft. fakturace/faktury/číst |  |
> | Akce | Microsoft. fakturace/registrace/akce |  |
> | Akce | Microsoft. fakturace/validateAddress/Action |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.BingMaps/mapApis/Delete | Odstranit operaci |
> | Akce | Microsoft. BingMaps/mapApis/listSecrets/Action | Výpis tajných kódů |
> | Akce | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Přečíst autorizační token jednotného přihlašování pro prostředek |
> | Akce | Microsoft. BingMaps/mapApis/Read | Operace čtení |
> | Akce | Microsoft.BingMaps/mapApis/regenerateKey/action | Znovu vygeneruje klíč. |
> | Akce | Microsoft. BingMaps/mapApis/Write | Operace zápisu |
> | Akce | Microsoft. BingMaps/Operations/Read | Popis operace |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. blockchain/blockchainMembers/DELETE | Odstraní existujícího člena blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/listApiKeys/Action | Získá nebo zobrazí seznam existujících klíčů rozhraní API členů blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/Read | Získá nebo zobrazí seznam existujících členů blockchain. |
> | Akce dataaction | Microsoft. blockchain/blockchainMembers/transactionNodes/Connect/Action | Připojí se k uzlu transakce člena blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/transactionNodes/DELETE | Odstraní existující uzel transakcí členů blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/transactionNodes/listApiKeys/Action | Získá nebo zobrazí seznam existujících klíčů rozhraní API uzlu transakcí členů blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/transactionNodes/Read | Získá nebo zobrazí seznam existujících uzlů transakcí členů blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/transactionNodes/Write | Vytvoří nebo aktualizuje uzel transakcí členů blockchain. |
> | Akce | Microsoft. blockchain/blockchainMembers/Write | Vytvoří nebo aktualizuje člen blockchain. |
> | Akce | Microsoft. blockchain/cordaMembers/DELETE | Odstraní stávajícího člena blockchain šňůry. |
> | Akce | Microsoft. blockchain/cordaMembers/Read | Získá nebo zobrazí seznam existujících členů šňůry blockchain. |
> | Akce | Microsoft. blockchain/cordaMembers/Write | Vytvoří nebo aktualizuje člena blockchain šňůry. |
> | Akce | Microsoft. blockchain/Locations/blockchainMemberOperationResults/Read | Získá výsledky operace členů blockchain. |
> | Akce | Microsoft. blockchain/Locations/checkNameAvailability/Action | Kontroluje, zda je název prostředku platný a není používán. |
> | Akce | Microsoft. blockchain/Operations/Read | Vypíše všechny operace v poskytovateli prostředků Microsoft blockchain. |
> | Akce | Microsoft. blockchain/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. detail/blueprintAssignments/assignmentOperations/Read | Přečtěte si všechny artefakty podrobného plánu |
> | Akce | Microsoft. detail/blueprintAssignments/DELETE | Odstranit artefakty podrobného plánu |
> | Akce | Microsoft. detail/blueprintAssignments/Read | Přečtěte si všechny artefakty podrobného plánu |
> | Akce | Microsoft. detail/blueprintAssignments/whoisblueprint/Action | Získá ID instančního objektu služby Azure modrotisky. |
> | Akce | Microsoft. detail/blueprintAssignments/Write | Vytvořit nebo aktualizovat artefakty podrobného plánu |
> | Akce | Microsoft. Details/modrotisky/artefakty/DELETE | Odstranit artefakty podrobného plánu |
> | Akce | Microsoft. Details/modrotisky/artefakty/číst | Přečtěte si všechny artefakty podrobného plánu |
> | Akce | Microsoft. Details/modrotisky/artefakty/Write | Vytvořit nebo aktualizovat artefakty podrobného plánu |
> | Akce | Microsoft. Details/modrotisky/DELETE | Odstranit všechny podrobné plány |
> | Akce | Microsoft. Details/modrotisky/Read | Přečtěte si všechny podrobné plány |
> | Akce | Microsoft. Details/modrotisky/verze/artefakty/čtení | Přečtěte si všechny artefakty podrobného plánu |
> | Akce | Microsoft. Details/modrotisky/Versions/DELETE | Odstranit všechny podrobné plány |
> | Akce | Microsoft. Details/modrotisky/verze/číst | Přečtěte si všechny podrobné plány |
> | Akce | Microsoft. Details/modrotisky/Versions/Write | Vytvořit nebo aktualizovat jakékoli podrobné plány |
> | Akce | Microsoft. Details/modrotisky/Write | Vytvořit nebo aktualizovat jakékoli podrobné plány |
> | Akce | Microsoft. detail/Register/Action | Zaregistruje poskytovatele prostředků Azure modrotisky. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. BotService/botServices/Channels/DELETE | Odstranění služby bot |
> | Akce | Microsoft. BotService/botServices/Channels/Read | Přečíst službu bot |
> | Akce | Microsoft. BotService/botServices/Channels/Write | Zápis služby bot |
> | Akce | Microsoft. BotService/botServices/Connections/DELETE | Odstranění služby bot |
> | Akce | Microsoft. BotService/botServices/Connections/Read | Přečíst službu bot |
> | Akce | Microsoft. BotService/botServices/Connections/Write | Zápis služby bot |
> | Akce | Microsoft. BotService/botServices/DELETE | Odstranění služby bot |
> | Akce | Microsoft. BotService/botServices/Read | Přečíst službu bot |
> | Akce | Microsoft. BotService/botServices/Write | Zápis služby bot |
> | Akce | Microsoft. BotService/Locations/operationresults/Read | Přečtěte si stav asynchronní operace. |
> | Akce | Microsoft. BotService/Operations/Read | Přečtěte si operace pro všechny typy prostředků. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. cache/checknameavailability/Action | Zkontroluje, jestli je název k dispozici pro použití s novým Redis Cache. |
> | Akce | Microsoft. cache/Locations/operationresults/Read | Získá výsledek dlouhotrvající operace, pro kterou se dřív vrátila hlavička Location do klienta. |
> | Akce | Microsoft.Cache/operations/read | Obsahuje seznam operací, které podporuje poskytovatel Microsoft. cache. |
> | Akce | Microsoft.Cache/redis/delete | Odstranit celý Redis Cache |
> | Akce | Microsoft.Cache/redis/export/action | Exportovat Redis data do předem vydaných objektů BLOB úložiště v zadaném formátu |
> | Akce | Microsoft.Cache/redis/firewallRules/delete | Odstranit pravidla brány firewall protokolu IP Redis Cache |
> | Akce | Microsoft.Cache/redis/firewallRules/read | Získat pravidla brány firewall protokolu IP pro Redis Cache |
> | Akce | Microsoft.Cache/redis/firewallRules/write | Upravit pravidla brány firewall protokolu IP Redis Cache |
> | Akce | Microsoft.Cache/redis/forceReboot/action | Vynutí restartování instance mezipaměti, což může způsobit ztrátu dat. |
> | Akce | Microsoft.Cache/redis/import/action | Import dat zadaného formátu z více objektů blob do Redis |
> | Akce | Microsoft.Cache/redis/linkedservers/delete | Odstranit odkazovaný server z Redis Cache |
> | Akce | Microsoft.Cache/redis/linkedservers/read | Získat propojené servery přidružené k mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/linkedservers/write | Přidání propojeného serveru do Redis Cache |
> | Akce | Microsoft.Cache/redis/listKeys/action | Zobrazení hodnoty přístupových klíčů Redis Cache na portálu pro správu |
> | Akce | Microsoft.Cache/redis/listUpgradeNotifications/read | Seznamte se s nejnovějšími oznámeními o upgradu pro tenanta mezipaměti. |
> | Akce | Microsoft.Cache/redis/metricDefinitions/read | Získá dostupné metriky pro Redis Cache. |
> | Akce | Microsoft.Cache/redis/patchSchedules/delete | Odstraní plán opravy Redis Cache. |
> | Akce | Microsoft.Cache/redis/patchSchedules/read | Gets the patching schedule of a Redis Cache |
> | Akce | Microsoft.Cache/redis/patchSchedules/write | Modify the patching schedule of a Redis Cache |
> | Akce | Microsoft.Cache/redis/read | View the Redis Cache's settings and configuration in the management portal |
> | Akce | Microsoft.Cache/redis/regenerateKey/action | Change the value of Redis Cache access keys in the management portal |
> | Akce | Microsoft.Cache/redis/start/action | Start a cache instance. |
> | Akce | Microsoft.Cache/redis/stop/action | Stop a cache instance. |
> | Akce | Microsoft.Cache/redis/write | Modify the Redis Cache's settings and configuration in the management portal |
> | Akce | Microsoft.Cache/register/action | Registers the 'Microsoft.Cache' resource provider with a subscription |
> | Akce | Microsoft.Cache/unregister/action | Unregisters the 'Microsoft.Cache' resource provider with a subscription |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Capacity/appliedreservations/read | Read All Reservations |
> | Akce | Microsoft.Capacity/calculateexchange/action | Computes the exchange amount and price of new purchase and returns policy Errors. |
> | Akce | Microsoft.Capacity/calculateprice/action | Calculate any Reservation Price |
> | Akce | Microsoft.Capacity/catalogs/read | Read catalog of Reservation |
> | Akce | Microsoft.Capacity/checkoffers/action | Check any Subscription Offers |
> | Akce | Microsoft.Capacity/checkscopes/action | Check any Subscription |
> | Akce | Microsoft.Capacity/commercialreservationorders/read | Get Reservation Orders created in any Tenant |
> | Akce | Microsoft.Capacity/exchange/action | Exchange any Reservation |
> | Akce | Microsoft.Capacity/operations/read | Read any Operation |
> | Akce | Microsoft.Capacity/register/action | Registers the Capacity resource provider and enables the creation of Capacity resources. |
> | Akce | Microsoft.Capacity/reservationorders/action | Update any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/availablescopes/action | Find any Available Scope |
> | Akce | Microsoft.Capacity/reservationorders/calculaterefund/action | Computes the refund amount and price of new purchase and returns policy Errors. |
> | Akce | Microsoft.Capacity/reservationorders/delete | Delete any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/merge/action | Merge any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/read | Read All Reservations |
> | Akce | Microsoft.Capacity/reservationorders/reservations/action | Update any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/reservations/availablescopes/action | Find any Available Scope |
> | Akce | Microsoft.Capacity/reservationorders/reservations/delete | Delete any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/reservations/read | Read All Reservations |
> | Akce | Microsoft.Capacity/reservationorders/reservations/revisions/read | Read All Reservations |
> | Akce | Microsoft.Capacity/reservationorders/reservations/write | Create any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/return/action | Return any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/split/action | Split any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/swap/action | Swap any Reservation |
> | Akce | Microsoft.Capacity/reservationorders/write | Create any Reservation |
> | Akce | Microsoft.Capacity/tenants/register/action | Register any Tenant |
> | Akce | Microsoft.Capacity/unregister/action | Unregister any Tenant |
> | Akce | Microsoft.Capacity/validatereservationorder/action | Validate any Reservation |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | Akce | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/read |  |
> | Akce | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/write |  |
> | Akce | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/delete |  |
> | Akce | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/read |  |
> | Akce | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/write |  |
> | Akce | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Akce | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Akce | Microsoft.Cdn/edgenodes/delete |  |
> | Akce | Microsoft.Cdn/edgenodes/read |  |
> | Akce | Microsoft.Cdn/edgenodes/write |  |
> | Akce | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | Akce | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/read |  |
> | Akce | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/write |  |
> | Akce | Microsoft.Cdn/operationresults/delete |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Akce | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Akce | Microsoft.Cdn/operationresults/read |  |
> | Akce | Microsoft.Cdn/operationresults/write |  |
> | Akce | Microsoft.Cdn/operations/read |  |
> | Akce | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Akce | Microsoft.Cdn/profiles/delete |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/read |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Akce | Microsoft.Cdn/profiles/endpoints/write |  |
> | Akce | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Akce | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Akce | Microsoft.Cdn/profiles/read |  |
> | Akce | Microsoft.Cdn/profiles/write |  |
> | Akce | Microsoft.Cdn/register/action | Registers the subscription for the CDN resource provider and enables the creation of CDN profiles. |
> | Akce | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Delete an existing certificate |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Get the list of certificates |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Add a new certificate or update an existing one |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Delete | Delete an existing AppServiceCertificate |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Read | Get the list of CertificateOrders |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Reissue an existing certificateorder |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renew an existing certificateorder |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Resend certificate email |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Resend request emails to another email address |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Retrieve site seal for an issued App Service Certificate |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Retrieve the list of certificate actions |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Retrieve certificate email history |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verify domain ownership |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Write | Add a new certificateOrder or update an existing one |
> | Akce | Microsoft.CertificateRegistration/operations/Read | List all operations from app service certificate registration |
> | Akce | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Provision service principal for service app principal |
> | Akce | Microsoft.CertificateRegistration/register/action | Register the Microsoft Certificates resource provider for the subscription |
> | Akce | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validate certificate purchase object without submitting it |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicCompute/capabilities/read | Shows the capabilities |
> | Akce | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Checks the availability of a given domain name. |
> | Akce | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Gets the availability of a given domain name. |
> | Akce | Microsoft.ClassicCompute/domainNames/active/write | Sets the active domain name. |
> | Akce | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Show the availability set for the resource. |
> | Akce | Microsoft.ClassicCompute/domainNames/capabilities/read | Shows the domain name capabilities |
> | Akce | Microsoft.ClassicCompute/domainNames/delete | Remove the domain names for resources. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Shows the deployment slots. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Get role on deployment slot of domain name |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Get role instance for role on deployment slot of domain name |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Get the deployment slot state. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Add the deployment slot state. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Get upgrade domain for deployment slot on domain name |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Update upgrade domain for deployment slot on domain name |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Vytvoří nebo aktualizuje nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/delete | Remove the domain name extensions. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Reads the operation status for the domain names extensions. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/read | Returns the domain name extensions. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/write | Add the domain name extensions. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remove a new internal load balance. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Reads the operation status for the domain names internal load balancers. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Gets the internal load balancers. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Creates a new internal load balance. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Reads the operation status for the domain names load balanced endpoint sets. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Get the load balanced endpoint sets. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Add the load balanced endpoint set. |
> | Akce | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Get operation status of the domain name. |
> | Akce | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Reads the operation status for the domain names extensions. |
> | Akce | Microsoft.ClassicCompute/domainNames/read | Return the domain names for resources. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Delete the service certificates used. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Reads the operation status for the domain names service certificates. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Returns the service certificates used. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Add or modify the service certificates used. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Aborts migration of a deployment slot. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Commits migration of a deployment slot. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/delete | Deletes a given deployment slot. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Reads the operation status for the domain names slots. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepares migration of a deployment slot. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/read | Shows the deployment slots. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remove the extension reference for the deployment slot role. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/operationStatuses/Read | Přečte stav operace pro odkazy na rozšíření rolí slotů názvů domén. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/Read | Vrátí odkaz na rozšíření role slotu nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/Write | Přidejte nebo upravte odkaz na rozšíření role slotu nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/metricdefinitions/Read | Získejte definici metriky role pro název domény. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/metriky/číst | Získá metriku role pro název domény. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/operationstatuses/Read | Získejte stav operace pro roli slotu názvů domén. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Získat nastavení diagnostiky. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Získá definice metrik. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/číst | Získejte roli pro slot nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/downloadremotedesktopconnectionfile/Action | Stáhne soubor připojení ke vzdálené ploše pro instanci role v roli slotu názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Získá stav operace pro instanci role v roli slotu názvů domén. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/Read | Získejte instanci role. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/znovu sestavit/akce | Znovu sestaví instanci role. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/obnovení obrazu/akce | Obnoví instanci role z image. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/restart/Action | Restartuje instance rolí. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/SKU/číst | Získá SKU role pro slot nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/role/zápis | Přidejte roli pro slot nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/Start/Action | Spustí slot nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/State/Start/Write | Změní stav slotu nasazení na zastaveno. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/State/stop/Write | Změní stav slotu nasazení na spuštěno. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/stop/Action | Pozastaví slot nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/upgradeDomain/Write | Projděte si upgrade domény. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/validateMigration/Action | Ověří migraci slotu nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/sloty/Write | Vytvoří nebo aktualizuje nasazení. |
> | Akce | Microsoft. ClassicCompute/domainNames/swap/Action | Zamění pracovní slot na produkční slot. |
> | Akce | Microsoft.ClassicCompute/domainNames/write | Přidejte nebo upravte názvy domén pro prostředky. |
> | Akce | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Přesuňte všechny klasické prostředky do jiného předplatného. |
> | Akce | Microsoft.ClassicCompute/operatingSystemFamilies/read | Obsahuje seznam rodin hostovaných operačních systémů, které jsou k dispozici v Microsoft Azure, a také uvádí dostupné verze operačních systémů pro každou rodinu. |
> | Akce | Microsoft.ClassicCompute/operatingSystems/read | Uvádí verze hostovaného operačního systému, které jsou v Microsoft Azure aktuálně k dispozici. |
> | Akce | Microsoft. ClassicCompute/Operations/Read | Gets the list of operations. |
> | Akce | Microsoft.ClassicCompute/operationStatuses/read | Reads the operation status for the resource. |
> | Akce | Microsoft. ClassicCompute/kvóty/čtení | Get the quota for the subscription. |
> | Akce | Microsoft. ClassicCompute/Register/Action | Registrovat do klasického výpočetního prostředí |
> | Akce | Microsoft. ClassicCompute/ResourceType/SKU/Read | Získá seznam SKU pro podporované typy prostředků. |
> | Akce | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Ověří dostupnost předplatného pro klasickou operaci přesunutí. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě přidruženou k virtuálnímu počítači. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Reads the operation status for the virtual machines associated network security groups. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě přidruženou k virtuálnímu počítači. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě přidruženou k virtuálnímu počítači. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Získá možné asynchronní operace. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/attachDisk/Action | Připojí datový disk k virtuálnímu počítači. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/Capture/Action | Zachyťte virtuální počítač. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/DELETE | Odebere virtuální počítače. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/detachDisk/Action | Odpojí datový disk od virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Získat nastavení diagnostiky virtuálního počítače |
> | Akce | Microsoft. ClassicCompute/virtualMachines/disks/Read | Načte seznam datových disků. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/Action | Stáhne soubor RDP pro virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Přečte stav operace pro rozšíření virtuálních počítačů. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/Extensions/Read | Získá rozšíření virtuálního počítače. |
> | Akce | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Vloží rozšíření virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Get the virtual machine metric definition. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/metrics/read | Získá metriky. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Deletes the network security group associated with the network interface. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Reads the operation status for the virtual machines associated network security groups. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Gets the network security group associated with the network interface. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adds a network security group associated with the network interface. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Reads the operation status for the virtual machines. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Performs maintenance on the virtual machine. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Získá definice metrik. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/read | Retrieves list of virtual machines. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Redeploys the virtual machine. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/restart/action | Restarts virtual machines. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Shutdown the virtual machine. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/start/action | Spusťte virtuální počítač |
> | Akce | Microsoft.ClassicCompute/virtualMachines/stop/action | Stops the virtual machine. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/write | Add or modify virtual machines. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Get an express route cross connection operation status. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Delete express route cross connection peering. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Get an express route cross connection peering operation status. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Get express route cross connection peering. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Add express route cross connection peering. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Get express route cross connections. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Add express route cross connections. |
> | Akce | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Retrieves the list of supported devices. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Deletes the network security group. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Reads the operation status for the network security group. |
> | Akce | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Gets the Network Security Groups Diagnostic Settings |
> | Akce | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the Network Security Groups diagnostic settings, this operation is supplemented by insights resource provider. |
> | Akce | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Gets the events for network security group |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/read | Gets the network security group. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Deletes the security rule. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Reads the operation status for the network security group security rules. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Gets the security rule. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adds or update a security rule. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adds a new network security group. |
> | Akce | Microsoft.ClassicNetwork/operations/read | Get classic network operations. |
> | Akce | Microsoft.ClassicNetwork/quotas/read | Get the quota for the subscription. |
> | Akce | Microsoft.ClassicNetwork/register/action | Register to Classic Network |
> | Akce | Microsoft.ClassicNetwork/reservedIps/delete | Delete a reserved Ip. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/join/action | Join a reserved Ip |
> | Akce | Microsoft.ClassicNetwork/reservedIps/link/action | Link a reserved Ip |
> | Akce | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Reads the operation status for the reserved ips. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/read | Gets the reserved Ips |
> | Akce | Microsoft.ClassicNetwork/reservedIps/write | Add a new reserved Ip |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Aborts the migration of a Virtual Network |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Shows the capabilities |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Checks the availability of a given IP address in a virtual network. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Commits the migration of a Virtual Network |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/delete | Deletes the virtual network. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Unrevokes a client certificate. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Read the revoked client certificates. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revokes a client certificate. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Deletes the virtual network gateway client certificate. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Downloads certificate by thumbprint. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Lists the virtual network gateway certificate package. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Find the client root certificates. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Uploads a new client root certificate. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Connects a site to site gateway connection. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Disconnects a site to site gateway connection. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Retrieves the list of connections. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Tests a site to site gateway connection. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Deletes the virtual network gateway. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Downloads the device configuration script. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Downloads the gateway diagnostics. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Retrieves the circuit service key. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Lists the virtual network gateway package. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Reads the operation status for the virtual networks gateways. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Gets the virtual network gateway package. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Gets the virtual network gateways. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Starts diagnostic for the virtual network gateway. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Stops the diagnostic for the virtual network gateway. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adds a virtual network gateway. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/join/action | Joins the virtual network. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Reads the operation status for the virtual networks. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Peers a virtual network with another virtual network. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepares the migration of a Virtual Network |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/read | Get the virtual network. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Deletes the remote virtual network peering proxy. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Gets the remote virtual network peering proxy. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adds or updates the remote virtual network peering proxy. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Deletes the network security group associated with the subnet. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Reads the operation status for the virtual network subnet associated network security group. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Gets the network security group associated with the subnet. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adds a network security group associated with the subnet. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Validates the migration of a Virtual Network |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Načte partnerský vztah virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/write | Add a new virtual network. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicStorage/capabilities/read | Shows the capabilities |
> | Akce | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Checks for the availability of a storage account. |
> | Akce | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Get the availability of a storage account. |
> | Akce | Microsoft.ClassicStorage/disks/read | Returns the storage account disk. |
> | Akce | Microsoft.ClassicStorage/images/operationstatuses/read | Gets Image Operation Status. |
> | Akce | Microsoft.ClassicStorage/images/read | Returns the image. |
> | Akce | Microsoft.ClassicStorage/operations/read | Gets classic storage operations |
> | Akce | Microsoft.ClassicStorage/osImages/read | Returns the operating system image. |
> | Akce | Microsoft.ClassicStorage/osPlatformImages/read | Gets the operating system platform image. |
> | Akce | Microsoft.ClassicStorage/publicImages/read | Gets the public virtual machine image. |
> | Akce | Microsoft.ClassicStorage/quotas/read | Get the quota for the subscription. |
> | Akce | Microsoft.ClassicStorage/register/action | Register to Classic Storage |
> | Akce | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Aborts migration of a storage account. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Získá definice metrik. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Commits migration of a storage account. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/delete | Delete the storage account. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/delete | Deletes a given storage account  disk. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Reads the operation status for the resource. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/read | Returns the storage account disk. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/write | Adds a storage account disk. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Získá definice metrik. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/delete | Deletes a given storage account image. (Deprecated. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Returns the storage account image operation status. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/read | Returns the storage account image. (Deprecated. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Akce | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lists the access keys for the storage accounts. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Reads the operation status for the resource. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Deletes a given storage account operating system image. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returns the storage account operating system image. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adds a given storage account operating system image. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepares migration of a storage account. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Získá definice metrik. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Získá definice metrik. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/read | Vrátí účet úložiště s daným účtem. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Obnoví existující přístupové klíče pro účet úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/Read | Získá definice metrik. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/Services/metriky/číst | Získá metriky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/read | Získejte dostupné služby. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Získat nastavení diagnostiky. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/metricDefinitions/Read | Získá definice metrik. |
> | Akce | Microsoft. ClassicStorage/storageAccounts/validateMigration/Action | Ověří migraci účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Odstraní zadanou image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Načte daný stav operace image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Vrátí image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Přidá zadanou image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/write | Přidá nový účet úložiště. |
> | Akce | Microsoft.ClassicStorage/vmImages/read | Zobrazí seznam imagí virtuálních počítačů. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/AnomalyDetector/časové řady/celého/Detect/Action | Tato operace generuje model pomocí celé řady, přičemž každý bod je zjištěn stejným modelem.<br>V této metodě body před a po určitém bodu se používají k určení, zda se jedná o anomálii.<br>Celá detekce může uživateli poskytnout celkový stav časových řad. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/AnomalyDetector/časové řady/Last/Detect/Action | Tato operace vygeneruje model pomocí bodů před poslední. S touto metodou se k určení, zda je cílový bod anomálií, používají pouze historické body. Nejnovější body, které se zjišťují, se shodují se scénářem monitorování obchodních metrik v reálném čase. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Automatický návrh/hledání/akce | Tato operace poskytuje návrhy na daný dotaz nebo částečný dotaz. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/analyzovat/Action | Tato operace extrahuje bohatou sadu vizuálních funkcí založenou na obsahu obrázku.  |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/areaofinterest/Action | Tato operace vrátí ohraničující rámeček kolem nejdůležitější oblasti obrázku. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/popis/akce | Tato operace vygeneruje popis obrázku v jazyce pro lidské čtení s úplnými větami.<br> Popis je založen na kolekci značek obsahu, které jsou také vráceny v rámci operace.<br>Pro každý obrázek lze vygenerovat více než jeden popis.<br> Popisy jsou seřazené podle jejich skóre spolehlivosti.<br>Všechny popisy jsou v angličtině. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Detect/Action | Tato operace provádí detekci objektu na zadané imagi.  |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/generatethumbnail/Action | Tato operace vygeneruje miniaturní obrázek s uživatelem zadanou šířkou a výškou.<br> Ve výchozím nastavení služba analyzuje obrázek, identifikuje oblast zájmu (ROI) a na základě této oblasti zájmu vygeneruje souřadnice inteligentního oříznutí.<br> Inteligentní oříznutí pomáhá při určení poměru stran, který se liší od hodnoty vstupního obrázku. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Tato operace rozpoznává obsah v rámci bitové kopie použitím modelu specifického pro doménu.<br> Seznam modelů specifických pro doménu, které rozhraní API pro počítačové zpracování obrazu podporuje, se dají načíst pomocí žádosti o získání/Models.<br> V současné době rozhraní API poskytuje následující modely specifické pro doménu: celebrit, orientačních bodů. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Models/Read | Tato operace vrátí seznam modelů specifických pro doménu, které jsou podporovány rozhraní API pro počítačové zpracování obrazu.  Rozhraní API v současné době podporuje následující modely specifické pro doménu: Nástroj pro rozpoznávání celebrit, nástroj pro rozpoznávání bodů. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/OCR/Action | Optické rozpoznávání znaků (OCR) detekuje text v obrázku a extrahuje rozpoznané znaky do datového proudu znaků, který je použitelný pro počítač.    |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Read/analyzovat/Action | Pomocí tohoto rozhraní můžete provést operaci čtení a využívat špičkové algoritmy optického rozpoznávání znaků (OCR) optimalizované pro textově náročné dokumenty.<br>Může zpracovávat ručně psané, tištěné nebo smíšené dokumenty.<br>Když použijete rozhraní pro čtení, odpověď obsahuje hlavičku s názvem Operations-Location.<br>Hlavička ' Operation-Location ' obsahuje adresu URL, kterou je nutné použít pro operaci získat výsledky čtení pro přístup k výsledkům optického rozpoznávání znaků. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Read/analyzeresults/Read | Pomocí tohoto rozhraní můžete načíst stav a výsledek optického rozpoznávání znaků operace čtení.  Adresa URL obsahující ' operationId ' je vrácena v hlavičce odpovědi operace čtení ' Operation-Location '. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Read/Core/asyncbatchanalyze/Action | Pomocí tohoto rozhraní získáte výsledek operace čtení souboru dávky, která využívá nejmodernější optický znak. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Read/Operations/Read | Toto rozhraní se používá k získání výsledků rozpoznávání OCR pro operaci čtení. Adresa URL tohoto rozhraní by měla být načtena z pole <b>"provozní umístění"</b> vráceného z rozhraní dávkového čtení souboru. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/recognizetext/Action | Pomocí tohoto rozhraní získáte výsledek operace Rozpoznávání textu. Když použijete rozhraní Rozpoznávání textu, odpověď obsahuje pole s názvem "Operation-Location". Pole "umístění operace" obsahuje adresu URL, kterou je nutné použít pro operaci získání Rozpoznávání textuho výsledku operace get. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/tag/Action | Tato operace vygeneruje seznam slov nebo značek, které jsou relevantní pro obsah dodané image.<br>Rozhraní API pro počítačové zpracování obrazu může vracet značky založené na objektech, živém, krajin nebo akcích, které se našly na obrázcích.<br>Na rozdíl od kategorií nejsou značky uspořádány podle hierarchického klasifikačního systému, ale odpovídají obsahu obrázku.<br>Značky mohou obsahovat tipy pro předcházení nejednoznačnosti nebo zadání kontextu, například značka "cello" může být doprovázena nápovědou "hudební instrument".<br>Všechny značky jsou v angličtině. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/textoperations/Read | Toto rozhraní se používá k získání výsledku pro rozpoznání textové operace. Adresa URL tohoto rozhraní by měla být načtena z Rozpoznávání textuho rozhraní z pole <b>"Operation Location"</b> . |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/imagelists/Action | Vytvořit seznam obrázků. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/ImageList/DELETE | Seznamy obrázků – odstranění |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/imagelists/images/DELETE | Odstraní obrázek ze seznamu obrázků. Seznam obrázků se dá použít k tomu, aby se při použití rozhraní API pro image a porovnávání nepřibližně shodoval s ostatními obrázky. Odstraní všechny obrázky ze seznamu. Seznam obrázků se dá použít k tomu, aby se při použití rozhraní API pro image a porovnávání nepřibližné shody s ostatními imagemi. * |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/imagelists/images/Read | Obrázek – načíst všechna ID imagí |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/imagelists/images/Write | Přidejte obrázek do seznamu obrázků. Seznam obrázků se dá použít k tomu, aby se při použití rozhraní API pro image a porovnávání nepřibližně shodoval s ostatními obrázky. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/ImageList/Read | Seznamy obrázků – získat podrobnosti-seznamy obrázků – získat vše |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/imagelists/refreshindex/Action | Seznamy obrázků – aktualizovat index hledání |
> | Akce dataaction | Microsoft. Cognitiveservices Account/účty/ContentModerator/ImageList/Write | Seznamy obrázků – podrobnosti o aktualizaci |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/processimage/Evaluate/Action | Vrátí pravděpodobnost obrázku obsahujícího obsah pikantní nebo dospělého. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/processimage/findfaces/Action | Najděte plošky na obrázcích. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/processimage/Match/Action | Fuzzily odpovídá obrázku v jednom z vašich vlastních seznamů obrázků.<br>Pomocí tohoto rozhraní API můžete vytvářet a spravovat vlastní seznamy imagí.<br> |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/processimage/OCR/Action | Vrátí libovolný text, který se nachází v obrázku pro zadaný jazyk. Pokud není zadán žádný jazyk ve vstupu, je detekce výchozím nastavením angličtina. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/processtext/operaci DetectLanguage/Action | Tato operace detekuje jazyk zadaného vstupního obsahu.<br>Vrátí kód ISO 639-3 pro převládající jazyk skládající se z odeslaného textu.<br>Podporuje se více než 110 jazyků. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/processtext/Screen/Action | Tato operace zjišťuje vulgární výrazy ve více než 100 jazycích a porovnává je vůči vlastním a sdíleným seznamům zakázaných slov. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/Teams/Jobs/Action | Pro obsah obrázku publikovaný v tomto koncovém bodu bude vráceno ID úlohy.  |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/Teams/Jobs/Read | Získat podrobnosti úlohy pro ID úlohy. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/Teams/Reviews/AccessKey/Read | Získejte přístupový klíč k obsahu pro váš tým. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/Teams/Reviews/Action | Vytvořené recenze by se zobrazily pro kontrolory v týmu. Když kontroloři dokončí kontrolu, výsledky kontroly by se PUBLIKOVALy (tj. HTTP POST) na určené CallBackEndpoint. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ContentModerator/Teams/Reviews/frames/Read | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/write | Use this method to add frames for a video review. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/publish/action | Video reviews are initially created in an unpublished state - which means it is not available for reviewers on your team to review yet. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/read | Returns review details for the review Id passed. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcript/action | This API adds a transcript file (text version of all the words spoken in a video) to a video review. The file should be a valid WebVTT format. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcriptmoderationresult/action | This API adds a transcript screen text result file for a video review. Transcript screen text result file is a result of Screen Text API . In order to generate transcript screen text result file , a transcript file has to be screened for profanity using Screen Text API. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/delete | Delete a template in your team |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/read | Returns an array of review templates provisioned on this team. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/write | Creates or updates the specified template |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/read | Get the details of a specific Workflow on your Team. Get all the Workflows available for you Team* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/write | Create a new workflow or update an existing one. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/action | Create term list. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/action | Term Lists - Bulk Update |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/delete | Term Lists - Delete |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/read | Term Lists - Get All - Term Lists - Get Details |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/refreshindex/action | Term Lists - Refresh Search Index |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/delete | Term - Delete - Term - Delete All Terms |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/read | Term - Get All Terms |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/write | Term - Add Term |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/write | Term Lists - Update Details |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/action | Classify an image and saves the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/nostore/action | Classify an image without saving the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/action | Classify an image url and saves the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/nostore/action | Classify an image url without saving the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/action | Detect objects in an image and saves the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/nostore/action | Detect objects in an image without saving the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/action | Detect objects in an image url and saves the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/nostore/action | Detect objects in an image url without saving the result. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/domains/read | Get information about a specific domain. Get a list of the available domains.* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/action | Set pool size of Label Proposal. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/read | Get pool size of Label Proposal for this project. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/project/migrate/action | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/action | Vytvoření projektu. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/delete | Delete a specific project. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/action | This API accepts body content as multipart/form-data and application/octet-stream. Při použití částí multipart |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/projekty/odstranit | Odstraní image ze sady školicích imagí. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty, obrázky/soubory/akce | Toto rozhraní API akceptuje dávku souborů a volitelně vytvoří tagy pro vytváření imagí. Je omezena na 64 obrázků a 20 značek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/Projects/images/ID/Read | Toto rozhraní API vrátí sadu imagí pro zadané značky a volitelně iteraci. Pokud není zadána žádná iterace |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/Projects/reimages/předpovědi/Action | Toto rozhraní API vytvoří dávku imagí z určených předpokládaných imagí. Je omezena na 64 obrázků a 20 značek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/Projects/reimages/regionproposals/Action | Toto rozhraní API získá návrhy oblastí pro obrázek spolu s jistotami pro oblast. Vrátí prázdné pole, pokud nebyly nalezeny žádné návrhy. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty, obrázky/oblasti/akce | Toto rozhraní API akceptuje dávku oblastí obrázků a volitelně vytvoří tagy pro aktualizaci existujících imagí s informacemi o oblastech. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty, obrázky/oblasti/odstranění | Odstraní sadu oblastí obrázků. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty/projekty/návrhy/akce | Toto rozhraní API načte netagované obrázky filtrované navrženými ID značek. Vrátí prázdné pole, pokud nebyly nalezeny žádné obrázky. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/Projects/images/navrhovaný/Count/Action | Toto rozhraní API používá tagIds k získání počtu netagovaných obrázků na navrhované značky pro danou prahovou hodnotu. |
> | Akce dataaction | Microsoft. cognitiveservices Account/Accounts/CustomVision. předpověď/Projects/images//Count/Read | Filtrování je na vztahu a/nebo. Například pokud jsou zadaná ID značek pro "pes" a |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/Projects/reimages/Tagged/Read | Toto rozhraní API podporuje dávkování a výběr rozsahu. Ve výchozím nastavení budou vracet pouze prvních 50 imagí vyhovující obrázkům. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty, images/Tags/Action | Přidružte sadu imagí se sadou značek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. Předpověde/projekty, images/Tags/DELETE | Odebere sadu značek ze sady imagí. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/projekty/neoznačeno/počet/čtení | Toto rozhraní API vrátí obrázky, které pro daný projekt neobsahují žádné značky, a volitelně iteraci. Pokud není zadána žádná iterace |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/projekty/neoznačeno/číst | Toto rozhraní API podporuje dávkování a výběr rozsahu. Ve výchozím nastavení budou vracet pouze prvních 50 imagí vyhovující obrázkům. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. Předpověde/projekty, images/URL/Action | Toto rozhraní API akceptuje dávku adres URL a volitelně vytvoří tagy pro vytváření imagí. Je omezena na 64 obrázků a 20 značek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace/DELETE | Odstraní konkrétní iteraci projektu. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty/iterace/export/akce | Exportujte vyškolenou iteraci. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace/export/čtení | Získá seznam exportů pro konkrétní iteraci. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace/počet/výkon/images/čtení | Filtrování je na vztahu a/nebo. Například pokud jsou zadaná ID značek pro "pes" a |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty/iterace/počet/výkon/image/čtení | Toto rozhraní API podporuje dávkování a výběr rozsahu. Ve výchozím nastavení budou vracet pouze prvních 50 imagí vyhovující obrázkům. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace///čtení | Získejte podrobné informace o výkonu iterace. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace/Publish/Action | Publikování konkrétní iterace. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace/Publish/DELETE | Zrušení publikování konkrétní iterace |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty/iterace/čtení | Získá konkrétní iteraci. Získat iterace pro projekt. * |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/iterace/Write | Aktualizuje konkrétní iteraci. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpovědi/projekty/předpovědi/DELETE | Odstraní sadu předpokládaných imagí a jejich přidružených výsledků předpovědi. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/CustomVision. předpověď/projekty/předpovědi/Query/Action | Načte obrázky, které byly odeslány do koncového bodu předpovědi. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/image/action | Quick test an image. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/url/action | Quick test an image url. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/read | Get a specific project. Get your projects.* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/action | Create a tag for the project. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/delete | Delete a tag from the project. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/read | Get information about a specific tag. Get the tags for a given project and iteration.* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/write | Update a tag. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tagsandregions/suggestions/action | This API will get suggested tags and regions for an array/batch of untagged images along with confidences for the tags. It returns an empty array if no tags are found. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/train/action | Queues project for training. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/write | Update a specific project. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/action | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/delete | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/refresh/write | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/prediction/user/read | Get usage for prediction resource for Oxford user |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/resource/tier/read | Get usage for training resource for Azure user |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/user/read | Get usage for training resource for Oxford user |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/action | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/delete | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/state/write | Update user state |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/tier/write | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/users/read | *NotDefined* |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/delete | Deletes a whitelisted user with specific capability |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/read | Gets a list of whitelisted users with specific capability |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/write | Updates or creates a user in the whitelist with specific capability |
> | Akce | Microsoft.CognitiveServices/accounts/delete | Deletes API accounts |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/EntitySearch/search/action | Get entities and places results for a given query. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detect human faces in an image, return face rectangles, and optionally with faceIds, landmarks, and attributes. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Delete a specified face list. The related face images in the face list will be deleted, too. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Delete a face from a face list by specified faceListId and persisitedFaceId. Odstraní se i související image obličeje. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Add a face to a specified face list, up to 1,000 faces. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Retrieve a face list's faceListId, name, userData and faces in the face list. List face lists' faceListId, name and userData. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Create an empty face list with user-specified faceListId, name and an optional userData. Up to 64 face lists are allowed Update information of a face list, including name and userData. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Given query face's faceId, to search the similar-looking faces from a faceId array, a face list or a large face list. faceId |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/group/action | Divide candidate faces into groups based on face similarity. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1-to-many identification to find the closest matches of the specific query person face from a person group or large person group. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Delete a specified large face list. The related face images in the large face list will be deleted, too. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Odstraňte plošku z velkého seznamu obličeje zadaným largeFaceListId a persisitedFaceId. Odstraní se i související image obličeje. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Načtěte trvalý obličej ve velkých listech podle largeFaceListId a persistedFaceId. Vypíše persistedFaceId a userData v zadaném velkém seznamu obličeje. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Přidejte obličej do určeného rozsáhlého seznamu, až 1 000 000 obličeje. Aktualizuje zadané pole userData na ploše v rozsáhlém seznamu na základě jeho persistedFaceId. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Načtěte largeFaceListId, název, název a userData pro velký seznam. Seznamte se s informacemi o largeFaceListId, jménech a userDatach ve velkých listech. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/largefacelists/vlak/Action | Odešlete rozsáhlý úkol školení pro seznam obličeje. Školení je rozhodující krok, který může používat jenom školicí seznam velkých obličejů. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Chcete-li ověřit, zda je stav vzdělávání velkého seznamu obličeje dokončený nebo stále dál. Školení LargeFaceList je asynchronní operace |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Vytvořte prázdný velký seznam obličeje pomocí uživatelsky zadaného largeFaceListId, názvu a volitelné uživatelské služby. Aktualizujte informace o velkém seznamu, včetně názvu a userData. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Odstraní existující velkou skupinu osob se zadaným personGroupId. Trvalá data v této velké skupině osob se odstraní. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/largepersongroups/persons/Action | Vytvoří novou osobu v zadané velké skupině osob. Pokud chcete této osobě přidat obličej, zavolejte prosím |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Odstraní stávající osobu z velké skupiny osob. Všechna uložená data osob a obrázky na ploše se odstraní. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Odstranění obličeje od uživatele ve velké skupině osob. Odstraní se také data obličeje a obrázek související s touto položkou obličeje. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Načte informace o obličeji osoby. Plocha trvalé osoby je určena largePersonGroupId, personId a persistedFaceId. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Přidejte obličejovou image k osobě do velké skupiny osob pro identifikaci nebo ověřování obličeje. Pro práci s obrázkem aktualizace pole userData osoby, které trvalo obličej. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Načtěte jméno osoby a userData a trvale faceIds představující image na ploše registrované osoby. Vypíše informace o všech osobách v zadané skupině Velká osoba, včetně personId, názvu, uživatelských a persistedFaceIds. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/largepersongroups/persons/Write | Název aktualizace nebo userData uživatele |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Načte informace o velké skupině osob, včetně názvu a uživatelských souborů. Toto rozhraní API vrací informace o velkých skupinách osob v seznamu všech stávajících velkých skupin osob largePesonGroupId, Name a userData. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/largepersongroups/vlak/Action | Odešlete úkol školení pro velkou skupinu osob. Školení je rozhodující krok, který může používat jenom školená velká skupina osob. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Chcete-li ověřit stav vzdělávání velkých skupin osob, který je dokončen nebo stále probíhá. Školení LargePersonGroup je asynchronní operace |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Vytvořte novou velkou skupinu osob s uživatelem zadanými largePersonGroupId, názvem a volitelnými userData. Aktualizujte stávající název a userData pro velkou skupinu osob. Vlastnosti zůstanou beze změny, pokud nejsou v textu žádosti. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/DELETE | Odstraní existující skupinu osob se zadaným personGroupId. Trvalá data v této skupině osob se odstraní. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/Action | Vytvoří novou osobu v zadané skupině Person. Pokud chcete této osobě přidat obličej, zavolejte prosím |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/DELETE | Odstraní stávajícího uživatele ze skupiny osob. Všechna uložená data osob a obrázky na ploše se odstraní. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/persistedfaces/DELETE | Odstraňte plošku z osoby ve skupině osob. Odstraní se také data obličeje a obrázek související s touto položkou obličeje. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/persistedfaces/Read | Načte informace o obličeji osoby. Plocha trvalé osoby je určena personGroupId, personId a persistedFaceId. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/persistedfaces/Write | Přidejte obličejovou obrázek k osobě do skupiny osob pro identifikaci nebo ověřování obličeje. Chcete-li se zabývat s obrázkem více aktualizací, které osoba trvala na osobním poli userData. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/Read | Načtěte jméno osoby a userData a trvale faceIds představující image na ploše registrované osoby. Vypíše informace o všech osobách v zadané skupině person, včetně personId, Name, userData a persistedFaceIds registrovaného. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/persons/Write | Název aktualizace nebo userData uživatele |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/Read | Načtení názvu skupiny osob a uživatelských souborů. Pokud chcete získat informace o osobě v rámci této skupiny osob, použijte pesonGroupId, Name a userData skupin osob seznam. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/vlak/Action | Odešlete úkol školení pro skupinu osob. Školení je rozhodující krok, který může používat jenom školená skupina osob. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/Training/Read | Chcete-li ověřit, zda stav školení skupiny osob bylo dokončeno nebo stále probíhá. Školení objektu person je aktivované asynchronní operace. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/objektů persongroup/Write | Vytvoří novou skupinu osob se zadaným personGroupId, názvem a uživatelsky dodanými userData. Aktualizujte název a userData existující skupiny osob. Vlastnosti zůstanou beze změny, pokud nejsou v textu žádosti. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/účty/Face/ověřit/akce | Ověřte, zda dvě plošky patří stejné osobě nebo zda jedna ploška patří osobě. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/Custom/Models/analyzovat/Action | Extrahuje páry klíč-hodnota z daného dokumentu. Vstupní dokument musí být jednoho z podporovaných typů obsahu – "Application/PDF", "image/jpeg" nebo "image/png". Ve formátu JSON se vrátí odpověď na úspěch. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/Custom/Models/DELETE | Odstraňte artefakty modelu. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/Custom/Models/Keys/Read | Načte klíče pro model. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/Custom/Models/Read | Získejte informace o modelu. Získat informace o všech proškolených vlastních modelech * |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/Custom/vlak/Action | Vytvoření a výuka vlastního modelu.<br>Požadavek na vlak musí zahrnovat zdrojový parametr, který je externě přístupný Azure Storage identifikátor URI kontejneru objektů BLOB (nejlépe pro identifikátor URI sdíleného přístupového podpisu) nebo platnou cestou ke složce dat na místně připojené jednotce.<br>Pokud jsou zadány místní cesty, musí následovat po formátu cesty Linux/UNIX a musí představovat absolutní cestu, která je kořenem pro vstupní konfiguraci připojení. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/předdefinovaná/Receipt/asyncbatchanalyze/Action | Extrahuje text pole a sémantické hodnoty z daného příjmového dokumentu. Vstupní dokument obrázku musí být jedním z podporovaných typů obsahu – JPEG, PNG, BMP, PDF nebo TIFF. Odpověď na úspěch je JSON obsahující pole s názvem operace-Location, které obsahuje adresu URL pro operaci získat výsledek příjemky pro asynchronní načtení výsledků. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/FormRecognizer/předdefinovaná/Receipt/Operations/Action | Umožňuje zadat dotaz na stav a načíst výsledek operace analýzy příjmu. Adresu URL tohoto rozhraní lze získat z hlavičky "umístění operace" v odpovědi na příjemku analýzy. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ImageSearch/Details/Action | Vrátí přehledy o obrázku, jako jsou například webové stránky, které obsahují obrázek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ImageSearch/Search/Action | Získání relevantních imagí pro daný dotaz. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ImageSearch/LINTREND/Action | Získat aktuálně trendy obrázků. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ImmersiveReader/getcontentmodelforreader/Action | Vytvoří relaci moderního čtenáře. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/InkRecognizer/rozpoznat/Action | Vzhledem k sadě dat tahu analyzuje obsah a generuje seznam rozpoznaných entit, včetně rozpoznaného textu. |
> | Akce | Microsoft.CognitiveServices/accounts/listKeys/action | Zobrazit seznam klíčů |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Načte předpověď publikovaných koncových bodů pro daný dotaz. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/NewsSearch/categorysearch/Action | Vrátí zprávy pro zadanou kategorii. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/NewsSearch/Search/Action | Získejte články, které jsou relevantní pro daný dotaz. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/NewsSearch/trendingtopics/Action | Získejte témata týkající se trendů identifikovaných bingem. Jedná se o stejná témata zobrazená v banneru v dolní části domovské stránky Bingu. |
> | Akce | Microsoft.CognitiveServices/accounts/read | Přečte účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/accounts/regenerateKey/action | Znovu vygenerovat klíč |
> | Akce | Microsoft.CognitiveServices/accounts/skus/read | Přečte dostupné SKU pro existující prostředek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/kontrolu pravopisu/kontrola pravopisu/akce | Vrátí výsledek dotazu kontroly pravopisu prostřednictvím GET nebo POST. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Rozhraní API vrátí seznam známých entit a obecných entit s názvem (\"person\", \"umístění\", \"organizace\" atd.) v daném dokumentu. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Rozhraní API vrátí seznam řetězců označujících klíčová témata ve vstupním textu. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/TextAnalytics/jazyky/akce | Rozhraní API vrátí detekovaný jazyk a číselné hodnocení v rozsahu 0 až 1. Hodnocení blížící se 1 značí 100% jistotu správné identifikace jazyka. Celkem se podporuje 120 jazyků. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Rozhraní API vrátí číselné hodnocení v rozsahu 0 až 1.<br>Skóre blížící se 1 značí kladné mínění, zatímco skóre blížící se 0 značí záporné mínění.<br>Skóre 0,5 označuje nedostatek mínění (např.<br>příkaz factoid). |
> | Akce | Microsoft.CognitiveServices/accounts/usages/read | Získá využití kvóty pro existující prostředek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/VideoSearch/Details/Action | Získejte přehled o videu, jako jsou například související videa. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/VideoSearch/Search/Action | Získejte videa relevantní pro daný dotaz. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/VideoSearch/LINTREND/Action | Získejte aktuálně Trendovaná videa. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/VisualSearch/Search/Action | Vrátí seznam značek relevantních pro poskytnutý obrázek. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/WebSearch/Search/Action | Získejte výsledky pro daný dotaz na webu, obrázky, novinky a & videa. |
> | Akce | Microsoft.CognitiveServices/accounts/write | Zapisuje účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/checkDomainAvailability/action | Přečte dostupné SKU pro předplatné. |
> | Akce | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Přečte dostupné SKU pro předplatné. |
> | Akce | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Přečte dostupné SKU pro předplatné. |
> | Akce | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Oznámení od společnosti Microsoft. Network odstraňování VirtualNetworks nebo podsítí. |
> | Akce | Microsoft. Cognitiveservices Account/Locations/operationresults/Read | Přečtěte si stav asynchronní operace. |
> | Akce | Microsoft.CognitiveServices/Operations/read | Vypsat všechny dostupné operace |
> | Akce | Microsoft.CognitiveServices/register/action | Zaregistruje předplatné pro Cognitive Services. |
> | Akce | Microsoft.CognitiveServices/register/action | Zaregistruje předplatné pro Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Commerce/RateCard/Read | Vrátí data nabídky, metadata prostředku/měřiče a sazby pro dané předplatné. |
> | Akce | Microsoft. obchod/registrace/akce | Registrace předplatného pro Microsoft Commerce UsageAggregate |
> | Akce | Microsoft. Commerce/zrušit registraci/akce | Zrušení registrace předplatného pro Microsoft Commerce UsageAggregate |
> | Akce | Microsoft.Commerce/UsageAggregates/read | Načte spotřebu Microsoft Azure předplatným. Výsledek obsahuje agregovaná data o využití, informace o předplatném a zdroji, v určitém časovém rozsahu. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. COMPUTE/availabilitySets/DELETE | Odstraní skupinu dostupnosti. |
> | Akce | Microsoft. COMPUTE/availabilitySets/Read | Získá vlastnosti skupiny dostupnosti. |
> | Akce | Microsoft. COMPUTE/availabilitySets/povolených velikostí/Read | Vypíše dostupné velikosti pro vytvoření nebo aktualizaci virtuálního počítače ve skupině dostupnosti. |
> | Akce | Microsoft. COMPUTE/availabilitySets/Write | Vytvoří novou skupinu dostupnosti nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/diskEncryptionSets/DELETE | Odstraní sadu šifrování disku. |
> | Akce | Microsoft. COMPUTE/diskEncryptionSets/Read | Získá vlastnosti sady šifrování disku. |
> | Akce | Microsoft. COMPUTE/diskEncryptionSets/Write | Vytvořit novou sadu pro šifrování disku nebo aktualizovat existující |
> | Akce | Microsoft. COMPUTE/disks/beginGetAccess/Action | Získat identifikátor URI SAS disku pro přístup k objektu BLOB |
> | Akce | Microsoft. COMPUTE/disks/DELETE | Odstraní disk. |
> | Akce | Microsoft. COMPUTE/disks/endGetAccess/Action | Odvolání identifikátoru URI SAS disku |
> | Akce | Microsoft. COMPUTE/disky/číst | Získá vlastnosti disku. |
> | Akce | Microsoft. COMPUTE/disks/Write | Vytvoří nový disk nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/Galerie/aplikace/odstranit | Odstraní aplikaci Galerie. |
> | Akce | Microsoft. COMPUTE/Galerie/aplikace/číst | Získá vlastnosti aplikace Galerie. |
> | Akce | Microsoft. COMPUTE/Galerie/aplikace/verze/odstranění | Odstraní verzi aplikace Galerie. |
> | Akce | Microsoft. COMPUTE/Galerie/aplikace/verze/čtení | Získá vlastnosti verze aplikace Galerie. |
> | Akce | Microsoft. COMPUTE/Galerie/aplikace/verze/zápis | Vytvoří novou verzi aplikace Galerie nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/Galerie/aplikace/zápis | Vytvoří novou aplikaci Galerie nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/Galerie/odstranění | Odstraní galerii. |
> | Akce | Microsoft.Compute/galleries/images/delete | Odstraní obrázek Galerie. |
> | Akce | Microsoft.Compute/galleries/images/read | Získá vlastnosti obrázku galerie. |
> | Akce | Microsoft.Compute/galleries/images/versions/delete | Odstraní verzi image galerie. |
> | Akce | Microsoft.Compute/galleries/images/versions/read | Získá vlastnosti verze image galerie. |
> | Akce | Microsoft. COMPUTE/Galerie/images/verze/zápis | Vytvoří novou verzi Image Galerie nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/galleries/images/write | Vytvoří novou image galerie nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/Galerie/čtení | Získá vlastnosti galerie. |
> | Akce | Microsoft. COMPUTE/Galerie/zápis | Vytvoří novou galerii nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/hostGroups/delete | Odstraní skupinu hostitelů. |
> | Akce | Microsoft. COMPUTE/hostGroups/Hosts/DELETE | Odstraní hostitele. |
> | Akce | Microsoft. COMPUTE/hostGroups/Hosts/Read | Získání vlastností hostitele |
> | Akce | Microsoft. COMPUTE/hostGroups/Hosts/Write | Vytvoří nového hostitele nebo aktualizuje stávajícího hostitele. |
> | Akce | Microsoft.Compute/hostGroups/read | Získá vlastnosti skupiny hostitelů. |
> | Akce | Microsoft.Compute/hostGroups/write | Vytvoří novou skupinu hostitelů nebo aktualizuje stávající skupinu hostitelů. |
> | Akce | Microsoft. COMPUTE/images/DELETE | Odstraní obrázek. |
> | Akce | Microsoft. COMPUTE/images/Read | Získá vlastnosti obrázku. |
> | Akce | Microsoft. COMPUTE/images/Write | Vytvoří novou bitovou kopii nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/Locations/capsOperations/Read | Získá stav operace asynchronního zakončení. |
> | Akce | Microsoft.Compute/locations/diskOperations/read | Získá stav operace asynchronního disku. |
> | Akce | Microsoft. COMPUTE/Locations/logAnalytics/getRequestRateByInterval/Action | Vytvořte protokoly, abyste zobrazili celkový počet požadavků podle časového intervalu na podporu pro diagnostiku omezování. |
> | Akce | Microsoft. COMPUTE/Locations/logAnalytics/getThrottledRequests/Action | Vytvořte protokoly, aby se zobrazily agregované požadavky s omezením seskupené podle hodnoty resourceName, OperationName nebo aplikovaných zásad omezení. |
> | Akce | Microsoft. COMPUTE/umístění/operace/čtení | Získá stav asynchronní operace. |
> | Akce | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/nabídky/číst | Získat vlastnosti nabídky image platformy |
> | Akce | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/nabídky/SKU/čtení | Získá vlastnosti SKU image platformy. |
> | Akce | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/nabídky/SKU/verze/čtení | Získá vlastnosti verze image platformy. |
> | Akce | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/typy/čtení | Získá vlastnosti VMExtension typu. |
> | Akce | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/typy/verze/číst | Získá vlastnosti verze VMExtension. |
> | Akce | Microsoft. COMPUTE/umístění/vydavatelé/číst | Získat vlastnosti vydavatele |
> | Akce | Microsoft. COMPUTE/Locations/runCommands/Read | Zobrazí seznam dostupných příkazů spuštění v umístění. |
> | Akce | Microsoft. COMPUTE/umístění/využití/čtení | Získá omezení služby a aktuální množství využití výpočetních prostředků předplatného v daném umístění. |
> | Akce | Microsoft. COMPUTE/Locations/povolených velikostí/Read | Zobrazí seznam dostupných velikostí virtuálních počítačů v daném umístění. |
> | Akce | Microsoft. COMPUTE/Locations/vsmOperations/Read | Získá stav asynchronní operace pro sadu škálování virtuálního počítače s rozšířením služby virtuálního počítače za běhu. |
> | Akce | Microsoft. COMPUTE/Operations/Read | Zobrazí seznam operací, které jsou k dispozici na poskytovateli prostředků Microsoft. Compute. |
> | Akce | Microsoft. COMPUTE/proximityPlacementGroups/DELETE | Odstraní skupinu umístění blízkosti. |
> | Akce | Microsoft. COMPUTE/proximityPlacementGroups/Read | Získá vlastnosti skupiny umístění blízkosti. |
> | Akce | Microsoft. COMPUTE/proximityPlacementGroups/Write | Vytvoří novou skupinu umístění pro proximity nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/registr/Action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. Compute. |
> | Akce | Microsoft.Compute/restorePointCollections/delete | Odstraní kolekci bodů obnovení a obsažené body obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/read | Získá vlastnosti kolekce bodů obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/delete | Odstraní bod obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/read | Získá vlastnosti bodu obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Získat vlastnosti bodu obnovení spolu s identifikátory URI SAS objektů BLOB |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/write | Vytvoří nový bod obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/write | Vytvoří novou kolekci bodů obnovení nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/sharedVMImages/DELETE | Odstraní SharedVMImage |
> | Akce | Microsoft. COMPUTE/sharedVMImages/Read | Získá vlastnosti SharedVMImage. |
> | Akce | Microsoft. COMPUTE/sharedVMImages/verze/DELETE | Odstranit SharedVMImageVersion |
> | Akce | Microsoft. COMPUTE/sharedVMImages/verze/číst | Získá vlastnosti SharedVMImageVersion. |
> | Akce | Microsoft. COMPUTE/sharedVMImages/verze/replika/akce | Replikace SharedVMImageVersion do cílových oblastí |
> | Akce | Microsoft. COMPUTE/sharedVMImages/verze/Write | Vytvoří novou SharedVMImageVersion nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/sharedVMImages/write | Vytvoří nový SharedVMImage nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/SKU/číst | Získá seznam skladových položek Microsoft. COMPUTE dostupných pro vaše předplatné. |
> | Akce | Microsoft. COMPUTE/snímky/beginGetAccess/Action | Získat identifikátor URI SAS snímku pro přístup k objektu BLOB |
> | Akce | Microsoft. COMPUTE/snímky/odstranit | Odstranění snímku |
> | Akce | Microsoft. COMPUTE/snímky/endGetAccess/Action | Odvolání identifikátoru URI SAS snímku |
> | Akce | Microsoft. COMPUTE/snímky/číst | Získá vlastnosti snímku. |
> | Akce | Microsoft. COMPUTE/snímky/zápis | Vytvoří nový snímek nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/zrušit registraci/akce | Zruší registraci předplatného u poskytovatele prostředků Microsoft. Compute. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Capture/Action | Zachycuje virtuální počítač kopírováním virtuálních pevných disků a vygeneruje šablonu, která se dá použít k vytvoření podobných virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/Action | Převede disky na bázi objektů BLOB virtuálního počítače na spravované disky. |
> | Akce | Microsoft. COMPUTE/virtualMachines/unallocate/Action | Vyžádá virtuální počítač a uvolní výpočetní prostředky. |
> | Akce | Microsoft.Compute/virtualMachines/delete | Odstraní virtuální počítač. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Extensions/DELETE | Odstraní rozšíření virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Extensions/Read | Získá vlastnosti rozšíření virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Extensions/Write | Vytvoří nové rozšíření virtuálního počítače nebo aktualizuje stávající. |
> | Akce | Microsoft. COMPUTE/virtualMachines/generalize/akce | Nastaví stav virtuálního počítače jako zobecněný a připraví virtuální počítač pro zachytávání. |
> | Akce | Microsoft. COMPUTE/virtualMachines/instanceView/Read | Získá podrobný běhový stav virtuálního počítače a jeho prostředků. |
> | Akce dataaction | Microsoft. COMPUTE/virtualMachines/Login/Action | Přihlaste se k virtuálnímu počítači jako běžný uživatel. |
> | Akce dataaction | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/Action | Přihlášení k virtuálnímu počítači pomocí oprávnění správce Windows nebo kořenového uživatele platformy Linux |
> | Akce | Microsoft. COMPUTE/virtualMachines/performMaintenance/Action | Provede na virtuálním počítači operaci údržby. |
> | Akce | Microsoft. COMPUTE/virtualMachines/stavu PowerOff/Action | Umožňuje vypnout virtuální počítač. Všimněte si, že se virtuální počítač bude nadále účtovat. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Read | Získá vlastnosti virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachines/znovu nasadit/akce | Znovu nasadí virtuální počítač. |
> | Akce | Microsoft. COMPUTE/virtualMachines/ReImage/Action | Obnoví virtuální počítač, který používá rozdílový disk, z image. |
> | Akce | Microsoft. COMPUTE/virtualMachines/restart/Action | Restartuje virtuální počítač. |
> | Akce | Microsoft. COMPUTE/virtualMachines/SpustitPříkaz/Action | Spustí na virtuálním počítači předdefinovaný skript. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Start/Action | Spustí virtuální počítač. |
> | Akce | Microsoft.Compute/virtualMachines/vmSizes/read | Zobrazí seznam dostupných velikostí, na které se dá virtuální počítač aktualizovat. |
> | Akce | Microsoft. COMPUTE/virtualMachines/Write | Vytvoří nový virtuální počítač nebo aktualizuje stávající virtuální počítač. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/unallocate/Action | Vypíná a uvolňuje výpočetní prostředky pro instance sady škálování virtuálních počítačů.  |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/DELETE | Odstraní sadu škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Odstraní instance sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/DELETE | Odstraní rozšíření sady škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Získá vlastnosti rozšíření sady škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Vytvoří nové rozšíření sady škálování virtuálního počítače nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ruční procházení domén aktualizace platformy pro sadu škálování virtuálního počítače Service Fabric, aby se dokončila zablokovaná nedokončená aktualizace |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/instanceView/Read | Získá zobrazení instance sady škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/Action | Aktualizuje instance ručně na nejnovější model sady škálování virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Získá vlastnosti všech síťových rozhraní sady škálování virtuálního počítače. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Spustí postupný upgrade, aby se přesunuly všechny instance sady škálování virtuálních počítačů na nejnovější dostupnou verzi operačního systému image platformy. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/osUpgradeHistory/Read | Načte historii upgradů operačního systému pro sadu škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/Action | Provede plánovanou údržbu instancí sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/stavu PowerOff/Action | Umožňuje vypnout instance sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/publicIPAddresses/Read | Získá vlastnosti všech veřejných IP adres sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Read | Získá vlastnosti sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/znovu nasadit/akce | Znovu nasaďte instance sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/ReImage/Action | Obnoví instance sady škálování virtuálních počítačů z image. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/reimageAll/Action | Obnoví z image všechny disky (disk s operačním systémem a datové disky) pro instance sady škálování virtuálních počítačů.  |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/restart/Action | Restartuje instance sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Zruší postupný upgrade sady škálování virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Získat nejnovější stav upgradu pro sadu škálování virtuálního počítače |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Scale/Action | Ověřte, jestli se může existující sada škálování virtuálního počítače škálovat nebo škálovat na zadaný počet instancí. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/SKU/Read | Vypíše platné SKU pro existující sadu škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Start/Action | Spustí instance sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/unallocate/Action | Vypíná a uvolňuje výpočetní prostředky pro virtuální počítač v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/DELETE | Odstraní konkrétní virtuální počítač v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/instanceView/Read | Načte zobrazení instance virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Získá vlastnosti veřejné IP adresy vytvořené pomocí sady škálování virtuálních počítačů. Sada škálování virtuálního počítače může vytvořit maximálně jednu veřejnou IP adresu na IPConfiguration (privátní IP adresa). |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Získá vlastnosti jedné nebo všech konfigurací IP síťového rozhraní vytvořeného pomocí sady škálování virtuálního počítače. Konfigurace protokolu IP reprezentují privátní IP adresy. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Read | Získá vlastnosti jednoho nebo všech síťových rozhraní virtuálního počítače vytvořeného pomocí sady škálování virtuálních počítačů. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Provede plánovanou údržbu instance virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/stavu PowerOff/Action | Umožňuje vypnout instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Read | Načte vlastnosti virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/znovu nasadit/akce | Znovu nasadí instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/ReImage/Action | Obnoví instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/reimageAll/Action | Obnoví z image všechny disky (disk s operačním systémem a datové disky) pro instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/Action | Restartuje instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/SpustitPříkaz/Action | Spustí předdefinovaný skript v instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Start/Action | Spustí instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Aktualizuje vlastnosti virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/povolených velikostí/Read | Vypíše dostupné velikosti pro vytvoření nebo aktualizaci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Akce | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Vytvoří novou sadu škálování virtuálního počítače nebo aktualizuje stávající. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. spotřeb/aggregatedcost/Read | Seznam AggregatedCost pro skupinu pro správu. |
> | Akce | Microsoft. spotřeb/bilance/číst | Vypíše Souhrn využití pro fakturační období pro skupinu pro správu. |
> | Akce | Microsoft. spotřeb/rozpočty/odstranit | Odstraňte rozpočty podle předplatného nebo skupiny pro správu. |
> | Akce | Microsoft. spotřeb/rozpočty/číst | Vypíše rozpočty podle předplatného nebo skupiny pro správu. |
> | Akce | Microsoft. spotřeb/rozpočty/zápis | Vytvoří a aktualizuje rozpočty pomocí předplatného nebo skupiny pro správu. |
> | Akce | Microsoft. spotřeba/poplatky/čtení | Poplatky za seznam |
> | Akce | Microsoft. spotřeba/kredity/čtení | Zobrazit kredity |
> | Akce | Microsoft. spotřeb/Events/číst | Události seznamu |
> | Akce | Microsoft. spotřeb/externalBillingAccounts/Tags/Read | Vypíše značky pro EA a Subscriptions. |
> | Akce | Microsoft. spotřeb/externalSubscriptions/Tags/Read | Vypíše značky pro EA a Subscriptions. |
> | Akce | Microsoft. spotřeb/prognózy/číst | Vypsat předpovědi |
> | Akce | Microsoft. spotřeba/dávky/čtení | Vypsat spousty |
> | Akce | Microsoft. spotřeb/Marketplace/číst | Vypíše podrobnosti o využití prostředků Marketplace v oboru pro předplatná EA a WebDirect. |
> | Akce | Microsoft. spotřeb/operationresults/Read | Seznam operationresults |
> | Akce | Microsoft. spotřeb/Operations/Read | Vypíše všechny podporované operace od poskytovatele prostředků Microsoft. spotřeb. |
> | Akce | Microsoft.Consumption/operationstatus/read | Seznam stav operationstatus |
> | Akce | Microsoft. spotřeb/pricesheets/Read | Vypíše Pricesheets data pro předplatné nebo skupinu pro správu. |
> | Akce | Microsoft. spotřeba/registrace/akce | Registrovat do RP spotřeby |
> | Akce | Microsoft. spotřeb/reservationDetails/Read | Seznam podrobností o využití pro rezervované instance podle pořadí rezervací nebo skupin pro správu. Data podrobností jsou na úrovni jednotlivých instancí a dnů. |
> | Akce | Microsoft. spotřeb/reservationRecommendations/Read | Seznamte se s jedním nebo sdíleným doporučením pro rezervované instance pro předplatné. |
> | Akce | Microsoft. spotřeb/reservationSummaries/Read | Vypíše Souhrn využití rezervovaných instancí podle pořadí rezervací nebo skupin pro správu. Souhrnná data jsou buď na úrovni měsíčního, nebo na denní úrovni. |
> | Akce | Microsoft. spotřeb/reservationTransactions/Read | Vypíše historii transakcí pro rezervované instance podle skupin pro správu. |
> | Akce | Microsoft. spotřeb/Tags/číst | Vypíše značky pro EA a Subscriptions. |
> | Akce | Microsoft. spotřeba/klienti/čtení | Seznam tenantů |
> | Akce | Microsoft. spotřeba/klienti/registrace/akce | Zaregistrujte akci pro obor Microsoft. spotřebujte u tenanta. |
> | Akce | Microsoft. spotřeb/terms/Read | Zobrazí seznam podmínek předplatného nebo skupiny pro správu. |
> | Akce | Microsoft. spotřeb/usageDetails/Read | Vypíšete podrobnosti o využití oboru pro předplatné EA a WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. ContainerInstance/containerGroups/Containers/buildlogs/Read | Získá protokoly sestavení pro konkrétní kontejner. |
> | Akce | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec do konkrétního kontejneru. |
> | Akce | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Získá protokoly pro konkrétní kontejner. |
> | Akce | Microsoft.ContainerInstance/containerGroups/delete | Odstraňte konkrétní skupinu kontejnerů. |
> | Akce | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Získat výsledek asynchronní operace |
> | Akce | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro skupinu kontejnerů. |
> | Akce | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/read | Získá všechny skupiny kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/restart/action | Restartuje konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/start/action | Spustí konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/stop/action | Zastaví konkrétní skupinu kontejnerů. Výpočetní prostředky budou uvolněny a fakturace se zastaví. |
> | Akce | Microsoft.ContainerInstance/containerGroups/write | Vytvoří nebo aktualizuje konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/locations/cachedImages/read | Načte image v mezipaměti pro odběr v oblasti. |
> | Akce | Microsoft. ContainerInstance/umístění/možnosti/číst | Získejte možnosti pro oblast. |
> | Akce | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoft. ContainerInstance, že se odstraňuje virtuální síť nebo podsíť. |
> | Akce | Microsoft. ContainerInstance/Locations/Operations/Read | Vypíše operace pro službu Azure Container instance. |
> | Akce | Microsoft.ContainerInstance/locations/usages/read | Získejte využití konkrétní oblasti. |
> | Akce | Microsoft. ContainerInstance/Operations/Read | Vypíše operace pro službu Azure Container instance. |
> | Akce | Microsoft.ContainerInstance/register/action | Zaregistruje předplatné pro poskytovatele prostředků instance kontejneru a povolí vytváření skupin kontejnerů. |
> | Akce | Microsoft. ContainerInstance/serviceassociationlinks/DELETE | Odstraní odkaz na přidružení služby vytvořený poskytovatelem prostředků Azure Container instance v podsíti. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontroluje, zda je název registru kontejneru k dispozici pro použití. |
> | Akce | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoftu. ContainerRegistry, že se odstraňuje virtuální síť nebo podsíť. |
> | Akce | Microsoft.ContainerRegistry/locations/operationResults/read | Načte výsledek asynchronní operace. |
> | Akce | Microsoft.ContainerRegistry/operations/read | Zobrazí seznam všech dostupných Azure Container Registrych operací REST API. |
> | Akce | Microsoft.ContainerRegistry/register/action | Zaregistruje předplatné pro poskytovatele prostředků registru kontejneru a povolí vytváření registrů kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/artifacts/delete | Odstraňte artefakt v registru kontejnerů. |
> | Akce | Microsoft. ContainerRegistry/Registry/Builds/Cancel/Action | Zruší existující sestavení. |
> | Akce | Microsoft. ContainerRegistry/Registry/Build/getLogLink/Action | Načte odkaz pro stažení protokolů sestavení. |
> | Akce | Microsoft. ContainerRegistry/Registry/Build/Read | Získá vlastnosti zadaného sestavení nebo Vypíše všechna sestavení pro zadaný registr kontejnerů. |
> | Akce | Microsoft. ContainerRegistry/Registry/Build/Write | Aktualizuje sestavení pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/delete | Odstraní úlohu sestavení z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Obsahuje seznam vlastností správy zdrojového kódu pro úlohu sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/read | Získá vlastnosti zadané úlohy sestavení nebo zobrazí seznam všech úloh sestavení pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Odstraní krok sestavení z úlohy sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Vypíše argumenty sestavení pro krok sestavení včetně tajných argumentů. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Získá vlastnosti zadaného kroku sestavení nebo zobrazí seznam všech kroků sestavení pro zadanou úlohu sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Vytvoří nebo aktualizuje krok sestavení pro úlohu sestavení se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/write | Vytvoří nebo aktualizuje úlohu sestavení pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/delete | Odstraní registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Odstraní filtr Event gridu z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Získá vlastnosti zadaného filtru Event gridu nebo vypíše všechny filtry služby Event Grid pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event gridu pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft. ContainerRegistry/Registry/generateCredentials/Action | Vygeneruje klíče pro token určeného registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Získá umístění pro odeslání pro uživatele, aby mohl odeslat zdroj. |
> | Akce | Microsoft.ContainerRegistry/registries/importImage/action | Importujte image do registru kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Získá umístění adresy URL pro nahrání zdroje pro registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/listCredentials/action | Zobrazí seznam přihlašovacích údajů pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/listPolicies/read | Zobrazí seznam zásad pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/listUsages/read | Vypíše využití kvót pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/metadata/read | Načte metadata konkrétního úložiště pro registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/metadata/write | Aktualizuje metadata úložiště pro registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/operationStatuses/read | Načte stav asynchronní operace v registru. |
> | Akce | Microsoft.ContainerRegistry/registries/pull/read | Vyžádání nebo získání imagí z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/push/write | Vložení nebo zápis imagí do registru kontejneru. |
> | Akce | Microsoft. ContainerRegistry/registrys/karanténa/čtení | Vyžádání nebo získání imagí v karanténě z registru kontejnerů |
> | Akce | Microsoft. ContainerRegistry/registrys/Quarantine/Write | Zápis/Změna stavu karantény imagí v karanténě |
> | Akce | Microsoft.ContainerRegistry/registries/queueBuild/action | Vytvoří nové sestavení založené na parametrech žádosti a přidá je do fronty sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/read | Získá vlastnosti zadaného registru kontejneru nebo zobrazí seznam všech registrů kontejnerů v rámci zadané skupiny prostředků nebo předplatného. |
> | Akce | Microsoft. ContainerRegistry/Registry/regenerateCredential/Action | Znovu vygeneruje jedno z přihlašovacích údajů pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/delete | Odstraní replikaci z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Získá stav asynchronní operace replikace. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/read | Získá vlastnosti zadané replikace nebo vypíše všechny replikace určeného registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/write | Vytvoří nebo aktualizuje replikaci pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft. ContainerRegistry/Registry/spuštění/zrušení/akce | Zruší existující běh. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Získá adresu URL protokolu SAS protokolu pro běh. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/read | Získá vlastnosti spuštění proti registru kontejneru nebo spuštění seznamu. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/write | Aktualizuje běh. |
> | Akce | Microsoft.ContainerRegistry/registries/scheduleRun/action | Naplánování spuštění proti registru kontejneru. |
> | Akce | Microsoft. ContainerRegistry/Registry/scopeMaps/DELETE | Odstraní mapování oboru z registru kontejneru. |
> | Akce | Microsoft. ContainerRegistry/Registry/scopeMaps/operationStatuses/Read | Získá stav asynchronní operace mapování oboru. |
> | Akce | Microsoft. ContainerRegistry/Registry/scopeMaps/Read | Získá vlastnosti zadané mapy oboru nebo vypíše všechny mapy oboru pro zadaný registr kontejnerů. |
> | Akce | Microsoft. ContainerRegistry/Registry/scopeMaps/Write | Vytvoří nebo aktualizuje mapu oboru pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/sign/write | Metadata vztahu důvěryhodnosti nabízených nebo vyžádaného obsahu pro registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/delete | Odstraní úlohu pro registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Zobrazí seznam všech podrobností úlohy pro registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/read | Načte úlohu pro registr kontejneru nebo seznam všech úkolů. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/write | Vytvoří nebo aktualizuje úlohu pro registr kontejneru. |
> | Akce | Microsoft. ContainerRegistry/registrys/tokens/DELETE | Odstraní token z registru kontejneru. |
> | Akce | Microsoft. ContainerRegistry/registrys/tokens/operationStatuses/Read | Získá stav asynchronní operace tokenu. |
> | Akce | Microsoft. ContainerRegistry/registrys/tokens/Read | Získá vlastnosti zadaného tokenu nebo vypíše všechny tokeny zadaného registru kontejneru. |
> | Akce | Microsoft. ContainerRegistry/registrys/tokens/Write | Vytvoří nebo aktualizuje token pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizuje zásady pro zadaný registr kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/delete | Odstraní Webhook z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Získá konfiguraci identifikátoru URI služby a vlastních hlaviček pro Webhook. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Vypíše nedávné události pro zadaný Webhook. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Získá stav asynchronní operace Webhooku. |
> | Akce | Microsoft. ContainerRegistry/Registry/webhooks/test/Action | Aktivuje událost testu, která se pošle Webhooku. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/read | Získá vlastnosti zadaného Webhooku nebo vypíše všechny Webhooky zadaného registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/write | Vytvoří nebo aktualizuje Webhook pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/write | Vytvoří nebo aktualizuje registr kontejneru se zadanými parametry. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerService/containerServices/delete | Odstraní službu kontejneru. |
> | Akce | Microsoft.ContainerService/containerServices/read | Získat službu kontejneru |
> | Akce | Microsoft.ContainerService/containerServices/write | Vytvoří novou službu kontejneru nebo aktualizuje stávající. |
> | Akce | Microsoft.ContainerService/locations/operationresults/read | Získá stav výsledku asynchronní operace. |
> | Akce | Microsoft.ContainerService/locations/operations/read | Získá stav asynchronní operace. |
> | Akce | Microsoft.ContainerService/locations/orchestrators/read | Vypisuje podporované orchestrace. |
> | Akce | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Získání profilu přístupu spravovaného clusteru podle názvu role pomocí přihlašovacích údajů pro seznam |
> | Akce | Microsoft.ContainerService/managedClusters/accessProfiles/read | Získání profilu přístupu spravovaného clusteru podle názvu role |
> | Akce | Microsoft.ContainerService/managedClusters/agentPools/delete | Odstraní fond agentů. |
> | Akce | Microsoft.ContainerService/managedClusters/agentPools/read | Načte fond agentů. |
> | Akce | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Získá profil upgradu fondu agentů. |
> | Akce | Microsoft.ContainerService/managedClusters/agentPools/write | Vytvoří nový fond agentů nebo aktualizuje stávající. |
> | Akce | Microsoft. ContainerService/managedClusters/availableAgentPoolVersions/Read | Získá dostupné verze fondu agentů clusteru. |
> | Akce | Microsoft.ContainerService/managedClusters/delete | Odstraní spravovaný cluster. |
> | Akce | Microsoft. ContainerService/managedClusters/detektory/číst | Získat detektor spravovaného clusteru |
> | Akce | Microsoft. ContainerService/managedClusters/diagnosticsState/Read | Načte stav diagnostiky clusteru. |
> | Akce | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Výpis přihlašovacích údajů clusterAdmin spravovaného clusteru |
> | Akce | Microsoft. ContainerService/managedClusters/listClusterMonitoringUserCredential/Action | Výpis přihlašovacích údajů clusterMonitoringUser spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Výpis přihlašovacích údajů clusterUser spravovaného clusteru |
> | Akce | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/Action | Určuje, jestli má uživatel povolený souhlas s připojením privátního koncového bodu. |
> | Akce | Microsoft.ContainerService/managedClusters/read | Získání spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Resetovat profil AAD spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Resetování hlavního profilu služby spravovaného clusteru |
> | Akce | Microsoft. ContainerService/managedClusters/rotateClusterCertificates/Action | Otočení certifikátů spravovaného clusteru |
> | Akce | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Získá profil upgradu clusteru. |
> | Akce | Microsoft.ContainerService/managedClusters/write | Vytvoří nový spravovaný cluster nebo aktualizuje stávající. |
> | Akce | Microsoft.ContainerService/openShiftClusters/delete | Odstraní otevřený cluster Shift. |
> | Akce | Microsoft.ContainerService/openShiftClusters/read | Získat otevřený cluster Shift |
> | Akce | Microsoft.ContainerService/openShiftClusters/write | Vytvoří nový cluster Open Shift nebo aktualizuje stávající. |
> | Akce | Microsoft.ContainerService/openShiftManagedClusters/delete | Odstranění spravovaného clusteru s otevřeným Shift |
> | Akce | Microsoft.ContainerService/openShiftManagedClusters/read | Získání spravovaného clusteru s otevřeným Shift |
> | Akce | Microsoft.ContainerService/openShiftManagedClusters/write | Vytvoří nový spravovaný cluster s otevřeným Shift nebo aktualizuje stávající. |
> | Akce | Microsoft.ContainerService/operations/read | Zobrazí seznam operací dostupných v poskytovateli prostředků Microsoft. ContainerService. |
> | Akce | Microsoft.ContainerService/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. ContainerService. |
> | Akce | Microsoft.ContainerService/unregister/action | Zruší registraci předplatného u poskytovatele prostředků Microsoft. ContainerService. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. CostManagement/Alerts/Write | Aktualizujte výstrahy. |
> | Akce | Microsoft.CostManagement/cloudConnectors/delete | Odstraní zadaný cloudConnector. |
> | Akce | Microsoft.CostManagement/cloudConnectors/read | Vypíše seznam cloudConnectors pro ověřeného uživatele. |
> | Akce | Microsoft.CostManagement/cloudConnectors/write | Vytvoří nebo aktualizuje zadaný cloudConnector. |
> | Akce | Microsoft. CostManagement/Dimensions/Read | Vypíše všechny podporované dimenze podle oboru. |
> | Akce | Microsoft. CostManagement/EXPORTS/Action | Spusťte zadaný export. |
> | Akce | Microsoft.CostManagement/exports/delete | Odstraní zadaný export. |
> | Akce | Microsoft.CostManagement/exports/read | Vypíše export podle oboru. |
> | Akce | Microsoft. CostManagement/EXPORTS/Run/Action | Spusťte exporty. |
> | Akce | Microsoft.CostManagement/exports/write | Vytvoří nebo aktualizuje zadaný export. |
> | Akce | Microsoft. CostManagement/externalBillingAccounts/Dimension/Read | Vypíše všechny podporované dimenze pro externí BillingAccounts. |
> | Akce | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Vypíše externalSubscriptionsy v rámci externalBillingAccount pro ověřeného uživatele. |
> | Akce | Microsoft. CostManagement/externalBillingAccounts/předpověď/Action | Vypovědět data o využití pro externí BillingAccounts |
> | Akce | Microsoft. CostManagement/externalBillingAccounts/FORECAST/Read | Vypovědět data o využití pro externí BillingAccounts |
> | Akce | Microsoft. CostManagement/externalBillingAccounts/dotaz/akce | Dotazování na data o využití pro externí BillingAccounts |
> | Akce | Microsoft. CostManagement/externalBillingAccounts/dotaz/číst | Dotazování na data o využití pro externí BillingAccounts |
> | Akce | Microsoft.CostManagement/externalBillingAccounts/read | Vypíše seznam externalBillingAccounts pro ověřeného uživatele. |
> | Akce | Microsoft. CostManagement/externalSubscriptions/Dimensions/Read | Vypíše všechny podporované dimenze pro externí předplatné. |
> | Akce | Microsoft. CostManagement/externalSubscriptions/předpověď/Action | Vypovědět data o využití pro externí BillingAccounts |
> | Akce | Microsoft. CostManagement/externalSubscriptions/FORECAST/Read | Vypovědět data o využití pro externí BillingAccounts |
> | Akce | Microsoft. CostManagement/externalSubscriptions/dotaz/akce | Dotaz na data o využití pro externí předplatné |
> | Akce | Microsoft. CostManagement/externalSubscriptions/dotaz/číst | Dotaz na data o využití pro externí předplatné |
> | Akce | Microsoft. CostManagement/externalSubscriptions/Read | Vypíše seznam externalSubscriptions pro ověřeného uživatele. |
> | Akce | Microsoft. CostManagement/externalSubscriptions/Write | Aktualizovat přidruženou skupinu pro správu externalSubscription |
> | Akce | Microsoft. CostManagement/předpověď/Action | Vypovědět data o využití podle oboru. |
> | Akce | Microsoft. CostManagement/předpověď/Read | Vypovědět data o využití podle oboru. |
> | Akce | Microsoft. CostManagement/Operations/Read | Vypíše všechny podporované operace od poskytovatele prostředků Microsoft. CostManagement. |
> | Akce | Microsoft. CostManagement/dotaz/akce | Dotazování dat o využití podle oboru |
> | Akce | Microsoft.CostManagement/query/read | Dotazování dat o využití podle oboru |
> | Akce | Microsoft.CostManagement/register/action | Zaregistrujte akci pro obor Microsoft. CostManagement v rámci předplatného. |
> | Akce | Microsoft. CostManagement/Reports/Action | Plánování sestav o využití dat podle oboru |
> | Akce | Microsoft.CostManagement/reports/read | Plánování sestav o využití dat podle oboru |
> | Akce | Microsoft.CostManagement/tenants/register/action | Zaregistrujte akci pro obor Microsoft. CostManagement v tenantovi. |
> | Akce | Microsoft. CostManagement/zobrazení/akce | Vytvořte zobrazení. |
> | Akce | Microsoft. CostManagement/views/DELETE | Odstranění uložených zobrazení |
> | Akce | Microsoft. CostManagement/zobrazení/čtení | Vypíše všechna uložená zobrazení. |
> | Akce | Microsoft. CostManagement/views/Write | Zobrazení aktualizace. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. DataBox/Jobs/bookShipmentPickUp/Action | Umožňuje rezervovat vyzvednutí pro návratové dodávky. |
> | Akce | Microsoft. DataBox/Jobs/Cancel/Action | Zruší probíhající objednávku. |
> | Akce | Microsoft.DataBox/jobs/delete | Odstranit objednávky |
> | Akce | Microsoft. DataBox/Jobs/listCredentials/Action | Zobrazí nešifrované přihlašovací údaje související s objednávkou. |
> | Akce | Microsoft. DataBox/Jobs/číst | Vypsat nebo získat objednávky |
> | Akce | Microsoft.DataBox/jobs/write | Vytvořit nebo aktualizovat objednávky |
> | Akce | Microsoft. DataBox/Locations/availableSkus/Action | Tato metoda vrátí seznam dostupných skladových položek. |
> | Akce | Microsoft. DataBox/Locations/availableSkus/Read | Vypsat nebo získat dostupné SKU |
> | Akce | Microsoft.DataBox/locations/operationResults/read | Vypsat nebo získat výsledky operace |
> | Akce | Microsoft. DataBox/Locations/regionConfiguration/Action | Tato metoda vrátí konfigurace pro oblast. |
> | Akce | Microsoft. DataBox/Locations/validateAddress/Action | Ověří dodací adresu a v případě potřeby poskytne alternativní adresy. |
> | Akce | Microsoft. DataBox/Locations/validateInputs/Action | Tato metoda provádí všechny typy ověření. |
> | Akce | Microsoft. DataBox/Operations/Read | Vypíše nebo získá operace. |
> | Akce | Microsoft.DataBox/register/action | Registrovat poskytovatele Microsoft. Databox |
> | Akce | Microsoft. DataBox/Subscriptions/resourceGroups/moveResources/Action | Tato metoda provede přesunutí prostředku. |
> | Akce | Microsoft. DataBox/Subscriptions/resourceGroups/validateMoveResources/Action | Tato metoda ověřuje, zda je povolený přesun prostředku. |
> | Akce | Microsoft. DataBox/zrušit registraci/akce | Zrušit registraci poskytovatele Microsoft. Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Zobrazí nebo získá výstrahy. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Odstraní plány šířky pásma. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Zobrazí nebo získá plány šířky pásma. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Zobrazí nebo získá plány šířky pásma. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Vytvoří nebo aktualizuje plány šířky pásma. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Odstraní zařízení Data Box Edge. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Stáhnout aktualizace v zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Načte rozšířené informace o prostředku. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalovat aktualizace na zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Zobrazí nebo získá úlohy. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Zobrazí nebo získá nastavení sítě zařízení. |
> | Akce | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Nodes/Read | Zobrazí nebo získá uzly. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Zobrazí nebo získá stav operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Odstraní objednávky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Zobrazí nebo získá objednávky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Zobrazí nebo získá objednávky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Vytvoří nebo aktualizuje objednávky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Zobrazí nebo získá Data Box Edge zařízení. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Zobrazí nebo získá Data Box Edge zařízení. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Zobrazí nebo získá Data Box Edge zařízení. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Odstraní role. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Zobrazí nebo získá role. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Zobrazí nebo získá role. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Vytvoří nebo aktualizuje role. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Vyhledat aktualizace |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizovat nastavení zabezpečení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Odstraní sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Zobrazí nebo získá sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Zobrazí nebo získá sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Obnovte metadata sdílení s daty z cloudu. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Vytvoří nebo aktualizuje sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Odstraní přihlašovací údaje účtu úložiště. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Zobrazí nebo získá přihlašovací údaje účtu úložiště. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Zobrazí nebo získá přihlašovací údaje účtu úložiště. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Vytvoří nebo aktualizuje přihlašovací údaje účtu úložiště. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Odstraní triggery. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Zobrazí nebo získá aktivační události. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Zobrazí nebo získá aktivační události. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Vytvoří nebo aktualizuje triggery. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Zobrazí nebo získá souhrn aktualizace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Nahrát certifikát pro registraci zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Odstraní uživatele sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Zobrazí nebo získá uživatele sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Zobrazí nebo získá uživatele sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Vytvoří nebo aktualizuje uživatele sdílené složky. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Vytvoří nebo aktualizuje Data Box Edge zařízení. |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Vytvoří nebo aktualizuje Data Box Edge zařízení. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Databricks/locations/getNetworkPolicies/action | Získání zásad záměru sítě pro podsíť na základě umístění používaného službou NRP |
> | Akce | Microsoft. datacihly/umístění/operationstatuses/čtení | Reads the operation status for the resource. |
> | Akce | Microsoft. datacihly/operace/čtení | Gets the list of operations. |
> | Akce | Microsoft. datacihly/registrovat/akce | Zaregistrujte se do datacihl. |
> | Akce | Microsoft. datacihly/pracovní prostory/odstranit | Odebere pracovní prostor datacihly. |
> | Akce | Microsoft. datacihly/pracovní prostory/poskytovatelé/Microsoft. Insights/diagnosticSettings/Read | Nastaví dostupná nastavení diagnostiky pro pracovní prostor datacihly. |
> | Akce | Microsoft. datacihly/pracovní prostory/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Akce | Microsoft. datacihly/pracovní prostory/poskytovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné definice protokolů pro pracovní prostor datacihly. |
> | Akce | Microsoft. datacihly/pracovní prostory/čtení | Načte seznam pracovních prostorů datacihlů. |
> | Akce | Microsoft. datacihly/pracovní prostory/refreshPermissions/Action | Aktualizace oprávnění pro pracovní prostor |
> | Akce | Microsoft. datacihly/pracovní prostory/updateDenyAssignment/Action | Aktualizace přiřazení zamítnutí akcí pro skupinu spravovaných prostředků pracovního prostoru |
> | Akce | Microsoft. datacihly/pracovní prostory/virtualNetworkPeerings/DELETE | Odstraní partnerský vztah virtuální sítě. |
> | Akce | Microsoft. datacihly/pracovní prostory/virtualNetworkPeerings/Read | Načte partnerský vztah virtuální sítě. |
> | Akce | Microsoft. datacihly/pracovní prostory/virtualNetworkPeerings/Write | Přidání nebo úprava partnerského vztahu virtuálních sítí |
> | Akce | Microsoft. datacihly/pracovní prostory/zápis | Vytvoří pracovní prostor datacihly. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataCatalog/catalogs/delete | Odstraní prostředek catalogs pro poskytovatele prostředků Data Catalog. |
> | Akce | Microsoft.DataCatalog/catalogs/read | Číst prostředky katalogu pro Data Catalog poskytovatele prostředků |
> | Akce | Microsoft.DataCatalog/catalogs/write | Zapsat prostředek katalogů pro Data Catalog poskytovatele prostředků |
> | Akce | Microsoft.DataCatalog/checkNameAvailability/read | Ověřte dostupnost názvu katalogu pro Data Catalog poskytovatele prostředků. |
> | Akce | Microsoft.DataCatalog/datacatalogs/delete | Odstraní prostředek datacatalog pro poskytovatele prostředků Data Catalog. |
> | Akce | Microsoft.DataCatalog/datacatalogs/read | Načte prostředek datacatalog pro poskytovatele prostředků Data Catalog. |
> | Akce | Microsoft.DataCatalog/datacatalogs/write | Zápis prostředku datacatalog pro poskytovatele prostředků Data Catalog. |
> | Akce | Microsoft.DataCatalog/operations/read | Přečte všechny dostupné operace v Data Catalog poskytovatel prostředků. |
> | Akce | Microsoft.DataCatalog/register/action | Registrace předplatného pro Data Catalog poskytovatele prostředků |
> | Akce | Microsoft.DataCatalog/unregister/action | Zrušení registrace předplatného pro Data Catalog poskytovatele prostředků |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Kontroluje, zda je název Data Factory k dispozici pro použití. |
> | Akce | Microsoft.DataFactory/datafactories/activitywindows/read | Přečte okna aktivity v Data Factory se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Přečte okna aktivity pro aktivitu kanálu se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Přečte okna aktivity pro kanál se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/delete | Odstraní všechny kanály. |
> | Akce | Microsoft. DataFactory/DataFactory/datapipelines/Pause/Action | Pozastaví všechny kanály. |
> | Akce | Microsoft. DataFactory/DataFactory/datapipelines/Read | Přečte libovolný kanál. |
> | Akce | Microsoft. DataFactory/DataFactory/Process/Resume/Action | Obnoví všechny kanály. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje všechny kanály. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/write | Vytvoří nebo aktualizuje libovolný kanál. |
> | Akce | Microsoft. DataFactory/DataFactory/activitywindows/Read | Přečte okna aktivity pro datovou sadu se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/delete | Odstraní libovolnou datovou sadu. |
> | Akce | Microsoft. DataFactory/DataFactory/DataSets/číst | Přečte libovolnou datovou sadu. |
> | Akce | Microsoft. DataFactory/DataFactory/sliceruns/Read | Přečte spuštění datového řezu pro danou datovou sadu s daným počátečním časem. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/slices/read | Načte datové řezy v daném období. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizujte stav datového řezu. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/write | Vytvoří nebo aktualizuje libovolnou datovou sadu. |
> | Akce | Microsoft.DataFactory/datafactories/delete | Odstraní Data Factory. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Přečte informace o připojení pro každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/delete | Odstraní libovolnou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Zobrazuje seznam ověřovacích klíčů pro každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/read | Přečte libovolnou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Znovu vygeneruje ověřovací klíče pro každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/write | Vytvoří nebo aktualizuje libovolnou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/delete | Odstraní veškerou propojenou službu. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/read | Přečte jakoukoli propojenou službu. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/write | Vytvoří nebo aktualizuje jakoukoli propojenou službu. |
> | Akce | Microsoft.DataFactory/datafactories/read | Přečte Data Factory. |
> | Akce | Microsoft.DataFactory/datafactories/runs/loginfo/read | Přečte identifikátor URI SAS pro kontejner objektů BLOB obsahující protokoly. |
> | Akce | Microsoft.DataFactory/datafactories/tables/delete | Odstraní libovolnou datovou sadu. |
> | Akce | Microsoft.DataFactory/datafactories/tables/read | Přečte libovolnou datovou sadu. |
> | Akce | Microsoft.DataFactory/datafactories/tables/write | Vytvoří nebo aktualizuje libovolnou datovou sadu. |
> | Akce | Microsoft.DataFactory/datafactories/write | Vytvoří nebo aktualizuje Data Factory. |
> | Akce | Microsoft. DataFactory/Factory/addDataFlowToDebugSession/Action | Přidejte tok dat do relace ladění pro verzi Preview. |
> | Akce | Microsoft. DataFactory/Factory/cancelpipelinerun/Action | Zruší běh kanálu určený IDENTIFIKÁTORem spuštění. |
> | Akce | Microsoft. DataFactory/Factory/cancelSandboxPipelineRun/Action | Zruší ladicí běh kanálu. |
> | Akce | Microsoft. DataFactory/Factory/createdataflowdebugsession/Action | Vytvoří relaci ladění toku dat. |
> | Akce | Microsoft.DataFactory/factories/dataflows/delete | Odstraní tok dat. |
> | Akce | Microsoft.DataFactory/factories/dataflows/read | Přečte tok dat. |
> | Akce | Microsoft.DataFactory/factories/dataflows/write | Vytvořit nebo aktualizovat tok dat |
> | Akce | Microsoft.DataFactory/factories/datasets/delete | Odstraní libovolnou datovou sadu. |
> | Akce | Microsoft.DataFactory/factories/datasets/read | Přečte libovolnou datovou sadu. |
> | Akce | Microsoft.DataFactory/factories/datasets/write | Vytvoří nebo aktualizuje libovolnou datovou sadu. |
> | Akce | Microsoft. DataFactory/Factory/debugpipelineruns/Cancel/Action | Zruší ladicí běh kanálu. |
> | Akce | Microsoft.DataFactory/factories/delete | Odstraní Data Factory. |
> | Akce | Microsoft. DataFactory/Factory/deletedataflowdebugsession/Action | Odstraní relaci ladění toku dat. |
> | Akce | Microsoft. DataFactory/Factory/executeDataFlowDebugCommand/Action | Spusťte příkaz pro ladění toku dat. |
> | Akce | Microsoft. DataFactory/Factory/getDataPlaneAccess/Action | Získá přístup k službě ADF (dataplaning Service). |
> | Akce | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Přečte přístup k službě ADF (dataplaning Service). |
> | Akce | Microsoft. DataFactory/Factory/getFeatureValue/Action | Get exposure control feature value for the specific location. |
> | Akce | Microsoft.DataFactory/factories/getFeatureValue/read | Reads exposure control feature value for the specific location. |
> | Akce | Microsoft. DataFactory/Factory/getGitHubAccessToken/Action | Získá přístupový token GitHubu. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/delete | Odstraní všechny Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Přečte Integration Runtime informace o připojení. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Získá metadata SSIS Integration Runtime pro zadaný Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Přečte stav Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Vytvoří odkaz na propojenou Integration Runtime v zadaném sdíleném Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Zobrazí seznam ověřovacích klíčů pro všechny Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Načte data monitorování pro všechny Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Odstraní uzel pro zadaný Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Vrátí IP adresu pro zadaný uzel Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Přečte uzel pro zadaný Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje uzel Integration Runtime v místním prostředí. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/read | Přečte všechny Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Aktualizuje SSIS metadata Integration Runtime pro zadaný Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Znovu vygeneruje ověřovací klíče pro zadaný Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Odebere propojené Integration Runtime odkazy ze zadaného Integration Runtime. |
> | Akce | Microsoft. DataFactory/Factory/integrationruntimes/Start/Action | Spustí všechny Integration Runtime. |
> | Akce | Microsoft. DataFactory/Factory/integrationruntimes/stop/Action | Zastaví všechny Integration Runtime. |
> | Akce | Microsoft. DataFactory/Factory/integrationruntimes/synccredentials/Action | Synchronizuje pověření pro zadaný Integration Runtime. |
> | Akce | Microsoft. DataFactory/Factory/integrationruntimes/upgrade/Action | Upgraduje zadaný Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/write | Vytvoří nebo aktualizuje jakékoli Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/delete | Odstraní propojenou službu. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/read | Načte propojenou službu. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/write | Vytvoří nebo aktualizuje propojenou službu. |
> | Akce | Microsoft. DataFactory/Factory/operationResults/Read | Získá výsledky operace. |
> | Akce | Microsoft. DataFactory/Factory/pipelineruns/activityruns/Read | Přečte spuštění aktivit pro zadané ID spuštění kanálu. |
> | Akce | Microsoft. DataFactory/Factory/pipelineruns/Cancel/Action | Zruší běh kanálu určený IDENTIFIKÁTORem spuštění. |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Dotazuje spuštění aktivity pro zadané ID spuštění kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Přečte výsledek spuštění aktivity dotazu pro zadané ID spuštění kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/read | Přečte spuštění kanálu. |
> | Akce | Microsoft. DataFactory/factorys/Pipelines/createrun/Action | Vytvoří běh pro kanál. |
> | Akce | Microsoft.DataFactory/factories/pipelines/delete | Odstraní kanál. |
> | Akce | Microsoft. DataFactory/factorys/Pipelines/pipelineruns/activityruns/průběh/čtení | Gets the Progress of Activity Runs. |
> | Akce | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Reads the Pipeline Run. |
> | Akce | Microsoft.DataFactory/factories/pipelines/read | Reads Pipeline. |
> | Akce | Microsoft.DataFactory/factories/pipelines/sandbox/action | Creates a debug run environment for the Pipeline. |
> | Akce | Microsoft.DataFactory/factories/pipelines/sandbox/create/action | Creates a debug run environment for the Pipeline. |
> | Akce | Microsoft.DataFactory/factories/pipelines/sandbox/run/action | Creates a debug run for the Pipeline. |
> | Akce | Microsoft.DataFactory/factories/pipelines/write | Create or Update Pipeline |
> | Akce | Microsoft.DataFactory/factories/querydataflowdebugsessions/action | Queries a Data Flow debug session. |
> | Akce | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Queries the Debug Pipeline Runs. |
> | Akce | Microsoft.DataFactory/factories/querypipelineruns/action | Queries the Pipeline Runs. |
> | Akce | Microsoft.DataFactory/factories/querypipelineruns/read | Reads the Result of Query Pipeline Runs. |
> | Akce | Microsoft.DataFactory/factories/querytriggerruns/action | Queries the Trigger Runs. |
> | Akce | Microsoft.DataFactory/factories/querytriggerruns/read | Reads the Result of Trigger Runs. |
> | Akce | Microsoft.DataFactory/factories/read | Reads Data Factory. |
> | Akce | Microsoft.DataFactory/factories/sandboxpipelineruns/action | Queries the Debug Pipeline Runs. |
> | Akce | Microsoft.DataFactory/factories/sandboxpipelineruns/read | Gets the debug run info for the Pipeline. |
> | Akce | Microsoft.DataFactory/factories/sandboxpipelineruns/sandboxActivityRuns/read | Gets the debug run info for the Activity. |
> | Akce | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Starts a Data Flow debug session. |
> | Akce | Microsoft.DataFactory/factories/triggerruns/read | Reads the Trigger Runs. |
> | Akce | Microsoft.DataFactory/factories/triggers/delete | Deletes any Trigger. |
> | Akce | Microsoft.DataFactory/factories/triggers/geteventsubscriptionstatus/action | Event Subscription Status. |
> | Akce | Microsoft.DataFactory/factories/triggers/read | Reads any Trigger. |
> | Akce | Microsoft.DataFactory/factories/triggers/start/action | Starts any Trigger. |
> | Akce | Microsoft.DataFactory/factories/triggers/stop/action | Stops any Trigger. |
> | Akce | Microsoft.DataFactory/factories/triggers/subscribetoevents/action | Subscribe to Events. |
> | Akce | Microsoft.DataFactory/factories/triggers/triggerruns/read | Reads the Trigger Runs. |
> | Akce | Microsoft.DataFactory/factories/triggers/unsubscribefromevents/action | Unsubscribe from Events. |
> | Akce | Microsoft.DataFactory/factories/triggers/write | Creates or Updates any Trigger. |
> | Akce | Microsoft.DataFactory/factories/write | Create or Update Data Factory |
> | Akce | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configures the repository for the factory. |
> | Akce | Microsoft.DataFactory/locations/getFeatureValue/action | Get exposure control feature value for the specific location. |
> | Akce | Microsoft.DataFactory/locations/getFeatureValue/read | Reads exposure control feature value for the specific location. |
> | Akce | Microsoft.DataFactory/operations/read | Reads all Operations in Microsoft Data Factory Provider. |
> | Akce | Microsoft.DataFactory/register/action | Registers the subscription for the Data Factory Resource Provider. |
> | Akce | Microsoft.DataFactory/unregister/action | Unregisters the subscription for the Data Factory Resource Provider. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Delete a compute policy. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Get information about a compute policy. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Create or update a compute policy. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Unlink a DataLakeStore account from a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Get information about a linked DataLakeStore account of a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Create or update a linked DataLakeStore account of a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/delete | Delete a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Delete a firewall rule. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Get information about a firewall rule. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Create or update a firewall rule. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Get result of a DataLakeAnalytics account operation. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/read | Get information about an existing DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | List SAS tokens for storage containers of a linked Storage account of a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Get containers of a linked Storage account of a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Unlink a Storage account from a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Get information about a linked Storage account of a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Create or update a linked Storage account of a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Grant permissions to cancel jobs submitted by other users. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transfer SystemMaxAnalyticsUnits among DataLakeAnalytics accounts. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Odstraní pravidlo virtuální sítě. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Získat informace o pravidle virtuální sítě. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Vytvoří nebo aktualizuje pravidlo virtuální sítě. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/write | Create or update a DataLakeAnalytics account. |
> | Akce | Microsoft.DataLakeAnalytics/locations/capability/read | Get capability information of a subscription regarding using DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Check availability of a DataLakeAnalytics account name. |
> | Akce | Microsoft.DataLakeAnalytics/locations/operationResults/read | Get result of a DataLakeAnalytics account operation. |
> | Akce | Microsoft.DataLakeAnalytics/locations/usages/read | Get quota usages information of a subscription regarding using DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/operations/read | Get available operations of DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/register/action | Register subscription to DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataLakeStore/accounts/delete | Delete a DataLakeStore account. |
> | Akce | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Enable KeyVault for a DataLakeStore account. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Delete an EventGrid Filter. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Get an EventGrid Filter. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Create or update an EventGrid Filter. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/delete | Delete a firewall rule. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/read | Get information about a firewall rule. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/write | Create or update a firewall rule. |
> | Akce | Microsoft.DataLakeStore/accounts/operationResults/read | Získejte výsledek operace DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/accounts/read | Get information about an existing DataLakeStore account. |
> | Akce | Microsoft.DataLakeStore/accounts/shares/delete | Delete a share. |
> | Akce | Microsoft.DataLakeStore/accounts/shares/read | Get information about a share. |
> | Akce | Microsoft.DataLakeStore/accounts/shares/write | Create or update a share. |
> | Akce | Microsoft.DataLakeStore/accounts/Superuser/action | Udělte uživateli oprávnění na Data Lake Store, pokud je udělený pomocí Microsoft. Authorization/roleAssignments/Write. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Odstraňte důvěryhodného zprostředkovatele identity. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Získejte informace o důvěryhodném zprostředkovateli identity. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Vytvořte nebo aktualizujte důvěryhodného zprostředkovatele identity. |
> | Akce | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Odstraní pravidlo virtuální sítě. |
> | Akce | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Získat informace o pravidle virtuální sítě. |
> | Akce | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Vytvoří nebo aktualizuje pravidlo virtuální sítě. |
> | Akce | Microsoft.DataLakeStore/accounts/write | Vytvořte nebo aktualizujte účet DataLakeStore. |
> | Akce | Microsoft. DataLakeStore/umístění/funkce/číst | Získejte informace o funkcích předplatného, které se týkají používání DataLakeStore. |
> | Akce | Microsoft. DataLakeStore/Locations/checkNameAvailability/Action | Ověřte dostupnost názvu účtu DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/operationResults/read | Získejte výsledek operace DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/locations/usages/read | Získat informace o využití kvóty pro předplatné týkající se použití DataLakeStore |
> | Akce | Microsoft.DataLakeStore/operations/read | Získejte dostupné operace DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/register/action | Zaregistrujte předplatné do DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. datamigration/Locations/operationResults/Read | Získání stavu dlouhotrvající operace související s přijatou odpovědí 202 |
> | Akce | Microsoft.DataMigration/locations/operationStatuses/read | Získání stavu dlouhotrvající operace související s přijatou odpovědí 202 |
> | Akce | Microsoft. datamigrace/registrace/akce | Zaregistruje předplatné u poskytovatele Azure Database Migration Service. |
> | Akce | Microsoft. datamigration/Services/addWorker/Action | Přidá pracovníka DMS do dispozici není žádná pracovníků služby. |
> | Akce | Microsoft. datamigration/Services/CheckStatus/Action | Ověřte, zda je služba nasazená a spuštěná. |
> | Akce | Microsoft. datamigration/Services/configureWorker/Action | Nakonfiguruje pracovní proces DMS na dispozici není žádná pracovní procesy služby. |
> | Akce | Migrace a služby Microsoft. DataServices/DELETE | Odstraní prostředek a všechny jeho podřízené položky. |
> | Akce | Microsoft. datamigration/Services/Projects/accessArtifacts/Action | Vygeneruje adresu URL, která se dá použít k získání nebo vložení artefaktů projektu. |
> | Akce | Microsoft.DataMigration/services/projects/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Akce | Microsoft.DataMigration/services/projects/files/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Akce | Microsoft.DataMigration/services/projects/files/read | Přečtěte si informace o prostředcích |
> | Akce | Migrace Microsoft. DataServices/služby/projekty/soubory/čtení/akce | Získat adresu URL, která se dá použít ke čtení obsahu souboru |
> | Akce | Migrace Microsoft. DataServices/služby/projekty/soubory/čtení a akce | Získat adresu URL, kterou lze použít ke čtení nebo zápisu obsahu souboru |
> | Akce | Microsoft.DataMigration/services/projects/files/write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Akce | Migrace Microsoft. DataServices/služby/projekty/čtení | Přečtěte si informace o prostředcích |
> | Akce | Microsoft. datamigrace/služby/projekty/úkoly/zrušit/akce | Zrušit úlohu, pokud je aktuálně spuštěna |
> | Akce | Microsoft.DataMigration/services/projects/tasks/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Akce | Microsoft.DataMigration/services/projects/tasks/read | Přečtěte si informace o prostředcích |
> | Akce | Microsoft.DataMigration/services/projects/tasks/write | Spouštění úloh Azure Database Migration Service úlohami |
> | Akce | Microsoft.DataMigration/services/projects/write | Spouštění úloh Azure Database Migration Service úlohami |
> | Akce | Migrace Microsoft. DataServices/služby/čtení | Přečtěte si informace o prostředcích |
> | Akce | Microsoft. datamigration/Services/removeWorker/Action | Odebere pracovní proces DMS na dispozici není žádná pracovní procesy služby. |
> | Akce | Microsoft. datamigration/Services/serviceTasks/Cancel/Action | Zrušit úlohu, pokud je aktuálně spuštěna |
> | Akce | Microsoft.DataMigration/services/serviceTasks/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Akce | Microsoft. datamigration/Services/serviceTasks/Read | Přečtěte si informace o prostředcích |
> | Akce | Microsoft. datamigration/Services/serviceTasks/Write | Spouštění úloh Azure Database Migration Service úlohami |
> | Akce | Microsoft.DataMigration/services/slots/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Akce | Migrace Microsoft. DataServices/služby/sloty/čtení | Přečtěte si informace o prostředcích |
> | Akce | Migrace Microsoft. DataServices/služby/sloty/zápis | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Akce | Microsoft. datamigration/Services/Start/Action | Spusťte službu DMS, aby mohla znovu zpracovat migrace. |
> | Akce | Migrace Microsoft. DataServices/služby/zastavení/akce | Zastavením služby DMS minimalizujete své náklady. |
> | Akce | Microsoft. datamigration/Services/updateAgentConfig/Action | Aktualizuje konfiguraci agenta DMS pomocí zadaných hodnot. |
> | Akce | Migrace Microsoft. DataServices/služby/Write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Akce | Migrace Microsoft. datasku/SKU/Read | Získejte seznam SKU podporovaných prostředky DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. DBforMariaDB/checkNameAvailability/Action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Akce | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Vrátí výsledky operace MariaDB serveru. |
> | Akce | Microsoft. DBforMariaDB/Locations/operationResults/Read | Vrátí výsledky operace serveru MariaDB založené na zdroji dat. |
> | Akce | Microsoft. DBforMariaDB/Locations/operationResults/Read | Vrátí výsledky operace MariaDB serveru. |
> | Akce | Microsoft.DBforMariaDB/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionOperationResults/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyOperationResults/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Akce | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Akce | Microsoft. DBforMariaDB/Locations/serverKeyAzureAsyncOperation/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft. DBforMariaDB/Locations/serverKeyOperationResults/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft.DBforMariaDB/operations/read | Vrátí seznam operací MariaDB. |
> | Akce | Microsoft. DBforMariaDB/performanceTiers/Read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft. DBforMariaDB/Register/Action | Zaregistrovat poskytovatele prostředků MariaDB |
> | Akce | Microsoft. DBforMariaDB/servery/Administrators/DELETE | Odstraní existujícího správce serveru MariaDB. |
> | Akce | Microsoft. DBforMariaDB/servery/správci/číst | Načte seznam správců serveru MariaDB. |
> | Akce | Microsoft. DBforMariaDB/servery/správci/zapisovat | Vytvoří nebo aktualizuje správce serveru MariaDB se zadanými parametry. |
> | Akce | Microsoft. DBforMariaDB/servery/poradci/createRecommendedActionSession/Action | Vytvořit novou relaci akce doporučení |
> | Akce | Microsoft. DBforMariaDB/servery/poradci/číst | Vrátí seznam poradců. |
> | Akce | Microsoft. DBforMariaDB/servery/poradci/číst | Vrácení poradce |
> | Akce | Microsoft. DBforMariaDB/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Akce | Microsoft. DBforMariaDB/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Akce | Microsoft. DBforMariaDB/servery/poradci/recommendedActions/číst | Vrácení doporučené akce |
> | Akce | Microsoft.DBforMariaDB/servers/configurations/read | Return the list of configurations for a server or gets the properties for the specified configuration. |
> | Akce | Microsoft.DBforMariaDB/servers/configurations/write | Update the value for the specified configuration |
> | Akce | Microsoft.DBforMariaDB/servers/databases/delete | Odstraní existující databázi MariaDB. |
> | Akce | Microsoft.DBforMariaDB/servers/databases/read | Vrátí seznam databází MariaDB nebo získá vlastnosti zadané databáze. |
> | Akce | Microsoft.DBforMariaDB/servers/databases/write | Vytvoří databázi MariaDB se zadanými parametry nebo aktualizuje vlastnosti zadané databáze. |
> | Akce | Microsoft.DBforMariaDB/servers/delete | Odstraní existující server. |
> | Akce | Microsoft. DBforMariaDB/servery/firewallRules/DELETE | Deletes an existing firewall rule. |
> | Akce | Microsoft. DBforMariaDB/servery/firewallRules/Read | Return the list of firewall rules for a server or gets the properties for the specified firewall rule. |
> | Akce | Microsoft.DBforMariaDB/servers/firewallRules/write | Creates a firewall rule with the specified parameters or update an existing rule. |
> | Akce | Microsoft. DBforMariaDB/servery/klíče/odstranit | Odstraní existující klíč serveru. |
> | Akce | Microsoft. DBforMariaDB/servery/klíče/číst | Vrátí seznam klíčů serveru nebo získá vlastnosti pro zadaný klíč serveru. |
> | Akce | Microsoft. DBforMariaDB/servery/klíče/zapisovat | Vytvoří klíč se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný klíč serveru. |
> | Akce | Microsoft.DBforMariaDB/servers/logFiles/read | Vrátí seznam protokolů MariaDB. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnectionProxies/DELETE | Odstraní existující proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnectionProxies/Read | Vrátí seznam proxy serverů připojení privátního koncového bodu nebo získá vlastnosti zadaného proxy serveru připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnectionProxies/ověřit/akci | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnectionProxies/Write | Vytvoří proxy server připojení privátního koncového bodu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný proxy privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnections/DELETE | Odstraní existující připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnections/Read | Vrátí seznam připojení privátního koncového bodu nebo získá vlastnosti pro zadané připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/servery/privateEndpointConnections/Write | Schválí nebo odmítne existující připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMariaDB/servery/privateLinkResources/Read | Získat prostředky privátního propojení pro odpovídající server MariaDB |
> | Akce | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Gets the disagnostic setting for the resource |
> | Akce | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. DBforMariaDB/servery/zprostředkovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro MariaDB servery. |
> | Akce | Microsoft. DBforMariaDB/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforMariaDB/servers/queryTexts/action | Return the texts for a list of queries |
> | Akce | Microsoft.DBforMariaDB/servers/queryTexts/action | Return the text of a query |
> | Akce | Microsoft.DBforMariaDB/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforMariaDB/servers/recoverableServers/read | Vrátit informace o obnovitelném serveru MariaDB |
> | Akce | Microsoft.DBforMariaDB/servers/replicas/read | Získání replik pro čtení serveru MariaDB |
> | Akce | Microsoft. DBforMariaDB/servery/restartovat/akce | Restarts a specific server. |
> | Akce | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Retrieve details of the server threat detection policy configured on a given server |
> | Akce | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Akce | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Return the list of Query Statistics for the top queries. |
> | Akce | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Vrátí statistiku dotazu. |
> | Akce | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Update configurations for the specified server |
> | Akce | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Odstraní existující pravidlo Virtual Network. |
> | Akce | Microsoft. DBforMariaDB/servery/virtualNetworkRules/Read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Akce | Microsoft. DBforMariaDB/servery/virtualNetworkRules/Write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Akce | Microsoft.DBforMariaDB/servers/waitStatistics/read | Return wait statistics for an instance |
> | Akce | Microsoft.DBforMariaDB/servers/waitStatistics/read | Vrácení statistiky čekání |
> | Akce | Microsoft.DBforMariaDB/servers/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. DBforMySQL/checkNameAvailability/Action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Akce | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Vrátit výsledky operace serveru MySQL |
> | Akce | Microsoft.DBforMySQL/locations/operationResults/read | Vrátit výsledky operace serveru MySQL založené na zdroji |
> | Akce | Microsoft.DBforMySQL/locations/operationResults/read | Vrátit výsledky operace serveru MySQL |
> | Akce | Microsoft.DBforMySQL/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft. DBforMySQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/Locations/privateEndpointConnectionOperationResults/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Akce | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Akce | Microsoft. DBforMySQL/Locations/serverKeyAzureAsyncOperation/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft. DBforMySQL/Locations/serverKeyOperationResults/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft.DBforMySQL/operations/read | Vrátí seznam operací MySQL. |
> | Akce | Microsoft.DBforMySQL/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMySQL/register/action | Registrovat poskytovatele prostředků MySQL |
> | Akce | Microsoft. DBforMySQL/servery/Administrators/DELETE | Odstraní existujícího správce serveru MySQL. |
> | Akce | Microsoft. DBforMySQL/servery/správci/číst | Načte seznam správců serveru MySQL. |
> | Akce | Microsoft. DBforMySQL/servery/správci/zapisovat | Vytvoří nebo aktualizuje správce serveru MySQL pomocí zadaných parametrů. |
> | Akce | Microsoft. DBforMySQL/servery/poradci/createRecommendedActionSession/Action | Vytvořit novou relaci akce doporučení |
> | Akce | Microsoft. DBforMySQL/servery/poradci/číst | Vrátí seznam poradců. |
> | Akce | Microsoft. DBforMySQL/servery/poradci/číst | Vrácení poradce |
> | Akce | Microsoft. DBforMySQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Akce | Microsoft. DBforMySQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Akce | Microsoft. DBforMySQL/servery/poradci/recommendedActions/číst | Vrácení doporučené akce |
> | Akce | Microsoft. DBforMySQL/servery/konfigurace/číst | Return the list of configurations for a server or gets the properties for the specified configuration. |
> | Akce | Microsoft.DBforMySQL/servers/configurations/write | Update the value for the specified configuration |
> | Akce | Microsoft.DBforMySQL/servers/databases/delete | Odstraní existující databázi MySQL. |
> | Akce | Microsoft.DBforMySQL/servers/databases/read | Vrátí seznam databází MySQL nebo získá vlastnosti zadané databáze. |
> | Akce | Microsoft.DBforMySQL/servers/databases/write | Vytvoří databázi MySQL se zadanými parametry nebo aktualizuje vlastnosti zadané databáze. |
> | Akce | Microsoft.DBforMySQL/servers/delete | Odstraní existující server. |
> | Akce | Microsoft. DBforMySQL/servery/firewallRules/DELETE | Deletes an existing firewall rule. |
> | Akce | Microsoft. DBforMySQL/servery/firewallRules/Read | Return the list of firewall rules for a server or gets the properties for the specified firewall rule. |
> | Akce | Microsoft. DBforMySQL/servery/firewallRules/Write | Creates a firewall rule with the specified parameters or update an existing rule. |
> | Akce | Microsoft. DBforMySQL/servery/klíče/odstranit | Odstraní existující klíč serveru. |
> | Akce | Microsoft. DBforMySQL/servery/klíče/číst | Vrátí seznam klíčů serveru nebo získá vlastnosti pro zadaný klíč serveru. |
> | Akce | Microsoft. DBforMySQL/servery/klíče/zapisovat | Vytvoří klíč se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný klíč serveru. |
> | Akce | Microsoft.DBforMySQL/servers/logFiles/read | Vrátí seznam protokolů MySQL. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnectionProxies/DELETE | Odstraní existující proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnectionProxies/Read | Vrátí seznam proxy serverů připojení privátního koncového bodu nebo získá vlastnosti zadaného proxy serveru připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnectionProxies/ověřit/akci | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnectionProxies/Write | Vytvoří proxy server připojení privátního koncového bodu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný proxy privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnections/DELETE | Odstraní existující připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnections/Read | Vrátí seznam připojení privátního koncového bodu nebo získá vlastnosti pro zadané připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/servery/privateEndpointConnections/Write | Schválí nebo odmítne existující připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforMySQL/servery/privateLinkResources/Read | Získat prostředky privátního propojení pro odpovídající server MySQL |
> | Akce | Microsoft. DBforMySQL/servery/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Gets the disagnostic setting for the resource |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. DBforMySQL/servery/zprostředkovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro servery MySQL. |
> | Akce | Microsoft. DBforMySQL/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforMySQL/servers/queryTexts/action | Return the texts for a list of queries |
> | Akce | Microsoft.DBforMySQL/servers/queryTexts/action | Return the text of a query |
> | Akce | Microsoft.DBforMySQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforMySQL/servers/recoverableServers/read | Vrátí informace o obnovitelném serveru MySQL. |
> | Akce | Microsoft. DBforMySQL/servery/repliky/číst | Získání replik pro čtení serveru MySQL |
> | Akce | Microsoft. DBforMySQL/servery/restartovat/akce | Restarts a specific server. |
> | Akce | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Retrieve details of the server threat detection policy configured on a given server |
> | Akce | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Akce | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Return the list of Query Statistics for the top queries. |
> | Akce | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Vrátí statistiku dotazu. |
> | Akce | Microsoft. DBforMySQL/servery/updateConfigurations/Action | Update configurations for the specified server |
> | Akce | Microsoft. DBforMySQL/servery/virtualNetworkRules/DELETE | Odstraní existující pravidlo Virtual Network. |
> | Akce | Microsoft. DBforMySQL/servery/virtualNetworkRules/Read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Akce | Microsoft. DBforMySQL/servery/virtualNetworkRules/Write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Akce | Microsoft. DBforMySQL/servery/waitStatistics/Read | Return wait statistics for an instance |
> | Akce | Microsoft. DBforMySQL/servery/waitStatistics/Read | Vrácení statistiky čekání |
> | Akce | Microsoft.DBforMySQL/servers/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Akce | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Vrátí výsledky operace PostgreSQL serveru. |
> | Akce | Microsoft.DBforPostgreSQL/locations/operationResults/read | Vrátí výsledky operace serveru PostgreSQL založené na zdroji dat. |
> | Akce | Microsoft.DBforPostgreSQL/locations/operationResults/read | Vrátí výsledky operace PostgreSQL serveru. |
> | Akce | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Akce | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Akce | Microsoft. DBforPostgreSQL/Locations/serverKeyAzureAsyncOperation/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft. DBforPostgreSQL/Locations/serverKeyOperationResults/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft.DBforPostgreSQL/operations/read | Vrátí seznam operací PostgreSQL. |
> | Akce | Microsoft.DBforPostgreSQL/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforPostgreSQL/register/action | Zaregistrovat poskytovatele prostředků PostgreSQL |
> | Akce | Microsoft. DBforPostgreSQL/servery/Administrators/DELETE | Odstraní existujícího správce serveru PostgreSQL. |
> | Akce | Microsoft. DBforPostgreSQL/servery/správci/číst | Načte seznam správců serveru PostgreSQL. |
> | Akce | Microsoft. DBforPostgreSQL/servery/správci/zapisovat | Vytvoří nebo aktualizuje správce serveru PostgreSQL se zadanými parametry. |
> | Akce | Microsoft.DBforPostgreSQL/servers/advisors/read | Vrátí seznam poradců. |
> | Akce | Microsoft. DBforPostgreSQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Akce | Microsoft. DBforPostgreSQL/servery/poradci/recommendedActionSessions/Action | Vytvořit doporučení |
> | Akce | Microsoft.DBforPostgreSQL/servers/configurations/read | Return the list of configurations for a server or gets the properties for the specified configuration. |
> | Akce | Microsoft.DBforPostgreSQL/servers/configurations/write | Update the value for the specified configuration |
> | Akce | Microsoft.DBforPostgreSQL/servers/databases/delete | Odstraní existující databázi PostgreSQL. |
> | Akce | Microsoft.DBforPostgreSQL/servers/databases/read | Vrátí seznam databází PostgreSQL nebo získá vlastnosti zadané databáze. |
> | Akce | Microsoft.DBforPostgreSQL/servers/databases/write | Vytvoří databázi PostgreSQL se zadanými parametry nebo aktualizuje vlastnosti zadané databáze. |
> | Akce | Microsoft.DBforPostgreSQL/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Deletes an existing firewall rule. |
> | Akce | Microsoft. DBforPostgreSQL/servery/firewallRules/Read | Return the list of firewall rules for a server or gets the properties for the specified firewall rule. |
> | Akce | Microsoft. DBforPostgreSQL/servery/firewallRules/Write | Creates a firewall rule with the specified parameters or update an existing rule. |
> | Akce | Microsoft. DBforPostgreSQL/servery/klíče/odstranit | Odstraní existující klíč serveru. |
> | Akce | Microsoft. DBforPostgreSQL/servery/klíče/číst | Vrátí seznam klíčů serveru nebo získá vlastnosti pro zadaný klíč serveru. |
> | Akce | Microsoft. DBforPostgreSQL/servery/klíče/zapisovat | Vytvoří klíč se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný klíč serveru. |
> | Akce | Microsoft. DBforPostgreSQL/servery/logFiles/Read | Vrátí seznam protokolů PostgreSQL. |
> | Akce | Microsoft. DBforPostgreSQL/servery/privateEndpointConnectionProxies/DELETE | Odstraní existující proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. DBforPostgreSQL/servery/privateEndpointConnectionProxies/Read | Vrátí seznam proxy serverů připojení privátního koncového bodu nebo získá vlastnosti zadaného proxy serveru připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Akce | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | Vytvoří proxy server připojení privátního koncového bodu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný proxy privátního koncového bodu. |
> | Akce | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/delete | Odstraní existující připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/read | Vrátí seznam připojení privátního koncového bodu nebo získá vlastnosti pro zadané připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/write | Schválí nebo odmítne existující připojení privátního koncového bodu. |
> | Akce | Microsoft.DBforPostgreSQL/servers/privateLinkResources/read | Get the private link resources for the corresponding PostgreSQL Server |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Gets the disagnostic setting for the resource |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for Postgres servers |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Return the text of a query |
> | Akce | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Return the texts for a list of queries |
> | Akce | Microsoft.DBforPostgreSQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Return the recoverable PostgreSQL Server info |
> | Akce | Microsoft.DBforPostgreSQL/servers/replicas/read | Get read replicas of a PostgreSQL server |
> | Akce | Microsoft.DBforPostgreSQL/servers/restart/action | Restarts a specific server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Retrieve details of the server threat detection policy configured on a given server |
> | Akce | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Akce | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Return the list of Query Statistics for the top queries. |
> | Akce | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Update configurations for the specified server |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Odstraní existující pravidlo Virtual Network. |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Akce | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Return wait statistics for an instance |
> | Akce | Microsoft.DBforPostgreSQL/servers/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Return the list of configurations for a server or gets the properties for the specified configuration. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Update the value for the specified configuration |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Deletes an existing firewall rule. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Return the list of firewall rules for a server or gets the properties for the specified firewall rule. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Creates a firewall rule with the specified parameters or update an existing rule. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Gets the disagnostic setting for the resource |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for Postgres servers |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Update configurations for the specified server |
> | Akce | Microsoft.DBforPostgreSQL/serversv2/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Devices/Account/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Devices/Account/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Devices/Account/logDefinitions/read | Gets the available log definitions for the IotHub Service |
> | Akce | Microsoft.Devices/Account/metricDefinitions/read | Gets the available metrics for the IotHub service |
> | Akce | Microsoft.Devices/checkNameAvailability/Action | Check If IotHub name is available |
> | Akce | Microsoft.Devices/checkNameAvailability/Action | Check If IotHub name is available |
> | Akce | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Check If Provisioning service name is available |
> | Akce | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Check If Provisioning service name is available |
> | Akce | Microsoft.Devices/digitalTwins/Delete | Delete an existing Digital Twins Account and all of its children |
> | Akce | Microsoft.Devices/digitalTwins/operationresults/Read | Get the status of an operation against a Digital Twins Account |
> | Akce | Microsoft. Devices/digitalTwins/Read | Načte seznam účtů digitálních vláken přidružených k předplatnému. |
> | Akce | Microsoft. Devices/digitalTwins/SKU/Read | Získat seznam platných SKU pro účty digitálních vláken |
> | Akce | Microsoft. Devices/digitalTwins/Write | Vytvořit nový účet s dvojitými podmnožinou čísel |
> | Akce | Microsoft. Devices/ElasticPools/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. Devices/ElasticPools/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. Devices/elasticPools/eventGridFilters/DELETE | Odstraní filtr Elastický fond Event Grid |
> | Akce | Microsoft. Devices/elasticPools/eventGridFilters/Read | Získá filtr Elastický fond Event Grid |
> | Akce | Microsoft. Devices/elasticPools/eventGridFilters/Write | Vytvořit nový nebo aktualizovat existující Elastický fond Event Grid filtr |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/DELETE | Deletes Certificate |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Generate Verification code |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Read | Gets the Certificate |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Verify/Action | Verify Certificate resource |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Write | Create or Update Certificate |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/DELETE | Odstranit prostředek tenanta IotHub |
> | Akce | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Odstranit skupinu uživatelů EventHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Získat skupiny uživatelů EventHub |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Vytvořit skupinu uživatelů EventHub |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportovat zařízení |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/GetStats/Read | Získá prostředek statistiky tenanta IotHub. |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Importovat zařízení |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/klíče listkey/Action | Získá klíč tenanta IotHub. |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/Read | Získat podrobnosti o úlohách odeslaných v daném IotHub |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/klíče listkey/Action | Načte klíče tenanta IotHub. |
> | Akce | Microsoft. Devices/ElasticPools/IotHubTenants/logDefinitions/Read | Gets the available log definitions for the IotHub Service |
> | Akce | Microsoft. Devices/ElasticPools/IotHubTenants/metricDefinitions/Read | Gets the available metrics for the IotHub service |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Get Quota Metrics |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Read | Získá prostředek klienta IotHub. |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Routing/Routes/$testall za akci | Test a message against all existing Routes |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Routing/Routes/$testnew za akci | Test a message against a provided test Route |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Gets the health of all routing Endpoints for an IotHub |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Získat výsledek asynchronní operace PUT pro centrum tenanta IoT SecuritySettings |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | Získání nastavení Azure Security Center na centru tenanta IoT |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | Aktualizace nastavení Azure Security Center na centru tenanta IoT |
> | Akce | Microsoft. Devices/elasticPools/iotHubTenants/Write | Vytvořit nebo aktualizovat prostředek klienta IotHub |
> | Akce | Microsoft. Devices/ElasticPools/metricDefinitions/Read | Gets the available metrics for the IotHub service |
> | Akce | Microsoft. Devices/iotHubs/Certificates/DELETE | Deletes Certificate |
> | Akce | Microsoft. Devices/iotHubs/Certificates/generateVerificationCode/Action | Generate Verification code |
> | Akce | Microsoft. Devices/iotHubs/Certificates/Read | Gets the Certificate |
> | Akce | Microsoft. Devices/iotHubs/Certificates/Verify/Action | Verify Certificate resource |
> | Akce | Microsoft. Devices/iotHubs/Certificates/Write | Create or Update Certificate |
> | Akce | Microsoft. Devices/iotHubs/DELETE | Odstranit prostředek IotHub |
> | Akce | Microsoft. Devices/IotHubs/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Odstraní filtr Event Grid. |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Read | Získá filtr Event Grid. |
> | Akce | Microsoft. Devices/iotHubs/eventGridFilters/Write | Vytvořit nový nebo aktualizovat existující filtr Event Grid |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Odstranit skupinu uživatelů EventHub |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Získat skupiny uživatelů EventHub |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Vytvořit skupinu uživatelů EventHub |
> | Akce | Microsoft. Devices/iotHubs/exportDevices/Action | Exportovat zařízení |
> | Akce | Microsoft. Devices/iotHubs/importDevices/Action | Importovat zařízení |
> | Akce | Microsoft. Devices/iotHubs/iotHubKeys/klíče listkey/Action | Získat IotHub klíč pro daný název |
> | Akce | Microsoft. Devices/iotHubs/iotHubStats/Read | Získat statistiku IotHub |
> | Akce | Microsoft. Devices/iotHubs/Jobs/Read | Získat podrobnosti o úlohách odeslaných v daném IotHub |
> | Akce | Microsoft. Devices/iotHubs/klíče listkey/Action | Získat všechny IotHub klíče |
> | Akce | Microsoft. Devices/IotHubs/logDefinitions/Read | Gets the available log definitions for the IotHub Service |
> | Akce | Microsoft.Devices/IotHubs/metricDefinitions/read | Gets the available metrics for the IotHub service |
> | Akce | Microsoft.Devices/iotHubs/operationresults/Read | Get Operation Result (Obsolete API) |
> | Akce | Microsoft.Devices/iotHubs/quotaMetrics/Read | Get Quota Metrics |
> | Akce | Microsoft.Devices/iotHubs/Read | Gets the IotHub resource(s) |
> | Akce | Microsoft.Devices/iotHubs/routing/$testall/Action | Test a message against all existing Routes |
> | Akce | Microsoft.Devices/iotHubs/routing/$testnew/Action | Test a message against a provided test Route |
> | Akce | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Gets the health of all routing Endpoints for an IotHub |
> | Akce | Microsoft.Devices/iotHubs/securitySettings/operationResults/Read | Get the result of the Async Put operation for Iot Hub SecuritySettings |
> | Akce | Microsoft.Devices/iotHubs/securitySettings/Read | Get the Azure Security Center settings on Iot Hub |
> | Akce | Microsoft.Devices/iotHubs/securitySettings/Write | Update the Azure Security Center settings on Iot Hub |
> | Akce | Microsoft.Devices/iotHubs/skus/Read | Get valid IotHub Skus |
> | Akce | Microsoft.Devices/iotHubs/Write | Create or update IotHub Resource |
> | Akce | Microsoft.Devices/locations/operationresults/Read | Get Location based Operation Result |
> | Akce | Microsoft.Devices/operationresults/Read | Get Operation Result |
> | Akce | Microsoft.Devices/operations/Read | Get All ResourceProvider Operations |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Delete | Deletes Certificate |
> | Akce | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generate Verification code |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Read | Gets the Certificate |
> | Akce | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verify Certificate resource |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Write | Create or Update Certificate |
> | Akce | Microsoft.Devices/provisioningServices/Delete | Delete IotDps resource |
> | Akce | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Get IotDps Keys for key name |
> | Akce | Microsoft.Devices/provisioningServices/listkeys/Action | Get all IotDps keys |
> | Akce | Microsoft.Devices/provisioningServices/logDefinitions/read | Gets the available log definitions for the provisioning Service |
> | Akce | Microsoft.Devices/provisioningServices/metricDefinitions/read | Gets the available metrics for the provisioning service |
> | Akce | Microsoft.Devices/provisioningServices/operationresults/Read | Get DPS Operation Result |
> | Akce | Microsoft.Devices/provisioningServices/Read | Get IotDps resource |
> | Akce | Microsoft.Devices/provisioningServices/skus/Read | Get valid IotDps Skus |
> | Akce | Microsoft.Devices/provisioningServices/Write | Create IotDps resource |
> | Akce | Microsoft.Devices/register/action | Register the subscription for the IotHub resource provider and enables the creation of IotHub resources |
> | Akce | Microsoft.Devices/usages/Read | Get subscription usage details for this provider. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DevSpaces/controllers/delete | Delete Azure Dev Spaces Controller and dataplane services |
> | Akce | Microsoft.DevSpaces/controllers/listConnectionDetails/action | List connection details for the Azure Dev Spaces Controller's infrastructure |
> | Akce | Microsoft.DevSpaces/controllers/read | Read Azure Dev Spaces Controller properties |
> | Akce | Microsoft.DevSpaces/controllers/write | Create or Update Azure Dev Spaces Controller properties |
> | Akce | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Check existing controller mapping for a container host |
> | Akce | Microsoft.DevSpaces/locations/operationresults/read | Read status for an asynchronous operation |
> | Akce | Microsoft.DevSpaces/register/action | Register Microsoft Dev Spaces resource provider with a subscription |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DevTestLab/labCenters/delete | Delete lab centers. |
> | Akce | Microsoft.DevTestLab/labCenters/read | Read lab centers. |
> | Akce | Microsoft.DevTestLab/labCenters/write | Add or modify lab centers. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Read azure resource manager templates. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generates an Azure Resource Manager template for the given artifact, uploads the required files to a storage account, and validates the generated artifact. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Read artifacts. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/delete | Delete artifact sources. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/read | Read artifact sources. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/write | Add or modify artifact sources. |
> | Akce | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Claim a random claimable virtual machine in the lab. |
> | Akce | Microsoft.DevTestLab/labs/costs/read | Read costs. |
> | Akce | Microsoft.DevTestLab/labs/costs/write | Add or modify costs. |
> | Akce | Microsoft.DevTestLab/labs/CreateEnvironment/action | Create virtual machines in a lab. |
> | Akce | Microsoft.DevTestLab/labs/customImages/delete | Delete custom images. |
> | Akce | Microsoft.DevTestLab/labs/customImages/read | Read custom images. |
> | Akce | Microsoft.DevTestLab/labs/customImages/write | Add or modify custom images. |
> | Akce | Microsoft.DevTestLab/labs/delete | Odstraňte Labs. |
> | Akce | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Ensure the current user has a valid profile in the lab. |
> | Akce | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exports the lab resource usage into a storage account |
> | Akce | Microsoft.DevTestLab/labs/formulas/delete | Delete formulas. |
> | Akce | Microsoft.DevTestLab/labs/formulas/read | Read formulas. |
> | Akce | Microsoft.DevTestLab/labs/formulas/write | Add or modify formulas. |
> | Akce | Microsoft.DevTestLab/labs/galleryImages/read | Čtení imagí galerie. |
> | Akce | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generate a URI for uploading custom disk images to a Lab. |
> | Akce | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Import a virtual machine into a different lab. |
> | Akce | Microsoft.DevTestLab/labs/ListVhds/action | List disk images available for custom image creation. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/delete | Delete notification channels. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Send notification to provided channel. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/read | Read notification channels. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/write | Add or modify notification channels. |
> | Akce | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Evaluates lab policy. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/delete | Delete policies. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/read | Read policies. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/write | Add or modify policies. |
> | Akce | Microsoft.DevTestLab/labs/policySets/read | Read policy sets. |
> | Akce | Microsoft.DevTestLab/labs/read | Read labs. |
> | Akce | Microsoft.DevTestLab/labs/schedules/delete | Delete schedules. |
> | Akce | Microsoft.DevTestLab/labs/schedules/Execute/action | Execute a schedule. |
> | Akce | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Lists all applicable schedules |
> | Akce | Microsoft.DevTestLab/labs/schedules/read | Read schedules. |
> | Akce | Microsoft.DevTestLab/labs/schedules/write | Add or modify schedules. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/delete | Delete service runners. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/read | Read service runners. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/write | Add or modify service runners. |
> | Akce | Microsoft.DevTestLab/labs/sharedGalleries/delete | Delete shared galleries. |
> | Akce | Microsoft.DevTestLab/labs/sharedGalleries/read | Read shared galleries. |
> | Akce | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Delete shared images. |
> | Akce | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Read shared images. |
> | Akce | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Add or modify shared images. |
> | Akce | Microsoft.DevTestLab/labs/sharedGalleries/write | Add or modify shared galleries. |
> | Akce | Microsoft.DevTestLab/labs/users/delete | Delete user profiles. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/Attach/action | Attach and create the lease of the disk to the virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/delete | Delete disks. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/Detach/action | Detach and break the lease of the disk attached to the virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/read | Read disks. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/write | Add or modify disks. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/read | Čtení prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/write | Add or modify environments. |
> | Akce | Microsoft.DevTestLab/labs/users/read | Read user profiles. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/delete | Delete secrets. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/read | Read secrets. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/write | Add or modify secrets. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Delete service fabrics. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lists the applicable start/stop schedules, if any. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Read service fabrics. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Delete schedules. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Execute a schedule. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Read schedules. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Add or modify schedules. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Start a service fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stop a service fabric |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Add or modify service fabrics. |
> | Akce | Microsoft.DevTestLab/labs/users/write | Add or modify user profiles. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Attach a new or existing data disk to virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Apply artifacts to virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Take ownership of an existing virtual machine |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ClearArtifactResults/action | Clears the artifact results of the virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/delete | Delete virtual machines. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Detach the specified disk from the virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Gets a string that represents the contents of the RDP file for the virtual machine |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lists the applicable start/stop schedules, if any. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/read | Read virtual machines. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Redeploy a virtual machine |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Resize Virtual Machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Restart a virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Delete schedules. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Execute a schedule. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Read schedules. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Add or modify schedules. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Start a virtual machine. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Stop a virtual machine |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transfers all data disks attached to the virtual machine to be owned by the current user. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Release ownership of an existing virtual machine |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/write | Add or modify virtual machines. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/delete | Delete bastionhosts. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/read | Read bastionhosts. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/write | Add or modify bastionhosts. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/delete | Delete virtual networks. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/read | Read virtual networks. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/write | Add or modify virtual networks. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/delete | Delete virtual machine pools. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/read | Read virtual machine pools. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/write | Add or modify virtual machine pools. |
> | Akce | Microsoft.DevTestLab/labs/write | Add or modify labs. |
> | Akce | Microsoft.DevTestLab/locations/operations/read | Read operations. |
> | Akce | Microsoft.DevTestLab/register/action | Registers the subscription |
> | Akce | Microsoft.DevTestLab/schedules/delete | Delete schedules. |
> | Akce | Microsoft.DevTestLab/schedules/Execute/action | Execute a schedule. |
> | Akce | Microsoft.DevTestLab/schedules/read | Read schedules. |
> | Akce | Microsoft.DevTestLab/schedules/Retarget/action | Updates a schedule's target resource Id. |
> | Akce | Microsoft.DevTestLab/schedules/write | Add or modify schedules. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DocumentDB/databaseAccountNames/read | Checks for name availability. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Delete a collection. Only applicable to API types: 'mongodb'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'mongodb'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Read a collection or list all the collections. Only applicable to API types: 'mongodb'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'mongodb'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Read a collection throughput. Only applicable to API types: 'mongodb'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Update a collection throughput. Only applicable to API types: 'mongodb'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Create or update a collection. Only applicable to API types: 'mongodb'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Delete a container. Only applicable to API types: 'sql'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'sql'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Read a container or list all the containers. Only applicable to API types: 'sql'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'sql'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Read a container throughput. Only applicable to API types: 'sql'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Update a container throughput. Only applicable to API types: 'sql'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Create or update a container. Only applicable to API types: 'sql'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Delete a database. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Delete a graph. Only applicable to API types: 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Read a graph or list all the graphs. Only applicable to API types: 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Read a graph throughput. Only applicable to API types: 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Update a graph throughput. Only applicable to API types: 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Create or update a graph. Only applicable to API types: 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Read a database or list all the databases. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Read a database throughput. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Update a database throughput. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Create a database. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Delete a keyspace. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Read a keyspace or list all the keyspaces. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Read a keyspace throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Update a keyspace throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Delete a table. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Read a table or list all the tables. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Read a table throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Update a table throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Create or update a table. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Create a keyspace. Only applicable to API types: 'cassandra'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Delete a table. Only applicable to API types: 'table'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'table'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Read a table or list all the tables. Only applicable to API types: 'table'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'table'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Read a table throughput. Only applicable to API types: 'table'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Update a table throughput. Only applicable to API types: 'table'. Only applicable for setting types: 'throughput'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Create or update a table. Only applicable to API types: 'table'. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/backup/action | Submit a request to configure backup |
> | Akce | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Change resource group of a database account |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Reads the collection metric definitions. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Reads the collection metrics. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Read database account partition key level metrics |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Read database account partition level metrics |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Read database account partitions in a collection |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Read database account partition level usages |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Reads the collection usages. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Reads the database metric definitions |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Reads the database metrics. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Reads the database usages. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/delete | Deletes the database accounts. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Change failover priorities of regions of a database account. This is used to perform manual failover operation |
> | Akce | Microsoft.DocumentDB/databaseAccounts/getBackupPolicy/action | Get the backup policy of database account |
> | Akce | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Get the connection strings for a database account |
> | Akce | Microsoft.DocumentDB/databaseAccounts/listKeys/action | List keys of a database account |
> | Akce | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Reads the database account metrics definitions. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/metrics/read | Reads the database account metrics. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline a region of a database account.  |
> | Akce | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online a region of a database account. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Stav čtení asynchronní operace |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Read latency metrics |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/read | Read percentiles of replication latencies |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Read latency metrics for a specific source and target region |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Read latency metrics for a specific target region |
> | Akce | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Delete a private endpoint connection proxy of Database Account |
> | Akce | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Stav čtení asynchronní operace proxy připojení privátního koncového bodu |
> | Akce | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Čtení proxy serveru připojení privátního koncového bodu k databázovému účtu |
> | Akce | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Ověření proxy serveru připojení privátního koncového bodu k databázovému účtu |
> | Akce | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Vytvoří nebo aktualizuje proxy serveru připojení privátního koncového bodu k databázovému účtu. |
> | Akce | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/DELETE | Odstranění připojení privátního koncového bodu databázového účtu |
> | Akce | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/Read | Stav čtení asynchronní operace privateEndpointConnenctions |
> | Akce | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Read | Přečtěte si připojení privátního koncového bodu nebo seznam všech připojení privátního koncového bodu databázového účtu. |
> | Akce | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Write | Vytvoří nebo aktualizuje připojení privátního koncového bodu databázového účtu. |
> | Akce | Microsoft. DocumentDB/databaseAccounts/privateLinkResources/Read | Přečtěte si prostředek privátního odkazu nebo seznam všech prostředků privátního propojení účtu databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/read | Přečte účet databáze. |
> | Akce | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Přečte klíče databázového účtu jen pro čtení. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Přečte klíče databázového účtu jen pro čtení. |
> | Akce | Microsoft. DocumentDB/databaseAccounts/regenerateKey/Action | Otočení klíčů databázového účtu |
> | Akce | Microsoft. DocumentDB/databaseAccounts/region/databáze/kolekce/metriky/čtení | Přečte místní metriky kolekce. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Číst metriky na úrovni klíče oddílu účtu místní databáze |
> | Akce | Microsoft. DocumentDB/databaseAccounts/region/databáze/kolekce/oddíly/metriky/čtení | Číst metriky na úrovni oddílu účtu místní databáze |
> | Akce | Microsoft. DocumentDB/databaseAccounts/region/databáze/kolekce/partitions/Read | Čtení oddílů účtu místní databáze v kolekci |
> | Akce | Microsoft. DocumentDB/databaseAccounts/region/metrika/číst | Přečte metriky oblasti a účtu databáze. |
> | Akce | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Odeslat žádost o obnovení |
> | Akce | Microsoft.DocumentDB/databaseAccounts/usages/read | Přečte využití databázového účtu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/write | Aktualizuje účty databáze. |
> | Akce | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoft. DocumentDB, že se odstraňuje VirtualNetwork nebo podsíť. |
> | Akce | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Stav čtení asynchronní operace deleteVirtualNetworkOrSubnets |
> | Akce | Microsoft.DocumentDB/locations/operationsStatus/read | Přečte stav asynchronních operací. |
> | Akce | Microsoft. DocumentDB/operationResults/Read | Stav čtení asynchronní operace |
> | Akce | Microsoft.DocumentDB/operations/read | Operace čtení dostupné pro Microsoft DocumentDB  |
> | Akce | Microsoft. DocumentDB/Register/Action |  Registrace poskytovatele prostředků Microsoft DocumentDB pro předplatné |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. DomainRegistration/checkDomainAvailability/Action | Zjistit, jestli je doména dostupná k nákupu |
> | Akce | Microsoft. DomainRegistration/domén/DELETE | Odstraní existující doménu. |
> | Akce | Microsoft. DomainRegistration/domény/domainownershipidentifiers/DELETE | Odstranit identifikátor vlastnictví |
> | Akce | Microsoft. DomainRegistration/doméns/domainownershipidentifiers/Read | Seznam identifikátorů vlastnictví |
> | Akce | Microsoft. DomainRegistration/doméns/domainownershipidentifiers/Read | Získat identifikátor vlastnictví |
> | Akce | Microsoft. DomainRegistration/domény/domainownershipidentifiers/Write | Vytvořit nebo aktualizovat identifikátor |
> | Akce | Microsoft. DomainRegistration/doméns/operationresults/Read | Získat doménovou operaci |
> | Akce | Microsoft. DomainRegistration/domény/číst | Získá seznam domén. |
> | Akce | Microsoft. DomainRegistration/domény/číst | Získat doménu |
> | Akce | Microsoft. DomainRegistration/domény/prodloužit/Action | Obnovte existující doménu. |
> | Akce | Microsoft. DomainRegistration/domén/Write | Přidat novou doménu nebo aktualizovat existující |
> | Akce | Microsoft. DomainRegistration/generateSsoRequest/Action | Vygenerujte žádost o přihlášení do centra řízení domény. |
> | Akce | Microsoft. DomainRegistration/listDomainRecommendations/Action | Načtěte doporučení k doméně seznamu na základě klíčových slov. |
> | Akce | Microsoft.DomainRegistration/operations/Read | Vypsat všechny operace z registrace domény App Service |
> | Akce | Microsoft. DomainRegistration/Register/Action | Registrace poskytovatele prostředků domény Microsoft pro předplatné |
> | Akce | Microsoft. DomainRegistration/topLevelDomains/listAgreements/Action | Akce výpisu smlouvy |
> | Akce | Microsoft. DomainRegistration/topLevelDomains/Read | Získat domény Toplevel |
> | Akce | Microsoft. DomainRegistration/topLevelDomains/Read | Získat doménu Toplevel |
> | Akce | Microsoft. DomainRegistration/validateDomainRegistrationInformation/Action | Ověřit objekt nákupu domény bez odeslání |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. EventGrid/domén/DELETE | Odstranění domény |
> | Akce | Microsoft. EventGrid/domény/klíče listkey/Action | Seznam klíčů pro doménu |
> | Akce | Microsoft. EventGrid/doméns/Providers/Microsoft. Insights/logDefinitions/Read | Povolit přístup k diagnostickým protokolům |
> | Akce | Microsoft. EventGrid/doméns/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro domény. |
> | Akce | Microsoft. EventGrid/domény/číst | Čtení domény |
> | Akce | Microsoft.EventGrid/domains/regenerateKey/action | Znovu vygenerovat klíč pro doménu |
> | Akce | Microsoft. EventGrid/domény/témata/odstranit | Téma odstranění domény |
> | Akce | Microsoft. EventGrid/domény/témata/číst | Přečíst téma domény |
> | Akce | Microsoft. EventGrid/domény/témata/zápis | Vytvořit nebo aktualizovat téma v doméně |
> | Akce | Microsoft. EventGrid/domén/Write | Vytvoření nebo aktualizace domény |
> | Akce | Microsoft.EventGrid/eventSubscriptions/delete | Odstranění eventSubscription |
> | Akce | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Získat úplnou adresu URL pro odběr události |
> | Akce | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro odběry událostí. |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro odběry událostí. |
> | Akce | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro eventSubscriptions. |
> | Akce | Microsoft. EventGrid/eventSubscriptions/Read | Přečíst eventSubscription |
> | Akce | Microsoft.EventGrid/eventSubscriptions/write | Vytvořit nebo aktualizovat eventSubscription |
> | Akce | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro témata. |
> | Akce | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata. |
> | Akce | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro témata. |
> | Akce | Microsoft.EventGrid/extensionTopics/read | Přečtěte si extensionTopic. |
> | Akce | Microsoft.EventGrid/locations/eventSubscriptions/read | Výpis odběrů místních událostí |
> | Akce | Microsoft.EventGrid/locations/operationResults/read | Přečtěte si výsledek regionální operace. |
> | Akce | Microsoft.EventGrid/locations/operationsStatus/read | Přečtěte si stav místní operace. |
> | Akce | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Výpis odběrů místních událostí podle TopicType |
> | Akce | Microsoft.EventGrid/operationResults/read | Přečtěte si výsledek operace. |
> | Akce | Microsoft.EventGrid/operations/read | Vypíše EventGrid operace. |
> | Akce | Microsoft.EventGrid/operationsStatus/read | Přečtěte si stav operace. |
> | Akce | Microsoft.EventGrid/register/action | Zaregistruje předplatné pro poskytovatele prostředků EventGrid. |
> | Akce | Microsoft. EventGrid/témata/DELETE | Odstranění tématu |
> | Akce | Microsoft.EventGrid/topics/listKeys/action | Seznam klíčů pro téma |
> | Akce | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro témata. |
> | Akce | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata. |
> | Akce | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/logDefinitions/Read | Povolit přístup k diagnostickým protokolům |
> | Akce | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro témata. |
> | Akce | Microsoft. EventGrid/témata/číst | Přečtěte si téma. |
> | Akce | Microsoft.EventGrid/topics/regenerateKey/action | Znovu vygenerovat klíč pro téma |
> | Akce | Microsoft.EventGrid/topics/write | Vytvoří nebo aktualizuje téma. |
> | Akce | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Vypsat globální odběry událostí podle typu tématu |
> | Akce | Microsoft.EventGrid/topictypes/eventtypes/read | Číst typ události podporovaný TopicType |
> | Akce | Microsoft. EventGrid/topictypes/Read | Přečíst TopicType |
> | Akce | Microsoft.EventGrid/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. EventHub/availableClusterRegions/Read | Operace čtení a výpisu dostupných předem zajištěných clusterů podle oblasti Azure. |
> | Akce | Microsoft. EventHub/checkNameAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. |
> | Akce | Microsoft. EventHub/checkNamespaceAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. Toto rozhraní API je zastaralé, použijte místo toho CheckNameAvailability. |
> | Akce | Microsoft. EventHub/clustery/odstranit | Odstraní existující prostředek clusteru. |
> | Akce | Microsoft. EventHub/clustery/obory názvů/číst | Vypíše obor názvů Azure Resource Manager ID pro obory názvů v rámci clusteru. |
> | Akce | Microsoft. EventHub/clustery/operationresults/Read | Získá stav asynchronní operace clusteru. |
> | Akce | Microsoft. EventHub/clustery/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik clusteru. |
> | Akce | Microsoft. EventHub/clustery/číst | Získá popis prostředku clusteru. |
> | Akce | Microsoft. EventHub/clustery/Write | Vytvoří nebo upraví existující prostředek clusteru. |
> | Akce | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla virtuální sítě v poskytovateli prostředků EventHub pro zadanou virtuální síť. |
> | Akce | Microsoft. EventHub/obory názvů/autorizačních pravidel/Action | Aktualizuje autorizační pravidlo oboru názvů. Toto rozhraní API je zastaralé. Místo toho prosím použijte volání PUT k aktualizaci autorizačního pravidla oboru názvů.. Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. EventHub/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Akce | Microsoft. EventHub/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Akce | Microsoft. EventHub/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Akce | Microsoft. EventHub/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. EventHub/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft. EventHub/obory názvů/odstranění | Odstranit prostředek oboru názvů |
> | Akce | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/klíče listkey/Action | Získá klíče autorizačních pravidel pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat autorizační pravidla primárního oboru názvů pro zotavení po havárii |
> | Akce | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/breakPairing/Action | Zakáže zotavení po havárii a zastaví replikaci změn z primárních oborů názvů na sekundární. |
> | Akce | Microsoft. EventHub/obory názvů/disasterrecoveryconfigs/checkNameAvailability/Action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/DELETE | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tuto operaci lze vyvolat pouze prostřednictvím primárního oboru názvů. |
> | Akce | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/převzetí služeb při selhání/akce | Vyvolá převzetí služeb při selhání GEOGRAFICKého zotavení po havárii a změní konfiguraci aliasu oboru názvů tak, aby odkazoval na sekundární obor názvů. |
> | Akce | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/Read | Získá konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Akce | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/Write | Vytvoří nebo aktualizuje konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/Action | Operace aktualizace EventHub Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel EventHub |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/klíče listkey/Action | Získá připojovací řetězec k centru EventHub. |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/Read |  Získat seznam autorizačních pravidel EventHub |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/Write | Vytvořte autorizační pravidla EventHub a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Akce | Microsoft. EventHub/obory názvů/eventHubs/consumergroups/DELETE | Operace odstranění prostředku uživatele |
> | Akce | Microsoft. EventHub/obory názvů/eventHubs/consumergroups/Read | Získá seznam popisů prostředků uživatele. |
> | Akce | Microsoft. EventHub/obory názvů/eventHubs/consumergroups/Write | Vytvoří nebo aktualizuje vlastnosti uživatele. |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/DELETE | Operace odstranění prostředku EventHub |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/Read | Získá seznam popisů prostředků EventHub. |
> | Akce | Microsoft. EventHub/obory názvů/eventhubs/Write | Vytvoří nebo aktualizuje vlastnosti EventHub. |
> | Akce | Microsoft. EventHub/obory názvů/ipFilterRules/DELETE | Odstranit prostředek filtru IP |
> | Akce | Microsoft. EventHub/obory názvů/ipFilterRules/Read | Získat prostředek filtru IP |
> | Akce | Microsoft. EventHub/obory názvů/ipFilterRules/Write | Vytvořit prostředek filtru IP |
> | Akce dataaction | Microsoft. EventHub/obory názvů/zprávy/přijmout/akce | Příjem zpráv |
> | Akce dataaction | Microsoft. EventHub/obory názvů/zprávy/odeslání/akce | Odesílání zpráv |
> | Akce | Microsoft. EventHub/obory názvů/messagingPlan/Read | Získá plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. EventHub/obory názvů/messagingPlan/Write | Aktualizuje plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. EventHub/obory názvů/networkruleset/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. EventHub/obory názvů/networkruleset/Read | Získá prostředek NetworkRuleSet. |
> | Akce | Microsoft. EventHub/obory názvů/networkruleset/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. EventHub/obory názvů/networkrulesets/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. EventHub/obory názvů/networkrulesets/Read | Získá prostředek NetworkRuleSet. |
> | Akce | Microsoft. EventHub/obory názvů/networkrulesets/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. EventHub/obory názvů/operationresults/Read | Získat stav operace oboru názvů |
> | Akce | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Akce | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Write | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Akce | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/logDefinitions/Read | Získá seznam popisů prostředků v názvech oborů názvů. |
> | Akce | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Akce | Microsoft. EventHub/obory názvů/číst | Získat seznam popisu prostředku oboru názvů |
> | Akce | Microsoft. EventHub/obory názvů/removeAcsNamepsace/Action | Odebrat obor názvů ACS |
> | Akce | Microsoft. EventHub/obory názvů/virtualNetworkRules/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. EventHub/obory názvů/virtualNetworkRules/Read | Získá prostředek pravidla virtuální sítě. |
> | Akce | Microsoft. EventHub/obory názvů/virtualNetworkRules/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. EventHub/obory názvů/zápis | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.EventHub/operations/read | Získat operace |
> | Akce | Microsoft. EventHub/registrace/akce | Zaregistruje předplatné pro poskytovatele prostředků EventHub a povolí vytváření prostředků EventHub. |
> | Akce | Microsoft.EventHub/sku/read | Získá seznam popisů prostředků SKU. |
> | Akce | Microsoft. EventHub/SKU/Regions/Read | Získá seznam popisů prostředků SkuRegions. |
> | Akce | Microsoft. EventHub/zrušit registraci/akce | Zaregistruje poskytovatele prostředků EventHub. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Features/Features/Read | Získá funkce předplatného. |
> | Akce | Microsoft. Features/Operations/Read | Gets the list of operations. |
> | Akce | Microsoft. Features/Providers/Features/Read | Získá funkci předplatného v daném poskytovateli prostředků. |
> | Akce | Microsoft. Features/Providers/Features/Register/Action | Zaregistruje funkci pro předplatné v daném poskytovateli prostředků. |
> | Akce | Microsoft. Features/poskytovatelé/funkce/zrušit registraci/akce | Zruší registraci funkce pro předplatné v daném poskytovateli prostředků. |
> | Akce | Microsoft. Features/Register/Action | Zaregistruje funkci předplatného. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Odstraňte přiřazení konfigurace hostů. |
> | Akce | Microsoft. GuestConfiguration/guestConfigurationAssignments/Read | Získat přiřazení konfigurace hostů. |
> | Akce | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Získat sestavu přiřazení konfigurace hostů |
> | Akce | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Vytvořte nové přiřazení konfigurace hostů. |
> | Akce | Microsoft. GuestConfiguration/Operations/Read | Získá operace pro poskytovatele prostředků Microsoft. GuestConfiguration. |
> | Akce | Microsoft. GuestConfiguration/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. HDInsight/clustery/aplikace/odstranit | Odstranit aplikaci pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/clustery/aplikace/číst | Získat aplikaci pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/clustery/aplikace/zapisovat | Vytvořit nebo aktualizovat cluster aplikace pro HDInsight |
> | Akce | Microsoft. HDInsight/Clusters/changerdpsetting/Action | Změna nastavení RDP pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/clustery/konfigurace/akce | Aktualizovat konfiguraci clusteru HDInsight |
> | Akce | Microsoft. HDInsight/clustery/konfigurace/akce | Získat konfigurace clusteru HDInsight |
> | Akce | Microsoft. HDInsight/clustery/konfigurace/číst | Získat konfigurace clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/delete | Odstranění clusteru HDInsight |
> | Akce | Microsoft. HDInsight/Clusters/Extensions/DELETE | Odstranit rozšíření clusteru pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/clustery/rozšíření/číst | Získat rozšíření clusteru pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/Clusters/Extensions/Write | Vytvoření rozšíření clusteru pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/Clusters/getGatewaySettings/Action | Získat nastavení brány pro cluster HDInsight |
> | Akce | Microsoft. HDInsight/clustery/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro cluster HDInsight prostředku. |
> | Akce | Microsoft. HDInsight/clustery/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro cluster HDInsight prostředku. |
> | Akce | Microsoft. HDInsight/clustery/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro cluster HDInsight. |
> | Akce | Microsoft. HDInsight/Clusters/Read | Získat podrobnosti o clusteru HDInsight |
> | Akce | Microsoft. HDInsight/clustery/role/Změna velikosti/akce | Změna velikosti clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualizace nastavení brány pro cluster HDInsight |
> | Akce | Microsoft.HDInsight/clusters/write | Vytvořit nebo aktualizovat cluster HDInsight |
> | Akce | Microsoft. HDInsight/umístění/možnosti/číst | Získání možností předplatného |
> | Akce | Microsoft. HDInsight/Locations/checkNameAvailability/Read | Ověřit dostupnost názvu |
> | Akce | Microsoft. HDInsight/registrace/akce | Registrace poskytovatele prostředků služby HDInsight pro předplatné |
> | Akce | Microsoft. HDInsight/zrušit registraci/akce | Zrušení registrace poskytovatele prostředků služby HDInsight u předplatného |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. ImportExport/Jobs/DELETE | Odstraní existující úlohu. |
> | Akce | Microsoft. ImportExport/Jobs/listBitLockerKeys/Action | Získá klíče BitLockeru pro zadanou úlohu. |
> | Akce | Microsoft. ImportExport/Jobs/číst | Získá vlastnosti pro zadanou úlohu nebo vrátí seznam úloh. |
> | Akce | Microsoft. ImportExport/Jobs/Write | Vytvoří úlohu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou úlohu. |
> | Akce | Microsoft. ImportExport/Locations/Read | Získá vlastnosti pro zadané umístění nebo vrátí seznam umístění. |
> | Akce | Microsoft. ImportExport/Operations/Read | Načte operace podporované poskytovatelem prostředků. |
> | Akce | Microsoft. ImportExport/Register/Action | Zaregistruje předplatné poskytovatele prostředků pro import/export a povolí vytváření úloh importu/exportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Insights/ActionGroups/Delete | Odstranění skupiny akcí |
> | Akce | Microsoft.Insights/ActionGroups/Read | Čtení skupiny akcí |
> | Akce | Microsoft.Insights/ActionGroups/Write | Vytvoří nebo aktualizuje skupinu akcí. |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivovalo se upozornění protokolu aktivit. |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Delete | Odstranění upozornění protokolu aktivit |
> | Akce | Microsoft. Insights/Upozorněníprotokoluaktivit/Read | Načtení upozornění protokolu aktivit |
> | Akce | Microsoft. Insights/Upozorněníprotokoluaktivit/Write | Vytvoří nebo aktualizuje upozornění protokolu aktivit. |
> | Akce | Microsoft.Insights/AlertRules/Activated/Action | Aktivované klasické upozornění na metriku |
> | Akce | Microsoft.Insights/AlertRules/Delete | Odstranění klasické výstrahy metriky |
> | Akce | Microsoft.Insights/AlertRules/Incidents/Read | Přečíst klasický incident výstrahy metriky |
> | Akce | Microsoft.Insights/AlertRules/Read | Čtení klasické výstrahy metriky |
> | Akce | Microsoft.Insights/AlertRules/Resolved/Action | Vyřešeno klasické upozornění na metriku |
> | Akce | Microsoft.Insights/AlertRules/Throttled/Action | Pravidlo upozornění na klasické metriky omezení |
> | Akce | Microsoft.Insights/AlertRules/Write | Vytvořit nebo aktualizovat klasické upozornění na metriku |
> | Akce | Microsoft.Insights/AutoscaleSettings/Delete | Odstranění nastavení automatického škálování |
> | Akce | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Read | Přečíst nastavení diagnostiky prostředků |
> | Akce | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředků. |
> | Akce | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/logDefinitions/Read | Čtení definic protokolů |
> | Akce | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Akce | Microsoft.Insights/AutoscaleSettings/Read | Přečíst nastavení automatického škálování |
> | Akce | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Vyvoláno automatické škálování pro horizontální navýšení kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Dokončí se horizontální horizontální navýšení kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Bylo zahájeno automatické škálování pro horizontální navýšení kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Automatické škálování pro horizontální navýšení kapacity dokončeno |
> | Akce | Microsoft.Insights/AutoscaleSettings/Write | Vytvoří nebo aktualizuje nastavení automatického škálování. |
> | Akce | Microsoft. Insights/směrný plán/čtení | Čtení směrného plánu metriky (Preview) |
> | Akce | Microsoft. Insights/CalculateBaseline/Read | Vypočítat směrný plán pro hodnoty metriky (Preview) |
> | Akce | Microsoft.Insights/Components/AnalyticsItems/Delete | Odstraňování položky Application Insights Analytics |
> | Akce | Microsoft. Insights/Components/AnalyticsItems/Read | Čtení položky Application Insights Analytics |
> | Akce | Microsoft. Insights/Components/AnalyticsItems/Write | Zápis položky Application Insights Analytics |
> | Akce | Microsoft. Insights/Components/AnalyticsTables/Action | Akce tabulky Application Insights Analytics |
> | Akce | Microsoft.Insights/Components/AnalyticsTables/Delete | Odstraňování schématu Application Insights Analytics Table |
> | Akce | Microsoft. Insights/Components/AnalyticsTables/Read | Čtení schématu Application Insights Analytics tabulka |
> | Akce | Microsoft. Insights/Components/AnalyticsTables/Write | Zápis schématu Application Insights Analytics tabulka |
> | Akce | Microsoft. Insights/komponenty/poznámky/odstranit | Odstranění anotace Application Insights |
> | Akce | Microsoft. Insights/komponenty/poznámky/číst | Čtení poznámky Application Insights |
> | Akce | Microsoft. Insights/komponenty/poznámky/zápis | Zápis Application Insights anotace |
> | Akce | Microsoft. Insights/komponenty/API/Read | Načítání rozhraní API pro Application Insights dat součásti |
> | Akce | Microsoft. Insights/Components/ApiKeys/Action | Generuje se klíč rozhraní Application Insights API. |
> | Akce | Microsoft.Insights/Components/ApiKeys/Delete | Odstraňuje se Application Insights klíč rozhraní API. |
> | Akce | Microsoft. Insights/Components/ApiKeys/Read | Čtení klíče rozhraní API Application Insights |
> | Akce | Microsoft. Insights/Components/BillingPlanForComponent/Read | Čtení plánu fakturace pro Application Insights komponentu |
> | Akce | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Čtení aktuálních funkcí fakturace pro součást Application Insights |
> | Akce | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Zápis aktuálních funkcí fakturace pro součást Application Insights |
> | Akce | Microsoft. Insights/Components/DailyCapReached/Action | Dosáhlo se denního limitu pro Application Insights komponentu |
> | Akce | Microsoft. Insights/Components/DailyCapWarningThresholdReached/Action | Bylo dosaženo prahové hodnoty pro upozornění na denní limit pro součást Application Insights |
> | Akce | Microsoft. Insights/Components/DefaultWorkItemConfig/Read | Čtení Application Insights výchozí konfigurace integrace ALM |
> | Akce | Microsoft. Insights/Components/DELETE | Odstraňuje se konfigurace komponenty Application Insights. |
> | Akce | Microsoft. Insights/komponenty/Events/číst | Získání protokolů z Application Insights pomocí formátu dotazu OData |
> | Akce | Microsoft. Insights/Components/ExportConfiguration/Action | Akce Application Insights exportu nastavení |
> | Akce | Microsoft. Insights/Components/ExportConfiguration/DELETE | Odstraňování nastavení exportu Application Insights |
> | Akce | Microsoft. Insights/Components/ExportConfiguration/Read | Čtení nastavení exportu Application Insights |
> | Akce | Microsoft. Insights/Components/ExportConfiguration/Write | Zápis nastavení exportu Application Insights |
> | Akce | Microsoft. Insights/Components/ExtendQueries/Read | Čtení výsledků rozšířeného dotazu součásti Application Insights |
> | Akce | Microsoft. Insights/komponenty/oblíbené/odstranit | Odstraňování oblíbených Application Insights |
> | Akce | Microsoft. Insights/komponenty/oblíbené/číst | Čtení Application Insights oblíbené položky |
> | Akce | Microsoft. Insights/komponenty/oblíbená/Write | Zápis Application Insights oblíbené položky |
> | Akce | Microsoft. Insights/Components/FeatureCapabilities/Read | Čtení možností funkce Application Insights součásti |
> | Akce | Microsoft. Insights/Components/GetAvailableBillingFeatures/Read | Čtení funkcí vyúčtování dostupných pro součást Application Insights |
> | Akce | Microsoft. Insights/komponenty/GetToken/číst | Načítá se token součásti Application Insights. |
> | Akce | Microsoft. Insights/Components/MetricDefinitions/Read | Načítají se definice metriky Application Insights komponenty. |
> | Akce | Microsoft. Insights/komponenty/metriky/číst | Čtení Application Insights metriky komponent |
> | Akce | Microsoft. Insights/Components/Move/Action | Přesunutí součásti Application Insights do jiné skupiny prostředků nebo předplatného |
> | Akce | Microsoft. Insights/Components/MyAnalyticsItems/DELETE | Odstranění Application Insights osobní položky analýzy |
> | Akce | Microsoft. Insights/Components/MyAnalyticsItems/Read | Čtení položky Application Insights osobní analýzy |
> | Akce | Microsoft. Insights/Components/MyAnalyticsItems/Write | Zápis položky Application Insights osobní analýzy |
> | Akce | Microsoft. Insights/Components/MyFavorites/Read | Čtení osobní oblíbené položky Application Insights |
> | Akce | Microsoft. Insights/komponenty/Operations/Read | Získat stav dlouhotrvajících operací v Application Insights |
> | Akce | Microsoft. Insights/Components/PricingPlans/Read | Čtení cenového plánu součásti Application Insights |
> | Akce | Microsoft. Insights/Components/PricingPlans/Write | Zápis cenového plánu součásti Application Insights |
> | Akce | Microsoft. Insights/Components/ProactiveDetectionConfigs/Read | Čte se konfigurace Application Insights proaktivní detekce. |
> | Akce | Microsoft. Insights/Components/ProactiveDetectionConfigs/Write | Zápis konfigurace Application Insights proaktivní detekce |
> | Akce | Microsoft. Insights/Components/Providers/Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Akce | Microsoft. Insights/komponenty/vyprázdnění/akce | Vymazání dat z Application Insights |
> | Akce | Microsoft. Insights/Components/Query/Read | Spouštění dotazů proti protokolům Application Insights |
> | Akce | Microsoft. Insights/Components/QuotaStatus/Read | Načítá se stav kvóty pro součást Application Insights. |
> | Akce | Microsoft. Insights/komponenty/číst | Čtení konfigurace komponenty Application Insights |
> | Akce | Microsoft. Insights/Components/SyntheticMonitorLocations/Read | Čtení Application Insights umístění WebTest |
> | Akce | Microsoft. Insights/komponenty/webtests/číst | Čtení konfigurace webového testu |
> | Akce | Microsoft. Insights/Components/WorkItemConfigs/DELETE | Odstraňování konfigurace integrace Application Insights ALM |
> | Akce | Microsoft. Insights/Components/WorkItemConfigs/Read | Čtení konfigurace integrace Application Insights ALM |
> | Akce | Microsoft. Insights/Components/WorkItemConfigs/Write | Zápis konfigurace integrace Application Insights ALM |
> | Akce | Microsoft. Insights/komponenty/Write | Zápis do konfigurace komponenty Application Insights |
> | Akce | Microsoft. Insights/DataCollectionRuleAssociations/DELETE | Odstranění přidružení prostředku s pravidlem shromažďování dat |
> | Akce | Microsoft. Insights/DataCollectionRuleAssociations/Read | Přečtěte si přidružení prostředku s pravidlem shromažďování dat. |
> | Akce | Microsoft. Insights/DataCollectionRuleAssociations/Write | Vytvoření nebo aktualizace přidružení prostředku pomocí pravidla shromažďování dat |
> | Akce dataaction | Microsoft. Insights/DataCollectionRules/data/Write | Odeslat data do pravidla shromažďování dat |
> | Akce | Microsoft. Insights/DataCollectionRules/DELETE | Odstranění pravidla shromažďování dat |
> | Akce | Microsoft. Insights/DataCollectionRules/Read | Čtení pravidla shromažďování dat |
> | Akce | Microsoft. Insights/DataCollectionRules/Write | Vytvoří nebo aktualizuje pravidlo shromažďování dat. |
> | Akce | Microsoft.Insights/DiagnosticSettings/Delete | Odstranění nastavení diagnostiky prostředků |
> | Akce | Microsoft. Insights/DiagnosticSettings/Read | Přečíst nastavení diagnostiky prostředků |
> | Akce | Microsoft. Insights/DiagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředků. |
> | Akce | Microsoft.Insights/EventCategories/Read | Číst dostupné kategorie událostí protokolu aktivit |
> | Akce | Microsoft.Insights/eventtypes/digestevents/Read | Výtah čtení typu události správy |
> | Akce | Microsoft. Insights/EventTypes/Values/Read | Číst události protokolu aktivit |
> | Akce | Microsoft. Insights/ExtendedDiagnosticSettings/DELETE | Odstraní nastavení diagnostiky protokolu síťového toku. |
> | Akce | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Přečíst nastavení diagnostiky protokolů síťových toků |
> | Akce | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky protokolu síťového toku. |
> | Akce | Microsoft.Insights/ListMigrationDate/Action | Získat datum migrace předplatného |
> | Akce | Microsoft. Insights/ListMigrationDate/Read | Získat datum migrace předplatného |
> | Akce | Microsoft. Insights/LogDefinitions/Read | Čtení definic protokolů |
> | Akce | Microsoft.Insights/LogProfiles/Delete | Odstraní profil protokolu aktivit. |
> | Akce | Microsoft.Insights/LogProfiles/Read | Číst profil protokolu aktivit |
> | Akce | Microsoft.Insights/LogProfiles/Write | Vytvoří nebo aktualizuje profil protokolu aktivit. |
> | Akce | Microsoft. Insights/logs/ADAssessmentRecommendation/číst | Read data from the ADAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/ADReplicationResult/číst | Read data from the ADReplicationResult table |
> | Akce | Microsoft. Insights/logs/ADSecurityAssessmentRecommendation/číst | Read data from the ADSecurityAssessmentRecommendation table |
> | Akce | Microsoft. Insights/protokoly/výstraha/čtení | Read data from the Alert table |
> | Akce | Microsoft.Insights/Logs/AlertHistory/Read | Read data from the AlertHistory table |
> | Akce | Microsoft.Insights/Logs/ApplicationInsights/Read | Read data from the ApplicationInsights table |
> | Akce | Microsoft. Insights/logs/AzureActivity/číst | Read data from the AzureActivity table |
> | Akce | Microsoft. Insights/logs/AzureMetrics/číst | Read data from the AzureMetrics table |
> | Akce | Microsoft. Insights/logs/BoundPort/číst | Read data from the BoundPort table |
> | Akce | Microsoft.Insights/Logs/CommonSecurityLog/Read | Read data from the CommonSecurityLog table |
> | Akce | Microsoft. Insights/protokoly/počítač/číst | Read data from the ComputerGroup table |
> | Akce | Microsoft. Insights/logs/ConfigurationChange/číst | Read data from the ConfigurationChange table |
> | Akce | Microsoft. Insights/logs/ConfigurationData/číst | Read data from the ConfigurationData table |
> | Akce | Microsoft. Insights/logs/ContainerImageInventory/číst | Read data from the ContainerImageInventory table |
> | Akce | Microsoft. Insights/logs/ContainerInventory/číst | Read data from the ContainerInventory table |
> | Akce | Microsoft.Insights/Logs/ContainerLog/Read | Read data from the ContainerLog table |
> | Akce | Microsoft.Insights/Logs/ContainerServiceLog/Read | Read data from the ContainerServiceLog table |
> | Akce | Microsoft.Insights/Logs/DeviceAppCrash/Read | Read data from the DeviceAppCrash table |
> | Akce | Microsoft. Insights/logs/DeviceAppLaunch/číst | Read data from the DeviceAppLaunch table |
> | Akce | Microsoft. Insights/logs/DeviceCalendar/číst | Read data from the DeviceCalendar table |
> | Akce | Microsoft. Insights/logs/DeviceCleanup/číst | Read data from the DeviceCleanup table |
> | Akce | Microsoft. Insights/logs/DeviceConnectSession/číst | Read data from the DeviceConnectSession table |
> | Akce | Microsoft. Insights/logs/DeviceEtw/číst | Read data from the DeviceEtw table |
> | Akce | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Read data from the DeviceHardwareHealth table |
> | Akce | Microsoft.Insights/Logs/DeviceHealth/Read | Read data from the DeviceHealth table |
> | Akce | Microsoft. Insights/logs/DeviceHeartbeat/číst | Read data from the DeviceHeartbeat table |
> | Akce | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Read data from the DeviceSkypeHeartbeat table |
> | Akce | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Read data from the DeviceSkypeSignIn table |
> | Akce | Microsoft.Insights/Logs/DeviceSleepState/Read | Read data from the DeviceSleepState table |
> | Akce | Microsoft. Insights/logs/DHAppFailure/číst | Read data from the DHAppFailure table |
> | Akce | Microsoft. Insights/logs/DHAppReliability/číst | Read data from the DHAppReliability table |
> | Akce | Microsoft. Insights/logs/DHDriverReliability/číst | Read data from the DHDriverReliability table |
> | Akce | Microsoft. Insights/logs/DHLogonFailures/číst | Read data from the DHLogonFailures table |
> | Akce | Microsoft. Insights/logs/DHLogonMetrics/číst | Read data from the DHLogonMetrics table |
> | Akce | Microsoft. Insights/logs/DHOSCrashData/číst | Read data from the DHOSCrashData table |
> | Akce | Microsoft. Insights/logs/DHOSReliability/číst | Read data from the DHOSReliability table |
> | Akce | Microsoft.Insights/Logs/DHWipAppLearning/Read | Read data from the DHWipAppLearning table |
> | Akce | Microsoft. Insights/logs/DnsEvents/číst | Read data from the DnsEvents table |
> | Akce | Microsoft. Insights/logs/DnsInventory/číst | Read data from the DnsInventory table |
> | Akce | Microsoft. Insights/logs/ETWEvent/číst | Read data from the ETWEvent table |
> | Akce | Microsoft. Insights/protokoly/událost/čtení | Read data from the Event table |
> | Akce | Microsoft. Insights/logs/ExchangeAssessmentRecommendation/číst | Read data from the ExchangeAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/ExchangeOnlineAssessmentRecommendation/číst | Read data from the ExchangeOnlineAssessmentRecommendation table |
> | Akce | Microsoft. Insights/protokoly/prezenční signál/čtení | Read data from the Heartbeat table |
> | Akce | Microsoft. Insights/logs/IISAssessmentRecommendation/číst | Read data from the IISAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/InboundConnection/číst | Read data from the InboundConnection table |
> | Akce | Microsoft.Insights/Logs/KubeNodeInventory/Read | Read data from the KubeNodeInventory table |
> | Akce | Microsoft.Insights/Logs/KubePodInventory/Read | Read data from the KubePodInventory table |
> | Akce | Microsoft.Insights/Logs/LinuxAuditLog/Read | Read data from the LinuxAuditLog table |
> | Akce | Microsoft. Insights/logs/MAApplication/číst | Read data from the MAApplication table |
> | Akce | Microsoft.Insights/Logs/MAApplicationHealth/Read | Read data from the MAApplicationHealth table |
> | Akce | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Read data from the MAApplicationHealthAlternativeVersions table |
> | Akce | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Read data from the MAApplicationHealthIssues table |
> | Akce | Microsoft. Insights/logs/MAApplicationInstance/číst | Read data from the MAApplicationInstance table |
> | Akce | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Read data from the MAApplicationInstanceReadiness table |
> | Akce | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Read data from the MAApplicationReadiness table |
> | Akce | Microsoft. Insights/logs/MADeploymentPlan/číst | Read data from the MADeploymentPlan table |
> | Akce | Microsoft. Insights/logs/MADevice/číst | Read data from the MADevice table |
> | Akce | Microsoft. Insights/logs/MADevicePnPHealth/číst | Read data from the MADevicePnPHealth table |
> | Akce | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Read data from the MADevicePnPHealthAlternativeVersions table |
> | Akce | Microsoft. Insights/logs/MADevicePnPHealthIssues/číst | Read data from the MADevicePnPHealthIssues table |
> | Akce | Microsoft. Insights/logs/MADeviceReadiness/číst | Read data from the MADeviceReadiness table |
> | Akce | Microsoft. Insights/logs/MADriverInstanceReadiness/číst | Read data from the MADriverInstanceReadiness table |
> | Akce | Microsoft.Insights/Logs/MADriverReadiness/Read | Read data from the MADriverReadiness table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddin/Read | Read data from the MAOfficeAddin table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Read data from the MAOfficeAddinHealth table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Read data from the MAOfficeAddinHealthIssues table |
> | Akce | Microsoft. Insights/logs/MAOfficeAddinInstance/číst | Read data from the MAOfficeAddinInstance table |
> | Akce | Microsoft. Insights/logs/MAOfficeAddinInstanceReadiness/číst | Read data from the MAOfficeAddinInstanceReadiness table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Read data from the MAOfficeAddinReadiness table |
> | Akce | Microsoft. Insights/logs/MAOfficeApp/číst | Read data from the MAOfficeApp table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Read data from the MAOfficeAppHealth table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Read data from the MAOfficeAppInstance table |
> | Akce | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Read data from the MAOfficeAppReadiness table |
> | Akce | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Read data from the MAOfficeBuildInfo table |
> | Akce | Microsoft. Insights/logs/MAOfficeCurrencyAssessment/číst | Read data from the MAOfficeCurrencyAssessment table |
> | Akce | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Read data from the MAOfficeCurrencyAssessmentDailyCounts table |
> | Akce | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Read data from the MAOfficeDeploymentStatus table |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Read data from the MAOfficeMacroHealth table |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Read data from the MAOfficeMacroHealthIssues table |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Read data from the MAOfficeMacroIssueInstanceReadiness table |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Read data from the MAOfficeMacroIssueReadiness table |
> | Akce | Microsoft. Insights/logs/MAOfficeMacroSummary/číst | Read data from the MAOfficeMacroSummary table |
> | Akce | Microsoft.Insights/Logs/MAOfficeSuite/Read | Read data from the MAOfficeSuite table |
> | Akce | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Read data from the MAOfficeSuiteInstance table |
> | Akce | Microsoft. Insights/logs/MAProposedPilotDevices/číst | Read data from the MAProposedPilotDevices table |
> | Akce | Microsoft. Insights/logs/MAWindowsBuildInfo/číst | Read data from the MAWindowsBuildInfo table |
> | Akce | Microsoft. Insights/logs/MAWindowsCurrencyAssessment/číst | Read data from the MAWindowsCurrencyAssessment table |
> | Akce | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Read data from the MAWindowsCurrencyAssessmentDailyCounts table |
> | Akce | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Read data from the MAWindowsDeploymentStatus table |
> | Akce | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Read data from the MAWindowsSysReqInstanceReadiness table |
> | Akce | Microsoft. Insights/logs/NetworkMonitoring/číst | Read data from the NetworkMonitoring table |
> | Akce | Microsoft. Insights/logs/OfficeActivity/číst | Read data from the OfficeActivity table |
> | Akce | Microsoft. Insights/protokoly/operace/čtení | Read data from the Operation table |
> | Akce | Microsoft. Insights/logs/OutboundConnection/číst | Read data from the OutboundConnection table |
> | Akce | Microsoft. Insights/protokoly/výkon/čtení | Read data from the Perf table |
> | Akce | Microsoft. Insights/logs/ProtectionStatus/číst | Read data from the ProtectionStatus table |
> | Akce | Microsoft. Insights/protokoly/číst | Čtení dat ze všech protokolů |
> | Akce | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Čtení dat z tabulky ReservedAzureCommonFields |
> | Akce | Microsoft. Insights/logs/ReservedCommonFields/číst | Read data from the ReservedCommonFields table |
> | Akce | Microsoft. Insights/logs/SCCMAssessmentRecommendation/číst | Read data from the SCCMAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/SCOMAssessmentRecommendation/číst | Read data from the SCOMAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/SecurityAlert/číst | Read data from the SecurityAlert table |
> | Akce | Microsoft.Insights/Logs/SecurityBaseline/Read | Read data from the SecurityBaseline table |
> | Akce | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Read data from the SecurityBaselineSummary table |
> | Akce | Microsoft. Insights/logs/SecurityDetection/číst | Read data from the SecurityDetection table |
> | Akce | Microsoft. Insights/logs/SecurityEvent/číst | Read data from the SecurityEvent table |
> | Akce | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Read data from the ServiceFabricOperationalEvent table |
> | Akce | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Read data from the ServiceFabricReliableActorEvent table |
> | Akce | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Read data from the ServiceFabricReliableServiceEvent table |
> | Akce | Microsoft. Insights/logs/SfBAssessmentRecommendation/číst | Read data from the SfBAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/SfBOnlineAssessmentRecommendation/číst | Read data from the SfBOnlineAssessmentRecommendation table |
> | Akce | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Read data from the SharePointOnlineAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/SPAssessmentRecommendation/číst | Read data from the SPAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/SQLAssessmentRecommendation/číst | Read data from the SQLAssessmentRecommendation table |
> | Akce | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Read data from the SQLQueryPerformance table |
> | Akce | Microsoft. Insights/protokoly/syslog/Read | Read data from the Syslog table |
> | Akce | Microsoft. Insights/logs/SysmonEvent/číst | Read data from the SysmonEvent table |
> | Akce | Microsoft. Insights/logs/Tables. vlastní/číst | Reading data from any custom log |
> | Akce | Microsoft. Insights/logs/UAApp/číst | Read data from the UAApp table |
> | Akce | Microsoft. Insights/logs/UAComputer/číst | Read data from the UAComputer table |
> | Akce | Microsoft. Insights/logs/UAComputerRank/číst | Read data from the UAComputerRank table |
> | Akce | Microsoft. Insights/logs/UADriver/číst | Read data from the UADriver table |
> | Akce | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Read data from the UADriverProblemCodes table |
> | Akce | Microsoft. Insights/logs/UAFeedback/číst | Read data from the UAFeedback table |
> | Akce | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Read data from the UAHardwareSecurity table |
> | Akce | Microsoft. Insights/logs/UAIESiteDiscovery/číst | Read data from the UAIESiteDiscovery table |
> | Akce | Microsoft. Insights/logs/UAOfficeAddIn/číst | Read data from the UAOfficeAddIn table |
> | Akce | Microsoft. Insights/logs/UAProposedActionPlan/číst | Read data from the UAProposedActionPlan table |
> | Akce | Microsoft.Insights/Logs/UASysReqIssue/Read | Read data from the UASysReqIssue table |
> | Akce | Microsoft. Insights/logs/UAUpgradedComputer/číst | Read data from the UAUpgradedComputer table |
> | Akce | Microsoft. Insights/protokoly/aktualizovat/číst | Read data from the Update table |
> | Akce | Microsoft.Insights/Logs/UpdateRunProgress/Read | Read data from the UpdateRunProgress table |
> | Akce | Microsoft.Insights/Logs/UpdateSummary/Read | Read data from the UpdateSummary table |
> | Akce | Microsoft. Insights/protokoly/využití/čtení | Read data from the Usage table |
> | Akce | Microsoft.Insights/Logs/W3CIISLog/Read | Read data from the W3CIISLog table |
> | Akce | Microsoft. Insights/logs/WaaSDeploymentStatus/číst | Read data from the WaaSDeploymentStatus table |
> | Akce | Microsoft. Insights/logs/WaaSInsiderStatus/číst | Read data from the WaaSInsiderStatus table |
> | Akce | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Read data from the WaaSUpdateStatus table |
> | Akce | Microsoft. Insights/logs/WDAVStatus/číst | Read data from the WDAVStatus table |
> | Akce | Microsoft. Insights/logs/WDAVThreat/číst | Read data from the WDAVThreat table |
> | Akce | Microsoft. Insights/logs/WindowsClientAssessmentRecommendation/číst | Read data from the WindowsClientAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/WindowsFirewall/číst | Read data from the WindowsFirewall table |
> | Akce | Microsoft. Insights/logs/WindowsServerAssessmentRecommendation/číst | Read data from the WindowsServerAssessmentRecommendation table |
> | Akce | Microsoft. Insights/logs/WireData/číst | Read data from the WireData table |
> | Akce | Microsoft. Insights/logs/WUDOAggregatedStatus/číst | Read data from the WUDOAggregatedStatus table |
> | Akce | Microsoft. Insights/logs/WUDOStatus/číst | Read data from the WUDOStatus table |
> | Akce | Microsoft.Insights/MetricAlerts/Delete | Odstranění výstrahy metriky |
> | Akce | Microsoft. Insights/MetricAlerts/Read | Čtení výstrahy metriky |
> | Akce | Microsoft. Insights/MetricAlerts/stav/čtení | Přečíst stav výstrahy metriky |
> | Akce | Microsoft. Insights/MetricAlerts/Write | Vytvoření nebo aktualizace výstrahy metriky |
> | Akce | Microsoft.Insights/MetricBaselines/Read | Čtení standardních hodnot metriky |
> | Akce | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/Read | Čtení definic metriky |
> | Akce | Microsoft. Insights/MetricDefinitions/Providers/Microsoft. Insights/Read | Čtení definic metriky |
> | Akce | Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Akce | Microsoft. Insights/Metricnamespaces/Read | Číst obory názvů metriky |
> | Akce | Microsoft. Insights/metriky/akce | Akce metriky |
> | Akce | Microsoft. Insights/metriky/Microsoft. Insights/číst | Čtení metrik |
> | Akce | Microsoft. Insights/metriky/zprostředkovatelé/metriky/čtení | Čtení metrik |
> | Akce | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Akce dataaction | Microsoft. Insights/metriky/Write | Zapsat metriky |
> | Akce | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrovat předplatné do nového cenového modelu |
> | Akce | Microsoft. Insights/Operations/Read | Operace čtení |
> | Akce | Microsoft. Insights/registr/Action | Registrace poskytovatele Microsoft Insights |
> | Akce | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Vrátit zpět předplatné na starší verzi cenového modelu |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Delete | Odstranění pravidla plánovaného dotazu |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Read | Načetlo se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Write | Zápis pravidla plánovaného dotazu |
> | Akce | Microsoft. Insights/klienti/registrovat/akce | Inicializuje poskytovatele Microsoft Insights. |
> | Akce | Microsoft.Insights/Unregister/Action | Registrace poskytovatele Microsoft Insights |
> | Akce | Microsoft.Insights/Webtests/Delete | Odstranění konfigurace webového testu |
> | Akce | Microsoft. Insights/webtests/GetToken/číst | Čtení tokenu WebTest |
> | Akce | Microsoft. Insights/webtests/MetricDefinitions/Read | Čtení definic metriky webového testu |
> | Akce | Microsoft. Insights/webtests/metriky/číst | Čtení metrik webového testu |
> | Akce | Microsoft. Insights/webtests/číst | Čtení konfigurace webového testu |
> | Akce | Microsoft. Insights/webtests/Write | Zápis do konfigurace webového testu |
> | Akce | Microsoft. Insights/sešity/odstranit | Odstranění sešitu |
> | Akce | Microsoft. Insights/sešity/číst | Přečíst sešit |
> | Akce | Microsoft. Insights/sešity/zápis | Vytvoří nebo aktualizuje sešit. |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Intune/diagnosticsettings/delete | Deleting a diagnostic setting |
> | Akce | Microsoft. Intune/diagnosticsettings/Read | Reading a diagnostic setting |
> | Akce | Microsoft.Intune/diagnosticsettings/write | Writing a diagnostic setting |
> | Akce | Microsoft. Intune/diagnosticsettingscategories/Read | Reading a diagnostic setting categories |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. IoTCentral/appTemplates/Action | Získá všechny dostupné šablony aplikací v Azure IoT Central |
> | Akce | Microsoft. IoTCentral/checkNameAvailability/Action | Zkontroluje, jestli je k dispozici IoT Central název aplikace. |
> | Akce | Microsoft.IoTCentral/checkSubdomainAvailability/action | Kontroluje, zda je k dispozici IoT Central subdoména aplikace. |
> | Akce | Microsoft.IoTCentral/IoTApps/delete | Odstraní aplikace IoT Central. |
> | Akce | Microsoft. IoTCentral/IoTApps/Read | Načte jednu IoT Central aplikaci. |
> | Akce | Microsoft. IoTCentral/IoTApps/Write | Vytvoří nebo aktualizuje aplikace IoT Central. |
> | Akce | Microsoft.IoTCentral/operations/read | Získá všechny dostupné operace s IoT Central aplikacemi. |
> | Akce | Microsoft. IoTCentral/Register/Action | Registrace předplatného pro poskytovatele prostředků Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. IoTSpaces/Graph/DELETE | Odstraní prostředek Microsoft. IoTSpaces Graph. |
> | Akce | Microsoft. IoTSpaces/Graph/Read | Načte prostředky grafu Microsoft. IoTSpaces. |
> | Akce | Microsoft.IoTSpaces/Graph/write | Vytvořit prostředek Microsoft. IoTSpaces Graph |
> | Akce | Microsoft. IoTSpaces/Register/Action | Registrace předplatného pro poskytovatele prostředků Microsoft. IoTSpaces Graph pro povolení vytváření prostředků |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. checkNameAvailability trezor//Read | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | Akce | Microsoft.KeyVault/deletedVaults/read | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | Akce | Microsoft. a trezor/hsmPools/DELETE | Odstranění fondu HSM |
> | Akce | Microsoft. joinVault trezor/hsmPools//Action | Připojte Trezor klíčů k fondu HSM. |
> | Akce | Microsoft. hsmPools trezor//Read | Zobrazení vlastností fondu HSM |
> | Akce | Microsoft. hsmPools – trezor//Write | Vytvoří nový fond HSM, ve kterém se aktualizuje vlastnosti existujícího fondu HSM. |
> | Akce | Microsoft.KeyVault/locations/deletedVaults/purge/action | Vymazání tichého odstraněného trezoru klíčů |
> | Akce | Microsoft.KeyVault/locations/deletedVaults/read | Zobrazit vlastnosti neodstraněného trezoru klíčů |
> | Akce | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Upozorní Microsoft. webtrezor, že se odstraňuje virtuální síť nebo podsíť. |
> | Akce | Microsoft.KeyVault/locations/operationResults/read | Zkontroluje výsledek dlouhotrvající operace spuštění. |
> | Akce | Microsoft.KeyVault/operations/read | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | Akce | Microsoft... trezor/registrace/akce | Zaregistruje předplatné. |
> | Akce | Microsoft.KeyVault/unregister/action | Zruší registraci předplatného. |
> | Akce | Trezory Microsoft. a trezory/accessPolicies/Write | Aktualizujte existující zásady přístupu sloučením nebo nahrazením nebo přidejte nové zásady přístupu do trezoru. |
> | Akce | Trezor Microsoft. a trezory/odstranění | Odstranění trezoru klíčů |
> | Akce | Trezor Microsoft. a trezory/nasazení/akce | Umožňuje přístup k tajným klíčům v trezoru klíčů při nasazování prostředků Azure. |
> | Akce | Microsoft.KeyVault/vaults/eventGridFilters/delete | Upozorní Microsoft. webtrezor, že se odstraňuje EventGrid odběr pro Key Vault. |
> | Akce | Microsoft.KeyVault/vaults/eventGridFilters/read | Upozorní Microsoft. webtrezor, že se zobrazuje EventGrid odběr pro Key Vault. |
> | Akce | Microsoft.KeyVault/vaults/eventGridFilters/write | Upozorní Microsoft. webtrezor, že se vytváří nové předplatné EventGrid pro Key Vault. |
> | Akce | Trezor Microsoft. a trezory/čtení | Zobrazení vlastností trezoru klíčů |
> | Akce | Trezor Microsoft. a trezory/tajné klíče/čtení | Umožňuje zobrazit vlastnosti tajného klíče, ale ne jeho hodnotu. |
> | Akce | Trezor Microsoft. a trezory/tajné klíče/zápis | Vytvoří nový tajný klíč nebo aktualizuje hodnotu existujícího tajného kódu. |
> | Akce | Microsoft.KeyVault/vaults/write | Vytvoří nový trezor klíčů nebo aktualizuje vlastnosti existujícího trezoru klíčů. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Kusto/Clusters/Activate/Action | Spustí cluster. |
> | Akce | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Odstraní připojenou konfiguraci databáze resourceCopy. |
> | Akce | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Přečte připojenou konfiguraci databáze resourceCopy. |
> | Akce | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Zapisuje připojenou konfiguraci databáze resourceCopy. |
> | Akce | Microsoft. Kusto/Clusters/CheckNameAvailability/Action | Kontroluje dostupnost názvu clusteru. |
> | Akce | Microsoft. Kusto/clustery/databáze/AddPrincipals/Action | Přidá objekty zabezpečení databáze. |
> | Akce | Microsoft. Kusto/clustery/databáze/CheckNameAvailability/Action | Kontroluje dostupnost názvu pro daný typ. |
> | Akce | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Odstraní datová připojení resourceCopy. |
> | Akce | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Přečte datová připojení resourceCopy. |
> | Akce | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Zapisuje datová připojení resourceCopy. |
> | Akce | Microsoft. Kusto/clustery/databáze/DataConnectionValidation/Action | Ověří datové připojení databáze. |
> | Akce | Microsoft.Kusto/Clusters/Databases/delete | Odstraní resourceCopy databáze. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Odstraní připojení centra událostí resourceCopy. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Přečte připojení centra událostí resourceCopy. |
> | Akce | Microsoft. Kusto/clustery/databáze/EventHubConnections/Write | Zapisuje připojení centra událostí resourceCopy. |
> | Akce | Microsoft. Kusto/clustery/databáze/EventHubConnectionValidation/Action | Ověří připojení centra událostí databáze. |
> | Akce | Microsoft. Kusto/clustery/databáze/ListPrincipals/Action | Seznam objektů zabezpečení databáze. |
> | Akce | Microsoft. Kusto/clustery/databáze/PrincipalAssignments/DELETE | Odstraní přiřazení objektu zabezpečení databáze resourceCopy. |
> | Akce | Microsoft. Kusto/clustery/databáze/PrincipalAssignments/Read | Přečte přiřazení objektů zabezpečení databáze resourceCopy. |
> | Akce | Microsoft. Kusto/clustery/databáze/PrincipalAssignments/Write | Zapíše přiřazení objektů zabezpečení databáze resourceCopy. |
> | Akce | Microsoft.Kusto/Clusters/Databases/read | Přečte databázi resourceCopy. |
> | Akce | Microsoft. Kusto/clustery/databáze/RemovePrincipals/Action | Odebere objekty zabezpečení databáze. |
> | Akce | Microsoft.Kusto/Clusters/Databases/write | Zapíše resourceCopy databáze. |
> | Akce | Microsoft. Kusto/Clusters/deaktivovat/Action | Zastaví cluster. |
> | Akce | Microsoft.Kusto/Clusters/delete | Odstraní cluster resourceCopy. |
> | Akce | Microsoft. Kusto/Clusters/DetachFollowerDatabases/Action | Odpojí databáze následného odesílatele. |
> | Akce | Microsoft. Kusto/Clusters/DiagnoseVirtualNetwork/Action | Diagnostikuje stav připojení k síti u externích prostředků, na kterých je služba depedent. |
> | Akce | Microsoft. Kusto/Clusters/ListFollowerDatabases/Action | Zobrazí seznam databází od následného. |
> | Akce | Microsoft.Kusto/Clusters/read | Přečte cluster resourceCopy. |
> | Akce | Microsoft. Kusto/Clusters/SKU/číst | Přečte resourceCopyy SKU clusteru. |
> | Akce | Microsoft. Kusto/Clusters/Start/Action | Spustí cluster. |
> | Akce | Microsoft. Kusto/Clusters/stop/Action | Zastaví cluster. |
> | Akce | Microsoft.Kusto/Clusters/write | Zapisuje cluster resourceCopy. |
> | Akce | Microsoft. Kusto/Locations/CheckNameAvailability/Action | Zkontroluje dostupnost názvu resourceCopy. |
> | Akce | Microsoft. Kusto/Locations/GetNetworkPolicies/Action | Získá zásady záměru sítě. |
> | Akce | Microsoft.Kusto/locations/operationresults/read | Čtení operací resourceCopys |
> | Akce | Microsoft.Kusto/Operations/read | Čtení operací resourceCopys |
> | Akce | Microsoft. Kusto/Register/Action | Akce registrace předplatného |
> | Akce | Microsoft. Kusto/Register/Action | Zaregistruje předplatné do poskytovatele prostředků Kusto. |
> | Akce | Microsoft.Kusto/SKUs/read | Přečte resourceCopy SKU. |
> | Akce | Microsoft. Kusto/zrušit registraci/akce | Zruší registraci předplatného pro poskytovatele prostředků Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.LabServices/labAccounts/CreateLab/action | Vytvořte testovací prostředí v účtu testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/delete | Odstraní účty testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/galleryImages/delete | Odstranit image z Galerie |
> | Akce | Microsoft.LabServices/labAccounts/galleryImages/read | Čtení imagí galerie. |
> | Akce | Microsoft.LabServices/labAccounts/galleryImages/write | Přidání nebo úprava imagí galerie. |
> | Akce | Microsoft. LabServices/labAccounts/GetRegionalAvailability/Action | Získat informace o regionální dostupnosti pro každou kategorii velikostí nakonfigurovanou v rámci účtu testovacího prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Přidání uživatelů do testovacího prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/delete | Odstraňte Labs. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Odstranit nastavení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Čtení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resets the user password on an environment |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Starts an environment by starting all resources inside the environment. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stops an environment by stopping all resources inside the environment |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Add or modify environments. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisions/deprovisions required resources for an environment setting based on current state of the lab/environment setting. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Read environment setting. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Resets password on the template virtual machine. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Saves current template image to the shared gallery in the lab account |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Delete schedules. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Read schedules. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Add or modify schedules. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Starts a template by starting all resources inside the template. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stops a template by stopping all resources inside the template. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Add or modify environment setting. |
> | Akce | Microsoft.LabServices/labAccounts/labs/read | Read labs. |
> | Akce | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Send email with registration link to the lab |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/delete | Delete users. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/read | Read users. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/write | Add or modify users. |
> | Akce | Microsoft.LabServices/labAccounts/labs/write | Add or modify labs. |
> | Akce | Microsoft.LabServices/labAccounts/read | Read lab accounts. |
> | Akce | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Delete sharedgalleries. |
> | Akce | Microsoft.LabServices/labAccounts/sharedGalleries/read | Read sharedgalleries. |
> | Akce | Microsoft.LabServices/labAccounts/sharedGalleries/write | Add or modify sharedgalleries. |
> | Akce | Microsoft.LabServices/labAccounts/sharedImages/delete | Delete sharedimages. |
> | Akce | Microsoft.LabServices/labAccounts/sharedImages/read | Read sharedimages. |
> | Akce | Microsoft.LabServices/labAccounts/sharedImages/write | Add or modify sharedimages. |
> | Akce | Microsoft.LabServices/labAccounts/write | Add or modify lab accounts. |
> | Akce | Microsoft.LabServices/locations/operations/read | Read operations. |
> | Akce | Microsoft.LabServices/register/action | Registers the subscription |
> | Akce | Microsoft.LabServices/users/ListAllEnvironments/action | List all Environments for the user |
> | Akce | Microsoft.LabServices/users/Register/action | Register a user to a managed lab |
> | Akce | Microsoft.LabServices/users/ResetPassword/action | Resets the user password on an environment |
> | Akce | Microsoft.LabServices/users/StartEnvironment/action | Starts an environment by starting all resources inside the environment. |
> | Akce | Microsoft.LabServices/users/StopEnvironment/action | Stops an environment by stopping all resources inside the environment |
> | Akce | Microsoft.LabServices/users/UserSettings/action | Updates and returns personal user settings. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/delete | Deletes the agreement in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Gets the callback URL for agreement content in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/read | Reads the agreement in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/write | Creates or updates the agreement in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/delete | Deletes the assembly in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Gets the callback URL for assembly content in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/read | Reads the assembly in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/write | Creates or updates the assembly in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Deletes the batch configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Reads the batch configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Creates or updates the batch configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/certificates/delete | Deletes the certificate in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/certificates/read | Reads the certificate in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/certificates/write | Creates or updates the certificate in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/delete | Deletes the integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/join/action | Joins the Integration Account. |
> | Akce | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Gets the callback URL for integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Gets the keys in the key vault. |
> | Akce | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Logs the tracking events in the integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/delete | Deletes the map in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Gets the callback URL for map content in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/read | Reads the map in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/write | Creates or updates the map in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/delete | Deletes the partner in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Gets the callback URL for partner content in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/read | Reads the partner in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/write | Creates or updates the partner in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Reads the Integration Account log definitions. |
> | Akce | Microsoft.Logic/integrationAccounts/read | Reads the integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenerates the access key secrets. |
> | Akce | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/delete | Deletes the RosettaNet process configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/read | Reads the RosettaNet process configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/write | Creates or updates the  RosettaNet process configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/delete | Deletes the schema in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Gets the callback URL for schema content in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/read | Reads the schema in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/write | Creates or updates the schema in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/sessions/delete | Deletes the session in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/sessions/read | Reads the batch configuration in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/sessions/write | Creates or updates the session in integration account. |
> | Akce | Microsoft.Logic/integrationAccounts/write | Creates or updates the integration account. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/availableManagedApis/read | Reads the integration service environment available managed APIs. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/delete | Deletes the integration service environment. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/join/action | Joins the Integration Service Environment. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Reads the integration service environment managed API operation. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/join/action | Joins the Integration Service Environment Managed API. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/operationStatuses/read | Reads the integration service environment managed API operation statuses. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Reads the integration service environment managed API. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/write | Creates or updates the integration service environment managed API. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Reads the integration service environment operation statuses. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Reads the integration service environment metric definitions. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/read | Reads the integration service environment. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/write | Creates or updates the integration service environment. |
> | Akce | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Gets the workflow recommend operation groups. |
> | Akce | Microsoft.Logic/locations/workflows/validate/action | Validates the workflow. |
> | Akce | Microsoft.Logic/operations/read | Gets the operation. |
> | Akce | Microsoft.Logic/register/action | Registers the Microsoft.Logic resource provider for a given subscription. |
> | Akce | Microsoft.Logic/workflows/accessKeys/delete | Deletes the access key. |
> | Akce | Microsoft.Logic/workflows/accessKeys/list/action | Lists the access key secrets. |
> | Akce | Microsoft.Logic/workflows/accessKeys/read | Reads the access key. |
> | Akce | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenerates the access key secrets. |
> | Akce | Microsoft.Logic/workflows/accessKeys/write | Creates or updates the access key. |
> | Akce | Microsoft.Logic/workflows/delete | Deletes the workflow. |
> | Akce | Microsoft.Logic/workflows/detectors/read | Reads the workflow detector. |
> | Akce | Microsoft.Logic/workflows/disable/action | Disables the workflow. |
> | Akce | Microsoft.Logic/workflows/enable/action | Povolí pracovní postup. |
> | Akce | Microsoft.Logic/workflows/listCallbackUrl/action | Gets the callback URL for workflow. |
> | Akce | Microsoft.Logic/workflows/listSwagger/action | Gets the workflow swagger definitions. |
> | Akce | Microsoft.Logic/workflows/move/action | Moves Workflow from its existing subscription id, resource group, and/or name to a different subscription id, resource group, and/or name. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Reads the workflow diagnostic settings. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the workflow diagnostic setting. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Reads the workflow log definitions. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Reads the workflow metric definitions. |
> | Akce | Microsoft.Logic/workflows/read | Reads the workflow. |
> | Akce | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenerates the access key secrets. |
> | Akce | Microsoft.Logic/workflows/run/action | Starts a run of the workflow. |
> | Akce | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Gets the workflow run action expression traces. |
> | Akce | Microsoft.Logic/workflows/runs/actions/read | Reads the workflow run action. |
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Gets the workflow run action repetition expression traces. |
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/read | Reads the workflow run action repetition. |
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Reads the workflow run repetition action request history. |
> | Akce | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Reads the workflow run action request history. |
> | Akce | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Reads the workflow run action scope repetition. |
> | Akce | Microsoft.Logic/workflows/runs/cancel/action | Cancels the run of a workflow. |
> | Akce | Microsoft.Logic/workflows/runs/delete | Deletes a run of a workflow. |
> | Akce | Microsoft.Logic/workflows/runs/operations/read | Reads the workflow run operation status. |
> | Akce | Microsoft.Logic/workflows/runs/read | Reads the workflow run. |
> | Akce | Microsoft.Logic/workflows/suspend/action | Suspends the workflow. |
> | Akce | Microsoft.Logic/workflows/triggers/histories/read | Reads the trigger histories. |
> | Akce | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Resubmits the workflow trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Gets the callback URL for trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/read | Reads the trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/reset/action | Resets the trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/run/action | Executes the trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/setState/action | Sets the trigger state. |
> | Akce | Microsoft.Logic/workflows/validate/action | Validates the workflow. |
> | Akce | Microsoft.Logic/workflows/versions/read | Reads the workflow version. |
> | Akce | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Gets the callback URL for trigger. |
> | Akce | Microsoft.Logic/workflows/write | Creates or updates the workflow. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Move any Machine Learning Commitment Plan Association |
> | Akce | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Read any Machine Learning Commitment Plan Association |
> | Akce | Microsoft.MachineLearning/commitmentPlans/delete | Delete any Machine Learning Commitment Plan |
> | Akce | Microsoft.MachineLearning/commitmentPlans/join/action | Join any Machine Learning Commitment Plan |
> | Akce | Microsoft.MachineLearning/commitmentPlans/read | Read any Machine Learning Commitment Plan |
> | Akce | Microsoft.MachineLearning/commitmentPlans/write | Create or Update any Machine Learning Commitment Plan |
> | Akce | Microsoft.MachineLearning/locations/operationresults/read | Get result of a Machine Learning Operation |
> | Akce | Microsoft.MachineLearning/locations/operationsstatus/read | Get status of an ongoing Machine Learning Operation |
> | Akce | Microsoft.MachineLearning/operations/read | Get Machine Learning Operations |
> | Akce | Microsoft.MachineLearning/register/action | Registers the subscription for the machine learning web service resource provider and enables the creation of web services. |
> | Akce | Microsoft.MachineLearning/skus/read | Get Machine Learning Commitment Plan SKUs |
> | Akce | Microsoft.MachineLearning/webServices/action | Create regional Web Service Properties for supported regions |
> | Akce | Microsoft.MachineLearning/webServices/delete | Delete any Machine Learning Web Service |
> | Akce | Microsoft.MachineLearning/webServices/listkeys/read | Get keys to a Machine Learning Web Service |
> | Akce | Microsoft.MachineLearning/webServices/read | Read any Machine Learning Web Service |
> | Akce | Microsoft.MachineLearning/webServices/write | Create or Update any Machine Learning Web Service |
> | Akce | Microsoft.MachineLearning/Workspaces/delete | Delete any Machine Learning Workspace |
> | Akce | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | List keys for a Machine Learning Workspace |
> | Akce | Microsoft.MachineLearning/Workspaces/read | Read any Machine Learning Workspace |
> | Akce | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Resync keys of storage account configured for a Machine Learning Workspace |
> | Akce | Microsoft.MachineLearning/Workspaces/write | Create or Update any Machine Learning Workspace |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Gets the status of a particular compute operation |
> | Akce | Microsoft.MachineLearningServices/locations/quotas/read | Gets the currently assigned Workspace Quotas based on VMFamily. |
> | Akce | Microsoft.MachineLearningServices/locations/updateQuotas/action | Update quota for each VM family in workspace. |
> | Akce | Microsoft.MachineLearningServices/locations/usages/read | Usage report for aml compute resources in a subscription |
> | Akce | Microsoft.MachineLearningServices/locations/vmsizes/read | Get supported vm sizes |
> | Akce | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Gets the status of a particular workspace operation |
> | Akce | Microsoft.MachineLearningServices/register/action | Registers the subscription for the Machine Learning Services Resource Provider |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/delete | Deletes the compute resources in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | List secrets for compute resources in Machine Learning Services Workspace |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | List nodes for compute resource in Machine Learning Services Workspace |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/read | Gets the compute resources in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/write | Creates or updates the compute resources in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/read | Gets data drift detectors in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/write | Creates or updates data drift detectors in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/registered/delete | Deletes registered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read | Gets dataset preview for registered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read | Gets dataset profiles for registered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/write | Creates or updates dataset profiles in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/registered/read | Gets registered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/registered/write | Creates or updates registered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete | Deletes unregistered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read | Gets dataset preview for unregistered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read | Gets dataset profiles for unregistered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/read | Gets unregistered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write | Creates or updates unregistered datasets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datastores/delete | Deletes datastores in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datastores/read | Gets datastores in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/datastores/write | Creates or updates datastores in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/delete | Deletes the Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read | Gets published pipelines and pipeline endpoints  in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write | Creates or updates published pipelines and pipeline endpoints in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/environments/build/action | Builds environments in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/environments/read | Gets environments in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/environments/readSecrets/action | Gets environments with secrets in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/environments/write | Creates or updates environments in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/eventGridFilters/read | Get an Event Grid filter for a particular workspace |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/delete | Deletes experiments in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/read | Gets experiments in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/runs/read | Gets runs in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/runs/scriptRun/submit/action | Creates or updates script runs in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/runs/write | Creates or updates runs in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/write | Creates or updates experiments in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/listKeys/action | List secrets for a Machine Learning Services Workspace |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/delete | Deletes artifacts in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read | Gets artifacts in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write | Creates or updates artifacts in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/delete | Deletes snapshots in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read | Gets snapshots in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write | Creates or updates snapshots in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/models/delete | Deletes models in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/models/download/action | Downloads models in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/models/package/action | Packages models in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/models/read | Gets models in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/models/write | Creates or updates models in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/modules/read | Gets modules in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/modules/write | Creates or updates module in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/delete | Deletes pipeline drafts in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/read | Gets pipeline drafts in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write | Creates or updates pipeline drafts in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/read | Gets the Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aci/delete | Deletes ACI services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aci/listkeys/action | Lists keys for ACI services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aci/write | Creates or updates ACI services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/delete | Deletes devtest AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/listkeys/action | Lists keys for devtest AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/score/action | Scores devtest AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/write | Creates or updates devtest AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/delete | Deletes prod AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/listkeys/action | Lists keys for prod AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/score/action | Scores prod AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/write | Creates or updates prod AKS services in Machine Learning Services Workspace(s) |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/services/read | Gets services in Machine Learning Services Workspace(s) |
> | Akce | Microsoft.MachineLearningServices/workspaces/write | Creates or updates a Machine Learning Services Workspace(s) |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ManagedIdentity/identities/read | Gets an existing system assigned identity |
> | Akce | Microsoft.ManagedIdentity/operations/read | Lists operations available on Microsoft.ManagedIdentity resource provider |
> | Akce | Microsoft.ManagedIdentity/register/action | Registers the subscription for the managed identity resource provider |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC action for assigning an existing user assigned identity to a resource |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Deletes an existing user assigned identity |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/read | Gets an existing user assigned identity |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/write | Creates a new user assigned identity or updates the tags associated with an existing user assigned identity |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ManagedServices/marketplaceRegistrationDefinitions/read | Retrieves a list of Managed Services registration definitions. |
> | Akce | Microsoft.ManagedServices/operations/read | Retrieves a list of Managed Services operations. |
> | Akce | Microsoft.ManagedServices/operationStatuses/read | Reads the operation status for the resource. |
> | Akce | Microsoft.ManagedServices/register/action | Register to Managed Services. |
> | Akce | Microsoft.ManagedServices/registrationAssignments/delete | Removes Managed Services registration assignment. |
> | Akce | Microsoft.ManagedServices/registrationAssignments/read | Retrieves a list of Managed Services registration assignments. |
> | Akce | Microsoft.ManagedServices/registrationAssignments/write | Add or modify Managed Services registration assignment. |
> | Akce | Microsoft.ManagedServices/registrationDefinitions/delete | Removes Managed Services registration definition. |
> | Akce | Microsoft.ManagedServices/registrationDefinitions/read | Retrieves a list of Managed Services registration definitions. |
> | Akce | Microsoft.ManagedServices/registrationDefinitions/write | Add or modify Managed Services registration definition. |
> | Akce | Microsoft.ManagedServices/unregister/action | Unregister from Managed Services. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Management/checkNameAvailability/action | Checks if the specified management group name is valid and unique. |
> | Akce | Microsoft.Management/getEntities/action | List all entities (Management Groups, Subscriptions, etc.) for the authenticated user. |
> | Akce | Microsoft.Management/managementGroups/delete | Delete management group. |
> | Akce | Microsoft.Management/managementGroups/descendants/read | Gets all the descendants (Management Groups, Subscriptions) of a Management Group. |
> | Akce | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | Akce | Microsoft.Management/managementGroups/subscriptions/delete | De-associates subscription from the management group. |
> | Akce | Microsoft.Management/managementGroups/subscriptions/write | Associates existing subscription with the management group. |
> | Akce | Microsoft.Management/managementGroups/write | Create or update a management group. |
> | Akce | Microsoft.Management/register/action | Register the specified subscription with Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce dataaction | Microsoft.Maps/accounts/data/read | Grants data read access to a maps account. |
> | Akce | Microsoft.Maps/accounts/delete | Delete a Maps Account. |
> | Akce | Microsoft.Maps/accounts/eventGridFilters/delete | Delete an Event Grid filter |
> | Akce | Microsoft.Maps/accounts/eventGridFilters/read | Get an Event Grid filter |
> | Akce | Microsoft.Maps/accounts/eventGridFilters/write | Create or update an Event Grid filter |
> | Akce | Microsoft.Maps/accounts/listKeys/action | List Maps Account keys |
> | Akce | Microsoft.Maps/accounts/read | Get a Maps Account. |
> | Akce | Microsoft.Maps/accounts/regenerateKey/action | Generate new Maps Account primary or secondary key |
> | Akce | Microsoft.Maps/accounts/write | Create or update a Maps Account. |
> | Akce | Microsoft.Maps/operations/read | Read the provider operations |
> | Akce | Microsoft.Maps/register/action | Register the provider |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Returns an Agreement. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepts a signed agreement. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Imports an image to the end user's ACR. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Returns a config. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Saves a config. |
> | Akce | Microsoft.Marketplace/register/action | Registers Microsoft.Marketplace resource provider in the subscription. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/delete | Does a DELETE operation on a classic dev service resource. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Gets a classic dev service resource management keys. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Gets the Single Sign On URL for a classic dev service. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/read | Does a GET operation on a classic dev service. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generates a classic dev service resource management keys. |
> | Akce | Microsoft.MarketplaceApps/Operations/read | Read the operations for all resource types. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancel an agreement for a given marketplace item |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Return an agreement for a given marketplace item |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Sign an agreement for a given marketplace item |
> | Akce | Microsoft.MarketplaceOrdering/agreements/read | Return all agreements under given subscription |
> | Akce | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Get an agreement for a given marketplace virtual machine item |
> | Akce | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Sign or Cancel an agreement for a given marketplace virtual machine item |
> | Akce | Microsoft.MarketplaceOrdering/operations/read | List all possible operations in the API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Media/checknameavailability/action | Checks if a Media Services account name is available |
> | Akce | Microsoft.Media/mediaservices/accountfilters/delete | Delete any Account Filter |
> | Akce | Microsoft.Media/mediaservices/accountfilters/read | Read any Account Filter |
> | Akce | Microsoft.Media/mediaservices/accountfilters/write | Create or Update any Account Filter |
> | Akce | Microsoft.Media/mediaservices/assets/assetfilters/delete | Delete any Asset Filter |
> | Akce | Microsoft.Media/mediaservices/assets/assetfilters/read | Read any Asset Filter |
> | Akce | Microsoft.Media/mediaservices/assets/assetfilters/write | Create or Update any Asset Filter |
> | Akce | Microsoft.Media/mediaservices/assets/delete | Delete any Asset |
> | Akce | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Get Asset Encryption Key |
> | Akce | Microsoft.Media/mediaservices/assets/listContainerSas/action | List Asset Container SAS URLs |
> | Akce | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | List Streaming Locators for Asset |
> | Akce | Microsoft.Media/mediaservices/assets/read | Read any Asset |
> | Akce | Microsoft.Media/mediaservices/assets/write | Create or Update any Asset |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Delete any Content Key Policy |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Get Policy Properties With Secrets |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/read | Read any Content Key Policy |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/write | Create or Update any Content Key Policy |
> | Akce | Microsoft.Media/mediaservices/delete | Delete any Media Services Account |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/delete | Delete any Event Grid Filter |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/read | Read any Event Grid Filter |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/write | Create or Update any Event Grid Filter |
> | Akce | Microsoft.Media/mediaservices/liveEventOperations/read | Read any Live Event Operation |
> | Akce | Microsoft.Media/mediaservices/liveEvents/delete | Delete any Live Event |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Delete any Live Output |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Read any Live Output |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Create or Update any Live Output |
> | Akce | Microsoft.Media/mediaservices/liveEvents/read | Read any Live Event |
> | Akce | Microsoft.Media/mediaservices/liveEvents/reset/action | Reset any Live Event Operation |
> | Akce | Microsoft.Media/mediaservices/liveEvents/start/action | Start any Live Event Operation |
> | Akce | Microsoft.Media/mediaservices/liveEvents/stop/action | Stop any Live Event Operation |
> | Akce | Microsoft.Media/mediaservices/liveEvents/write | Create or Update any Live Event |
> | Akce | Microsoft.Media/mediaservices/liveOutputOperations/read | Read any Live Output Operation |
> | Akce | Microsoft.Media/mediaservices/read | Read any Media Services Account |
> | Akce | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Read any Streaming Endpoint Operation |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/delete | Delete any Streaming Endpoint |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/read | Read any Streaming Endpoint |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Scale any Streaming Endpoint Operation |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Start any Streaming Endpoint Operation |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Stop any Streaming Endpoint Operation |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/write | Create or Update any Streaming Endpoint |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/delete | Delete any Streaming Locator |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | List Content Keys |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | List Paths |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/read | Read any Streaming Locator |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/write | Create or Update any Streaming Locator |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/delete | Delete any Streaming Policy |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/read | Read any Streaming Policy |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/write | Create or Update any Streaming Policy |
> | Akce | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronize the Storage Keys for an attached Azure Storage account |
> | Akce | Microsoft.Media/mediaservices/transforms/delete | Delete any Transform |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Zrušit úlohu |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/delete | Delete any Job |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/read | Read any Job |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/write | Create or Update any Job |
> | Akce | Microsoft.Media/mediaservices/transforms/read | Read any Transform |
> | Akce | Microsoft.Media/mediaservices/transforms/write | Create or Update any Transform |
> | Akce | Microsoft.Media/mediaservices/write | Create or Update any Media Services Account |
> | Akce | Microsoft.Media/operations/read | Get Available Operations |
> | Akce | Microsoft.Media/register/action | Registers the subscription for the Media Services resource provider and enables the creation of Media Services accounts |
> | Akce | Microsoft.Media/unregister/action | Unregisters the subscription for the Media Services resource provider |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Akce | Microsoft.Migrate/assessmentprojects/assessments/read | Lists assessments within a project |
> | Akce | Microsoft.Migrate/assessmentprojects/delete | Deletes the assessment project |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Deletes an assessment |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Gets the properties of an assessment |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/delete | Deletes a group |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/read | Get the properties of a group |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Update group by adding or removing machines |
> | Akce | Microsoft.Migrate/assessmentprojects/groups/write | Creates a new group or updates an existing group |
> | Akce | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Deletes the HyperV collector |
> | Akce | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Gets the properties of HyperV collector |
> | Akce | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Creates a new HyperV collector or updates an existing HyperV collector |
> | Akce | Microsoft.Migrate/assessmentprojects/importcollectors/delete | Deletes the Import collector |
> | Akce | Microsoft.Migrate/assessmentprojects/importcollectors/read | Gets the properties of Import collector |
> | Akce | Microsoft.Migrate/assessmentprojects/importcollectors/write | Creates a new Import collector or updates an existing Import collector |
> | Akce | Microsoft.Migrate/assessmentprojects/machines/read | Gets the properties of a machine |
> | Akce | Microsoft.Migrate/assessmentprojects/read | Gets the properties of assessment project |
> | Akce | Microsoft.Migrate/assessmentprojects/servercollectors/read | Gets the properties of Server collector |
> | Akce | Microsoft.Migrate/assessmentprojects/servercollectors/write | Creates a new Server collector or updates an existing Server collector |
> | Akce | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Deletes the VMware collector |
> | Akce | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Gets the properties of VMware collector |
> | Akce | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Creates a new VMware collector or updates an existing VMware collector |
> | Akce | Microsoft.Migrate/assessmentprojects/write | Creates a new assessment project or updates an existing assessment project |
> | Akce | Microsoft.Migrate/locations/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Akce | Microsoft.Migrate/locations/checknameavailability/action | Checks availability of the resource name for the given subscription in the given location |
> | Akce | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Gets the properties of a database instance |
> | Akce | Microsoft.Migrate/migrateprojects/Databases/read | Gets the properties of a database |
> | Akce | Microsoft.Migrate/migrateprojects/delete | Deletes a migrate project |
> | Akce | Microsoft.Migrate/migrateprojects/machines/read | Gets the properties of a machine |
> | Akce | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Deletes a migrate event |
> | Akce | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Gets the properties of a migrate events. |
> | Akce | Microsoft.Migrate/migrateprojects/read | Gets the properties of migrate project |
> | Akce | Microsoft.Migrate/migrateprojects/RefreshSummary/action | Refreshes the migrate project summary |
> | Akce | Microsoft.Migrate/migrateprojects/registerTool/action | Registers tool to a migrate project |
> | Akce | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Clean up the migrate project solution data |
> | Akce | Microsoft.Migrate/migrateprojects/solutions/Delete | Deletes a  migrate project solution |
> | Akce | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Gets the migrate project solution configuration |
> | Akce | Microsoft.Migrate/migrateprojects/solutions/read | Gets the properties of migrate project solution |
> | Akce | Microsoft.Migrate/migrateprojects/solutions/write | Creates a new migrate project solution or updates an existing migrate project solution |
> | Akce | Microsoft.Migrate/migrateprojects/write | Creates a new migrate project or updates an existing migrate project |
> | Akce | Microsoft.Migrate/Operations/read | Lists operations available on Microsoft.Migrate resource provider |
> | Akce | Microsoft.Migrate/projects/assessments/read | Lists assessments within a project |
> | Akce | Microsoft.Migrate/projects/delete | Deletes the project |
> | Akce | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Akce | Microsoft.Migrate/projects/groups/assessments/delete | Deletes an assessment |
> | Akce | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Akce | Microsoft.Migrate/projects/groups/assessments/read | Gets the properties of an assessment |
> | Akce | Microsoft.Migrate/projects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Akce | Microsoft.Migrate/projects/groups/delete | Deletes a group |
> | Akce | Microsoft.Migrate/projects/groups/read | Get the properties of a group |
> | Akce | Microsoft.Migrate/projects/groups/write | Creates a new group or updates an existing group |
> | Akce | Microsoft.Migrate/projects/keys/action | Gets shared keys for the project |
> | Akce | Microsoft.Migrate/projects/machines/read | Gets the properties of a machine |
> | Akce | Microsoft.Migrate/projects/read | Gets the properties of a project |
> | Akce | Microsoft.Migrate/projects/write | Creates a new project or updates an existing project |
> | Akce | Microsoft.Migrate/register/action | Registers Subscription with Microsoft.Migrate resource provider |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MixedReality/register/action | Registers a subscription for the Mixed Reality resource provider. |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Create spatial anchors |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Delete spatial anchors |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Akce | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Akce | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Akce | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Update spatial anchors properties |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.NetApp/locations/checkfilepathavailability/action | Check if file path is available |
> | Akce | Microsoft.NetApp/locations/checknameavailability/action | Check if resource name is available |
> | Akce | Microsoft.NetApp/locations/operationresults/read | Reads an operation result resource. |
> | Akce | Microsoft.NetApp/locations/read | Reads an availability check resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | Deletes a backup policy resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/backupPolicies/read | Reads a backup policy resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/backupPolicies/write | Writes a backup policy resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Deletes a pool resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/read | Reads a pool resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | Deletes a backup resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | Reads a backup resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | Writes a backup resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/break/action | Break volume replication relations |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Deletes a volume resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Reads a mount target resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Reads a volume resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | Reads the statuses of the Volume Replication. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/revert/action | Revert volume to specific snapshot |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Deletes a snapshot resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Reads a snapshot resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Writes a snapshot resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Writes a volume resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/write | Writes a pool resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/delete | Deletes an account resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/read | Reads an account resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/vaults/read | Reads a vault resource. |
> | Akce | Microsoft.NetApp/netAppAccounts/write | Writes an account resource. |
> | Akce | Microsoft.NetApp/Operations/read | Reads an operation resources. |
> | Akce | Microsoft.NetApp/register/action | Registers Subscription with Microsoft.NetApp resource provider |
> | Akce | Microsoft.NetApp/unregister/action | Unregisters Subscription with Microsoft.NetApp resource provider |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Get Application Gateway available Request Headers |
> | Akce | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Get Application Gateway available Response Header |
> | Akce | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Get Application Gateway available Server Variables |
> | Akce | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl Predefined Policy |
> | Akce | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway available Ssl Options |
> | Akce | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Gets Application Gateway Available Waf Rule Sets |
> | Akce | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joins an application gateway backend address pool. Nelze upozorňovat. |
> | Akce | Microsoft.Network/applicationGateways/backendhealth/action | Gets an application gateway backend health |
> | Akce | Microsoft.Network/applicationGateways/delete | Deletes an application gateway |
> | Akce | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | Gets an application gateway backend health on demand for given http setting and backend pool |
> | Akce | Microsoft.Network/applicationGateways/read | Gets an application gateway |
> | Akce | Microsoft.Network/applicationGateways/start/action | Starts an application gateway |
> | Akce | Microsoft.Network/applicationGateways/stop/action | Stops an application gateway |
> | Akce | Microsoft.Network/applicationGateways/write | Creates an application gateway or updates an application gateway |
> | Akce | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Deletes an Application Gateway WAF policy |
> | Akce | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Gets an Application Gateway WAF policy |
> | Akce | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Creates an Application Gateway WAF policy or updates an Application Gateway WAF policy |
> | Akce | Microsoft.Network/applicationSecurityGroups/delete | Deletes an Application Security Group |
> | Akce | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Joins an IP Configuration to Application Security Groups. Nelze upozorňovat. |
> | Akce | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Joins a Security Rule to Application Security Groups. Nelze upozorňovat. |
> | Akce | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Lists IP Configurations in the ApplicationSecurityGroup |
> | Akce | Microsoft.Network/applicationSecurityGroups/read | Gets an Application Security Group ID. |
> | Akce | Microsoft.Network/applicationSecurityGroups/write | Creates an Application Security Group, or updates an existing Application Security Group. |
> | Akce | Microsoft.Network/azureFirewallFqdnTags/read | Gets Azure Firewall FQDN Tags |
> | Akce | Microsoft.Network/azurefirewalls/delete | Delete Azure Firewall |
> | Akce | Microsoft.Network/azurefirewalls/read | Get Azure Firewall |
> | Akce | Microsoft.Network/azurefirewalls/write | Creates or updates an Azure Firewall |
> | Akce | Microsoft.Network/bastionHosts/createbsl/action | Creates shareable urls for the VMs under a bastion and returns the urls |
> | Akce | Microsoft.Network/bastionHosts/delete | Deletes a Bastion Host |
> | Akce | Microsoft.Network/bastionHosts/deletebsl/action | Deletes shareable urls for the provided VMs under a bastion |
> | Akce | Microsoft.Network/bastionHosts/disconnectactivesessions/action | Disconnect given Active Sessions in the Bastion Host |
> | Akce | Microsoft.Network/bastionHosts/getactivesessions/action | Get Active Sessions in the Bastion Host |
> | Akce | Microsoft.Network/bastionHosts/getbsl/action | Returns the shareable urls for the specified VMs in a Bastion subnet provided their urls are created |
> | Akce | Microsoft.Network/bastionHosts/read | Gets a Bastion Host |
> | Akce | Microsoft.Network/bastionHosts/write | Create or Update a Bastion Host |
> | Akce | Microsoft.Network/bgpServiceCommunities/read | Get Bgp Service Communities |
> | Akce | Microsoft.Network/checkFrontDoorNameAvailability/action | Checks whether a Front Door name is available |
> | Akce | Microsoft.Network/checkTrafficManagerNameAvailability/action | Checks the availability of a Traffic Manager Relative DNS name. |
> | Akce | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/revoke/action | Marks an Express Route Connection status as Revoked |
> | Akce | Microsoft.Network/connections/sharedkey/action | Get VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft. Network/Connections/sharedKey/Write | Vytvoří nebo aktualizuje existující SharedKey VirtualNetworkGatewayConnection. |
> | Akce | Microsoft. Network/Connections/startpacketcapture/Action | Spustí zachytávání paketů připojení Virtual Network brány. |
> | Akce | Microsoft. Network/Connections/stoppacketcapture/Action | Zastaví zachytávání paketu připojení Virtual Network brány. |
> | Akce | Microsoft. Network/Connections/vpndeviceconfigurationscript/Action | Načte konfiguraci zařízení VPN pro VirtualNetworkGatewayConnection. |
> | Akce | Microsoft. Network/Connections/Write | Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection. |
> | Akce | Microsoft. Network/ddosCustomPolicies/DELETE | Odstraní přizpůsobené zásady DDoS. |
> | Akce | Microsoft. Network/ddosCustomPolicies/Read | Načte definici definice vlastní zásady DDoS. |
> | Akce | Microsoft. Network/ddosCustomPolicies/Write | Vytvoří vlastní zásadu DDoS nebo aktualizuje stávající zásady DDoS. |
> | Akce | Microsoft.Network/ddosProtectionPlans/delete | Odstraní plán DDoS Protection. |
> | Akce | Microsoft.Network/ddosProtectionPlans/join/action | Připojí DDoS Protection plán. Nelze upozorňovat. |
> | Akce | Microsoft.Network/ddosProtectionPlans/read | Získá plán DDoS Protection. |
> | Akce | Microsoft.Network/ddosProtectionPlans/write | Vytvoří plán DDoS Protection nebo aktualizuje plán DDoS Protection.  |
> | Akce | Microsoft. Network/dnsoperationresults/Read | Získá výsledky operace DNS. |
> | Akce | Microsoft.Network/dnsoperationstatuses/read | Získá stav operace DNS.  |
> | Akce | Microsoft.Network/dnszones/A/delete | Odebere sadu záznamů daného názvu a zapíše ' A ' ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/A/read | Získá sadu záznamů typu A ve formátu JSON. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft. Network/dnszones/A/Write | Vytvoří nebo aktualizuje sadu záznamů typu A v rámci zóny DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/dnszones/AAAA/delete | Odebere sadu záznamů daného názvu a zapíše "AAAA" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/AAAA/read | Získá sadu záznamů typu AAAA ve formátu JSON. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/dnszones/AAAA/write | Vytvoří nebo aktualizuje sadu záznamů typu AAAA v zóně DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft. Network/dnszones/All/Read | Načte sady záznamů DNS napříč typy. |
> | Akce | Microsoft.Network/dnszones/CAA/delete | Odeberte sadu záznamů daného názvu a zadejte ' CAA ' ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/CAA/read | Získá sadu záznamů typu ' CAA ' ve formátu JSON. Sada záznamů obsahuje hodnoty TTL, Tags a ETag. |
> | Akce | Microsoft.Network/dnszones/CAA/write | Vytvoří nebo aktualizuje sadu záznamů typu ' CAA ' v zóně DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/dnszones/CNAME/delete | Odebere sadu záznamů daného názvu a zadáte "CNAME" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/CNAME/read | Získá sadu záznamů typu CNAME ve formátu JSON. Sada záznamů obsahuje hodnoty TTL, Tags a ETag. |
> | Akce | Microsoft.Network/dnszones/CNAME/write | Vytvoří nebo aktualizuje sadu záznamů typu CNAME v zóně DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/dnszones/delete | Odstraňte zónu DNS ve formátu JSON. Mezi vlastnosti zóny patří značky, ETag, numberOfRecordSets a maxNumberOfRecordSets. |
> | Akce | Microsoft.Network/dnszones/MX/delete | Odebere sadu záznamů daného názvu a v zóně DNS typ MX. |
> | Akce | Microsoft.Network/dnszones/MX/read | Získá sadu záznamů typu MX ve formátu JSON. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/dnszones/MX/write | Vytvoří nebo aktualizuje sadu záznamů typu MX v zóně DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft. Network/dnszones/NS/DELETE | Odstraní sadu záznamů DNS typu NS. |
> | Akce | Microsoft. Network/dnszones/NS/Read | Načte sadu záznamů DNS typu NS. |
> | Akce | Microsoft. Network/dnszones/NS/Write | Vytvoří nebo aktualizuje sadu záznamů DNS typu NS. |
> | Akce | Microsoft. Network/dnszones/PTR/DELETE | Odebere sadu záznamů daného názvu a typ PTR ze zóny DNS. |
> | Akce | Microsoft. Network/dnszones/PTR/Read | Získá sadu záznamů typu PTR ve formátu JSON. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft. Network/dnszones/PTR/Write | Vytvoří nebo aktualizuje sadu záznamů typu PTR v zóně DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/dnszones/read | Získejte zónu DNS ve formátu JSON. Mezi vlastnosti zóny patří značky, ETag, numberOfRecordSets a maxNumberOfRecordSets. Všimněte si, že tento příkaz nenačte sady záznamů obsažené v zóně. |
> | Akce | Microsoft.Network/dnszones/recordsets/read | Načte sady záznamů DNS napříč typy. |
> | Akce | Microsoft.Network/dnszones/SOA/read | Načte sadu záznamů DNS typu SOA. |
> | Akce | Microsoft.Network/dnszones/SOA/write | Vytvoří nebo aktualizuje sadu záznamů DNS typu SOA. |
> | Akce | Microsoft.Network/dnszones/SRV/delete | Odebere sadu záznamů daného názvu a zadáte ' SRV ' ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/SRV/read | Získá sadu záznamů typu SRV ve formátu JSON. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/dnszones/SRV/write | Vytvořit nebo aktualizovat sadu záznamů typu SRV |
> | Akce | Microsoft.Network/dnszones/TXT/delete | Odeberte sadu záznamů daného názvu a zadejte ' TXT ' ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/TXT/read | Získá sadu záznamů typu TXT ve formátu JSON. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/dnszones/TXT/write | Vytvoří nebo aktualizuje sadu záznamů typu TXT v zóně DNS. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/dnszones/write | Vytvoří nebo aktualizuje zónu DNS v rámci skupiny prostředků.  Používá se k aktualizaci značek v prostředku zóny DNS. Všimněte si, že tento příkaz nelze použít k vytvoření nebo aktualizaci sad záznamů v rámci zóny. |
> | Akce | Microsoft. Network/expressRouteCircuits/Authorizations/DELETE | Odstraní autorizaci ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCircuits/autorizace/číst | Získá autorizaci ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCircuits/Authorizations/Write | Vytvoří nebo aktualizuje existující autorizaci ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCircuits/DELETE | Odstraní ExpressRoute |
> | Akce | Microsoft. Network/expressRouteCircuits/JOIN/Action | Spojí okruh Express Route. Nelze upozorňovat. |
> | Akce | Microsoft. Network/expressRouteCircuits/peering/arpTables/Read | Získá ArpTable partnerského vztahu ExpressRoute. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Odstraní připojení ExpressRoute. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Získá připojení ExpressRoute. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Vytvoří nebo aktualizuje existující prostředek připojení ExpressRoute. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/delete | Odstraní partnerský vztah ExpressRoute. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Získá připojení okruhu peer Express Route. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/read | Získá partnerský vztah ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCircuits/peering/routeTables/Read | Získá trasu partnerského vztahu ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCircuits/peering/routeTablesSummary/Read | Načte souhrn ExpressRoute partnerských vztahů. |
> | Akce | Microsoft. Network/expressRouteCircuits/peering/stats/Read | Získá statu partnerského vztahu ExpressRoute. |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/write | Vytvoří nebo aktualizuje existující partnerský vztah ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCircuits/Read | Získat ExpressRoute |
> | Akce | Microsoft. Network/expressRouteCircuits/stats/Read | Načte ExpressRoute stat. |
> | Akce | Microsoft.Network/expressRouteCircuits/write | Vytvoří nebo aktualizuje existující ExpressRoute. |
> | Akce | Microsoft. Network/expressRouteCrossConnections/JOIN/Action | Připojí mezi připojení Express Route. Nelze upozorňovat. |
> | Akce | Microsoft. Network/expressRouteCrossConnections/peering/arpTables/Read | Načte tabulku protokolu ARP vzájemného propojení Express Route. |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Odstraní partnerský vztah vzájemného propojení Express Route. |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/read | Získá partnerský vztah vzájemného propojení Express Route. |
> | Akce | Microsoft. Network/expressRouteCrossConnections/peering/routeTables/Read | Načte tabulku směrování partnerských vztahů mezi připojeními Express Route. |
> | Akce | Microsoft. Network/expressRouteCrossConnections/peering/routeTableSummary/Read | Získá Souhrn tabulky směrování vzájemného propojení Express Route. |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/write | Vytvoří partnerský vztah mezi propojením Express Route nebo aktualizuje existující partnerský vztah mezi propojením Express Route. |
> | Akce | Microsoft. Network/expressRouteCrossConnections/Read | Získat vzájemné propojení Express Route |
> | Akce | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Odstraní připojení Express Route. |
> | Akce | Microsoft. Network/expressRouteGateways/expressRouteConnections/Read | Získá připojení Express Route. |
> | Akce | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Vytvoří připojení Express Route nebo aktualizuje existující připojení Express Route. |
> | Akce | Microsoft. Network/expressRouteGateways/JOIN/Action | Připojí bránu Express Route. Nelze upozorňovat. |
> | Akce | Microsoft. Network/expressRouteGateways/Read | Získat bránu Express Route |
> | Akce | Microsoft.Network/expressRoutePorts/delete | Odstraní ExpressRoutePorts |
> | Akce | Microsoft. Network/expressRoutePorts/JOIN/Action | Připojí porty Express Route. Nelze upozorňovat. |
> | Akce | Microsoft. Network/expressRoutePorts/propojuje/číst | Získá ExpressRouteLink |
> | Akce | Microsoft. Network/expressRoutePorts/Read | Získá ExpressRoutePorts |
> | Akce | Microsoft.Network/expressRoutePorts/write | Vytvoří nebo aktualizuje ExpressRoutePorts |
> | Akce | Microsoft. Network/expressRoutePortsLocations/Read | Získat umístění portů Express Route |
> | Akce | Microsoft.Network/expressRouteServiceProviders/read | Získá poskytovatele expresního Route Service. |
> | Akce | Microsoft. Network/firewallPolicies/DELETE | Odstraní zásady brány firewall. |
> | Akce | Microsoft. Network/firewallPolicies/JOIN/Action | Připojí se k zásadě brány firewall. Nelze upozorňovat. |
> | Akce | Microsoft. Network/firewallPolicies/Read | Načte zásady brány firewall. |
> | Akce | Microsoft. Network/firewallPolicies/ruleGroups/DELETE | Odstraní skupinu pravidel zásad brány firewall. |
> | Akce | Microsoft. Network/firewallPolicies/ruleGroups/Read | Načte skupinu pravidel zásad brány firewall. |
> | Akce | Microsoft. Network/firewallPolicies/ruleGroups/Write | Vytvoří skupinu pravidel zásad brány firewall nebo aktualizuje stávající skupinu pravidel zásad brány firewall. |
> | Akce | Microsoft. Network/firewallPolicies/Write | Vytvoří zásadu brány firewall nebo aktualizuje stávající zásady brány firewall. |
> | Akce | Microsoft.Network/frontDoors/backendPools/delete | Odstraní back-end fond. |
> | Akce | Microsoft. Network/frontDoors/backendPools/Read | Načte back-end fond. |
> | Akce | Microsoft.Network/frontDoors/backendPools/write | Vytvoří nebo aktualizuje back-end fond. |
> | Akce | Microsoft.Network/frontDoors/delete | Odstraní přední dveře. |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/delete | Odstraní koncový bod front-endu. |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Zakáže HTTPS u koncového bodu front-endu. |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Povolí HTTPS u koncového bodu front-endu. |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/read | Načte koncový bod front-endu. |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/write | Vytvoří nebo aktualizuje koncový bod front-endu. |
> | Akce | Microsoft.Network/frontDoors/healthProbeSettings/delete | Odstraní nastavení sondy stavu. |
> | Akce | Microsoft.Network/frontDoors/healthProbeSettings/read | Načte nastavení sondy stavu. |
> | Akce | Microsoft.Network/frontDoors/healthProbeSettings/write | Vytvoří nebo aktualizuje nastavení sondy stavu. |
> | Akce | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Vytvoří nebo aktualizuje nastavení vyrovnávání zatížení. |
> | Akce | Microsoft.Network/frontDoors/loadBalancingSettings/read | Načte nastavení vyrovnávání zatížení. |
> | Akce | Microsoft.Network/frontDoors/loadBalancingSettings/write | Vytvoří nebo aktualizuje nastavení vyrovnávání zatížení. |
> | Akce | Microsoft. Network/frontDoors/vyprázdnění/akce | Vyprázdnit obsah uložený v mezipaměti z přední dveře |
> | Akce | Microsoft. Network/frontDoors/Read | Načte přední dveře. |
> | Akce | Microsoft.Network/frontDoors/routingRules/delete | Odstraní pravidlo směrování. |
> | Akce | Microsoft.Network/frontDoors/routingRules/read | Získá pravidlo směrování. |
> | Akce | Microsoft.Network/frontDoors/routingRules/write | Vytvoří nebo aktualizuje pravidlo směrování. |
> | Akce | Microsoft. Network/frontDoors/validateCustomDomain/Action | Ověří koncový bod front-endu pro přední dveře. |
> | Akce | Microsoft.Network/frontDoors/write | Vytvoří nebo aktualizuje přední dveře. |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Načte sady pravidel spravované přes bránu firewall webových aplikací. |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Odstraní zásady firewallu webových aplikací. |
> | Akce | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/JOIN/Action | Připojí zásady firewallu webových aplikací. Nelze upozorňovat. |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Získá zásady firewallu webových aplikací. |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Vytvoří nebo aktualizuje zásady firewallu webových aplikací. |
> | Akce | Microsoft. Network/ipGroups/DELETE | Odstraní IpGroup |
> | Akce | Microsoft. Network/ipGroups/JOIN/Action | Připojí se k IpGroup. Nelze upozorňovat. |
> | Akce | Microsoft. Network/ipGroups/Read | Načte IpGroup |
> | Akce | Microsoft. Network/ipGroups/Write | Vytvoří IpGroup nebo aktualizuje existující IpGroups. |
> | Akce | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Připojí fond adres back-endu nástroje pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Akce | Microsoft. Network/loadBalancers/backendAddressPools/Read | Získá definici fondu adres back-endu nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft. Network/loadBalancers/DELETE | Deletes a load balancer |
> | Akce | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Joins a Load Balancer Frontend IP Configuration. Nelze upozorňovat. |
> | Akce | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Gets a load balancer frontend IP configuration definition |
> | Akce | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joins a load balancer inbound NAT pool. Nelze upozorňovat. |
> | Akce | Microsoft.Network/loadBalancers/inboundNatPools/read | Gets a load balancer inbound nat pool definition |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/delete | Deletes a load balancer inbound nat rule |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joins a load balancer inbound nat rule. Nelze upozorňovat. |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/read | Gets a load balancer inbound nat rule definition |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/write | Creates a load balancer inbound nat rule or updates an existing load balancer inbound nat rule |
> | Akce | Microsoft.Network/loadBalancers/loadBalancingRules/read | Gets a load balancer load balancing rule definition |
> | Akce | Microsoft.Network/loadBalancers/networkInterfaces/read | Gets references to all the network interfaces under a load balancer |
> | Akce | Microsoft.Network/loadBalancers/outboundRules/read | Gets a load balancer outbound rule definition |
> | Akce | Microsoft.Network/loadBalancers/probes/join/action | Allows using probes of a load balancer. For example, with this permission healthProbe property of VM scale set can reference the probe. Nelze upozorňovat. |
> | Akce | Microsoft.Network/loadBalancers/probes/read | Gets a load balancer probe |
> | Akce | Microsoft.Network/loadBalancers/read | Gets a load balancer definition |
> | Akce | Microsoft.Network/loadBalancers/virtualMachines/read | Gets references to all the virtual machines under a load balancer |
> | Akce | Microsoft.Network/loadBalancers/write | Creates a load balancer or updates an existing load balancer |
> | Akce | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Akce | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Akce | Microsoft.Network/localnetworkgateways/write | Creates or updates an existing LocalNetworkGateway |
> | Akce | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Gets Auto Approved Private Link Services |
> | Akce | Microsoft.Network/locations/availableDelegations/read | Gets Available Delegations |
> | Akce | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Gets available Private Endpoint resources |
> | Akce | Microsoft.Network/locations/availableServiceAliases/read | Gets Available Service Aliases |
> | Akce | Microsoft.Network/locations/bareMetalTenants/action | Allocates or validates a Bare Metal Tenant |
> | Akce | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Checks Accelerated Networking support |
> | Akce | Microsoft.Network/locations/checkDnsNameAvailability/read | Checks if dns label is available at the specified location |
> | Akce | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Checks Private Link Service Visibility |
> | Akce | Microsoft.Network/locations/operationResults/read | Gets operation result of an async POST or DELETE operation |
> | Akce | Microsoft.Network/locations/operations/read | Gets operation resource that represents status of an asynchronous operation |
> | Akce | Microsoft.Network/locations/serviceTags/read | Get Service Tags |
> | Akce | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Gets supported virtual machines sizes |
> | Akce | Microsoft.Network/locations/usages/read | Gets the resources usage metrics |
> | Akce | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Gets a list of available Virtual Network Endpoint Services |
> | Akce | Microsoft.Network/networkIntentPolicies/delete | Deletes an Network Intent Policy |
> | Akce | Microsoft.Network/networkIntentPolicies/read | Gets an Network Intent Policy Description |
> | Akce | Microsoft.Network/networkIntentPolicies/write | Creates an Network Intent Policy or updates an existing Network Intent Policy |
> | Akce | Microsoft.Network/networkInterfaces/delete | Deletes a network interface |
> | Akce | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Get Network Security Groups configured On Network Interface Of The Vm |
> | Akce | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Get Route Table configured On Network Interface Of The Vm |
> | Akce | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Joins a Network Interface IP Configuration. Nelze upozorňovat. |
> | Akce | Microsoft.Network/networkInterfaces/ipconfigurations/read | Gets a network interface ip configuration definition.  |
> | Akce | Microsoft.Network/networkInterfaces/join/action | Joins a Virtual Machine to a network interface. Nelze upozorňovat. |
> | Akce | Microsoft.Network/networkInterfaces/loadBalancers/read | Gets all the load balancers that the network interface is part of |
> | Akce | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Akce | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Deletes a Network Interface Tap Configuration. |
> | Akce | Microsoft.Network/networkInterfaces/tapConfigurations/read | Gets a Network Interface Tap Configuration. |
> | Akce | Microsoft.Network/networkInterfaces/tapConfigurations/write | Creates a Network Interface Tap Configuration or updates an existing Network Interface Tap Configuration. |
> | Akce | Microsoft.Network/networkInterfaces/write | Creates a network interface or updates an existing network interface.  |
> | Akce | Microsoft.Network/networkProfiles/delete | Deletes a Network Profile |
> | Akce | Microsoft.Network/networkProfiles/read | Gets a Network Profile |
> | Akce | Microsoft.Network/networkProfiles/removeContainers/action | Removes Containers |
> | Akce | Microsoft.Network/networkProfiles/setContainers/action | Sets Containers |
> | Akce | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Sets Container Network Interfaces |
> | Akce | Microsoft.Network/networkProfiles/write | Creates or updates a Network Profile |
> | Akce | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Gets a default security rule definition |
> | Akce | Microsoft.Network/networkSecurityGroups/delete | Deletes a network security group |
> | Akce | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Nelze upozorňovat. |
> | Akce | Microsoft.Network/networkSecurityGroups/read | Gets a network security group definition |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/delete | Deletes a security rule |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/read | Gets a security rule definition |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/write | Creates a security rule or updates an existing security rule |
> | Akce | Microsoft.Network/networkSecurityGroups/write | Creates a network security group or updates an existing network security group |
> | Akce | Microsoft.Network/networkWatchers/availableProvidersList/action | Returns all available internet service providers for a specified Azure region. |
> | Akce | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returns the relative latency score for internet service providers from a specified location to Azure regions. |
> | Akce | Microsoft.Network/networkWatchers/configureFlowLog/action | Configures flow logging for a target resource. |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/delete | Deletes a Connection Monitor |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Query monitoring connectivity between specified endpoints |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/read | Get Connection Monitor details |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Start monitoring connectivity between specified endpoints |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Stop/pause monitoring connectivity between specified endpoints |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/write | Creates a Connection Monitor |
> | Akce | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifies the possibility of establishing a direct TCP connection from a virtual machine to a given endpoint including another VM or an arbitrary remote server. |
> | Akce | Microsoft.Network/networkWatchers/delete | Deletes a network watcher |
> | Akce | Microsoft.Network/networkWatchers/flowLogs/delete | Deletes a Flow Log |
> | Akce | Microsoft.Network/networkWatchers/flowLogs/read | Get Flow Log details |
> | Akce | Microsoft.Network/networkWatchers/flowLogs/write | Creates a Flow Log |
> | Akce | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returns whether the packet is allowed or denied to or from a particular destination. |
> | Akce | Microsoft.Network/networkWatchers/lenses/delete | Deletes a Lens |
> | Akce | Microsoft.Network/networkWatchers/lenses/query/action | Query monitoring network traffic on a specified endpoint |
> | Akce | Microsoft.Network/networkWatchers/lenses/read | Get Lens details |
> | Akce | Microsoft.Network/networkWatchers/lenses/start/action | Start monitoring network traffic on a specified endpoint |
> | Akce | Microsoft.Network/networkWatchers/lenses/stop/action | Stop/pause monitoring network traffic on a specified endpoint |
> | Akce | Microsoft.Network/networkWatchers/lenses/write | Creates a Lens |
> | Akce | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostic of network configuration. |
> | Akce | Microsoft.Network/networkWatchers/nextHop/action | For a specified target and destination IP address, return the next hop type and next hope IP address. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/delete | Deletes a packet capture |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Gets information about properties and status of a packet capture resource. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/read | Get the packet capture definition |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stop the running packet capture session. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/write | Creates a packet capture |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/delete | Deletes a PingMesh |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/read | Get PingMesh details |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/start/action | Start PingMesh between specified VMs |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Stop PingMesh between specified VMs |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/write | Creates a PingMesh |
> | Akce | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Batch query monitoring connectivity between specified endpoints |
> | Akce | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Gets the status of flow logging on a resource. |
> | Akce | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Gets the troubleshooting result from the previously run or currently running troubleshooting operation. |
> | Akce | Microsoft.Network/networkWatchers/read | Get the network watcher definition |
> | Akce | Microsoft.Network/networkWatchers/securityGroupView/action | View the configured and effective network security group rules applied on a VM. |
> | Akce | Microsoft.Network/networkWatchers/topology/action | Gets a network level view of resources and their relationships in a resource group. |
> | Akce | Microsoft.Network/networkWatchers/troubleshoot/action | Starts troubleshooting on a Networking resource in Azure. |
> | Akce | Microsoft.Network/networkWatchers/write | Creates a network watcher or updates an existing network watcher |
> | Akce | Microsoft.Network/operations/read | Get Available Operations |
> | Akce | Microsoft.Network/p2sVpnGateways/delete | Deletes a P2SVpnGateway. |
> | Akce | Microsoft.Network/p2sVpnGateways/disconnectp2svpnconnections/action | Disconnect p2s vpn connections |
> | Akce | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generate Vpn Profile for P2SVpnGateway |
> | Akce | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Gets a P2S Vpn Connection health for P2SVpnGateway |
> | Akce | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | Gets a P2S Vpn Connection health detailed for P2SVpnGateway |
> | Akce | Microsoft.Network/p2sVpnGateways/read | Gets a P2SVpnGateway. |
> | Akce | Microsoft.Network/p2sVpnGateways/write | Puts a P2SVpnGateway. |
> | Akce | Microsoft.Network/privateDnsOperationResults/read | Gets results of a Private DNS operation |
> | Akce | Microsoft.Network/privateDnsOperationStatuses/read | Gets status of a Private DNS operation |
> | Akce | Microsoft.Network/privateDnsZones/A/delete | Remove the record set of a given name and type ‘A’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/A/read | Get the record set of type ‘A’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/privateDnsZones/A/write | Create or update a record set of type ‘A’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/privateDnsZones/AAAA/delete | Remove the record set of a given name and type ‘AAAA’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/AAAA/read | Get the record set of type ‘AAAA’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/privateDnsZones/AAAA/write | Create or update a record set of type ‘AAAA’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/privateDnsZones/ALL/read | Gets Private DNS record sets across types |
> | Akce | Microsoft.Network/privateDnsZones/CNAME/delete | Remove the record set of a given name and type ‘CNAME’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/CNAME/read | Get the record set of type ‘CNAME’ within a Private DNS zone, in JSON format. |
> | Akce | Microsoft.Network/privateDnsZones/CNAME/write | Create or update a record set of type ‘CNAME’ within a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/delete | Delete a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/MX/delete | Remove the record set of a given name and type ‘MX’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/MX/read | Get the record set of type ‘MX’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/privateDnsZones/MX/write | Create or update a record set of type ‘MX’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/privateDnsZones/PTR/delete | Remove the record set of a given name and type ‘PTR’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/PTR/read | Get the record set of type ‘PTR’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/privateDnsZones/PTR/write | Create or update a record set of type ‘PTR’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/privateDnsZones/read | Get the Private DNS zone properties, in JSON format. Note that this command does not retrieve the virtual networks to which the Private DNS zone is linked or the record sets contained within the zone. |
> | Akce | Microsoft.Network/privateDnsZones/recordsets/read | Gets Private DNS record sets across types |
> | Akce | Microsoft.Network/privateDnsZones/SOA/read | Get the record set of type ‘SOA’ within a Private DNS zone, in JSON format. |
> | Akce | Microsoft.Network/privateDnsZones/SOA/write | Update a record set of type ‘SOA’ within a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/SRV/delete | Remove the record set of a given name and type ‘SRV’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/SRV/read | Get the record set of type ‘SRV’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/privateDnsZones/SRV/write | Create or update a record set of type ‘SRV’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/privateDnsZones/TXT/delete | Remove the record set of a given name and type ‘TXT’ from a Private DNS zone. |
> | Akce | Microsoft.Network/privateDnsZones/TXT/read | Get the record set of type ‘TXT’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Akce | Microsoft.Network/privateDnsZones/TXT/write | Create or update a record set of type ‘TXT’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Akce | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Delete a Private DNS zone link to virtual network. |
> | Akce | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Get the Private DNS zone link to virtual network properties, in JSON format. |
> | Akce | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Create or update a Private DNS zone link to virtual network. |
> | Akce | Microsoft.Network/privateDnsZones/write | Create or update a Private DNS zone within a resource group. Note that this command cannot be used to create or update virtual network links or record sets within the zone. |
> | Akce | Microsoft.Network/privateEndpointRedirectMaps/read | Gets a Private Endpoint RedirectMap |
> | Akce | Microsoft.Network/privateEndpointRedirectMaps/write | Creates Private Endpoint RedirectMap Or Updates An Existing Private Endpoint RedirectMap |
> | Akce | Microsoft.Network/privateEndpoints/delete | Deletes an private endpoint resource. |
> | Akce | Microsoft.Network/privateEndpoints/read | Gets an private endpoint resource. |
> | Akce | Microsoft.Network/privateEndpoints/write | Creates a new private endpoint, or updates an existing private endpoint. |
> | Akce | Microsoft.Network/privateLinkServices/delete | Deletes an private link service resource. |
> | Akce | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Deletes an private endpoint connection. |
> | Akce | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Gets an private endpoint connection definition. |
> | Akce | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Creates a new private endpoint connection, or updates an existing private endpoint connection. |
> | Akce | Microsoft.Network/privateLinkServices/read | Gets an private link service resource. |
> | Akce | Microsoft.Network/privateLinkServices/write | Creates a new private link service, or updates an existing private link service. |
> | Akce | Microsoft.Network/publicIPAddresses/delete | Deletes a public Ip address. |
> | Akce | Microsoft.Network/publicIPAddresses/join/action | Joins a public ip address. Nelze upozorňovat. |
> | Akce | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Akce | Microsoft.Network/publicIPAddresses/write | Creates a public Ip address or updates an existing public Ip address.  |
> | Akce | Microsoft.Network/publicIPPrefixes/delete | Deletes A Public Ip Prefix |
> | Akce | Microsoft.Network/publicIPPrefixes/join/action | Joins a PublicIPPrefix. Nelze upozorňovat. |
> | Akce | Microsoft.Network/publicIPPrefixes/read | Gets a Public Ip Prefix Definition |
> | Akce | Microsoft.Network/publicIPPrefixes/write | Creates A Public Ip Prefix Or Updates An Existing Public Ip Prefix |
> | Akce | Microsoft.Network/register/action | Registers the subscription |
> | Akce | Microsoft.Network/routeFilters/delete | Deletes a route filter definition |
> | Akce | Microsoft.Network/routeFilters/join/action | Joins a route filter. Nelze upozorňovat. |
> | Akce | Microsoft.Network/routeFilters/read | Gets a route filter definition |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/delete | Deletes a route filter rule definition |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/read | Gets a route filter rule definition |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/write | Creates a route filter rule or Updates an existing route filter rule |
> | Akce | Microsoft.Network/routeFilters/write | Creates a route filter or Updates an existing route filter |
> | Akce | Microsoft.Network/routeTables/delete | Deletes a route table definition |
> | Akce | Microsoft.Network/routeTables/join/action | Joins a route table. Nelze upozorňovat. |
> | Akce | Microsoft.Network/routeTables/read | Gets a route table definition |
> | Akce | Microsoft.Network/routeTables/routes/delete | Deletes a route definition |
> | Akce | Microsoft.Network/routeTables/routes/read | Gets a route definition |
> | Akce | Microsoft.Network/routeTables/routes/write | Creates a route or Updates an existing route |
> | Akce | Microsoft.Network/routeTables/write | Creates a route table or Updates an existing route table |
> | Akce | Microsoft.Network/serviceEndpointPolicies/delete | Deletes a Service Endpoint Policy |
> | Akce | Microsoft.Network/serviceEndpointPolicies/join/action | Joins a Service Endpoint Policy. Nelze upozorňovat. |
> | Akce | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Joins a Subnet To Service Endpoint Policies. Nelze upozorňovat. |
> | Akce | Microsoft.Network/serviceEndpointPolicies/read | Gets a Service Endpoint Policy Description |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Deletes a Service Endpoint Policy Definition |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Gets a Service Endpoint Policy Definition Description |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Creates a Service Endpoint Policy Definition or updates an existing Service Endpoint Policy Definition |
> | Akce | Microsoft.Network/serviceEndpointPolicies/write | Creates a Service Endpoint Policy or updates an existing Service Endpoint Policy |
> | Akce | Microsoft.Network/trafficManagerGeographicHierarchies/read | Gets the Traffic Manager Geographic Hierarchy containing regions which can be used with the Geographic traffic routing method |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Deletes an Azure Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted Azure Endpoint. |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Gets an Azure Endpoint which belongs to a Traffic Manager Profile, including all the properties of that Azure Endpoint. |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Add a new Azure Endpoint in an existing Traffic Manager Profile or update the properties of an existing Azure Endpoint in that Traffic Manager Profile. |
> | Akce | Microsoft.Network/trafficManagerProfiles/delete | Delete the Traffic Manager profile. All settings associated with the Traffic Manager profile will be lost, and the profile can no longer be used to route traffic. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Deletes an External Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted External Endpoint. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Gets an External Endpoint which belongs to a Traffic Manager Profile, including all the properties of that External Endpoint. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Add a new External Endpoint in an existing Traffic Manager Profile or update the properties of an existing External Endpoint in that Traffic Manager Profile. |
> | Akce | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Gets the Traffic Manager Heat Map for the given Traffic Manager profile which contains query counts and latency data by location and source IP. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Deletes an Nested Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted Nested Endpoint. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Gets an Nested Endpoint which belongs to a Traffic Manager Profile, including all the properties of that Nested Endpoint. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Add a new Nested Endpoint in an existing Traffic Manager Profile or update the properties of an existing Nested Endpoint in that Traffic Manager Profile. |
> | Akce | Microsoft.Network/trafficManagerProfiles/read | Get the Traffic Manager profile configuration. This includes DNS settings, traffic routing settings, endpoint monitoring settings, and the list of endpoints routed by this Traffic Manager profile. |
> | Akce | Microsoft.Network/trafficManagerProfiles/write | Create a Traffic Manager profile, or modify the configuration of an existing Traffic Manager profile.<br>This includes enabling or disabling a profile and modifying DNS settings, traffic routing settings, or endpoint monitoring settings.<br>Endpoints routed by the Traffic Manager profile can be added, removed, enabled or disabled. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Deletes the subscription-level key used for Realtime User Metrics collection. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/read | Gets the subscription-level key used for Realtime User Metrics collection. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/write | Creates a new subscription-level key to be used for Realtime User Metrics collection. |
> | Akce | Microsoft.Network/unregister/action | Unregisters the subscription |
> | Akce | Microsoft.Network/virtualHubs/delete | Deletes a Virtual Hub |
> | Akce | Microsoft.Network/virtualHubs/effectiveRoutes/action | Gets effective route configured on Virtual Hub |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Deletes a HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Create or update a HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/read | Get a Virtual Hub |
> | Akce | Microsoft.Network/virtualHubs/routeTables/delete | Delete a VirtualHubRouteTableV2 |
> | Akce | Microsoft.Network/virtualHubs/routeTables/read | Get a VirtualHubRouteTableV2 |
> | Akce | Microsoft.Network/virtualHubs/routeTables/write | Create or Update a VirtualHubRouteTableV2 |
> | Akce | Microsoft.Network/virtualHubs/write | Create or update a Virtual Hub |
> | Akce | microsoft.network/virtualnetworkgateways/connections/read | Get VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/virtualNetworkGateways/delete | Deletes a virtualNetworkGateway |
> | Akce | microsoft.network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | Disconnect virtual network gateway vpn connections |
> | Akce | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Generate VpnClient package for virtualNetworkGateway |
> | Akce | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generate VpnProfile package for VirtualNetworkGateway |
> | Akce | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Gets virtualNetworkGateway advertised routes |
> | Akce | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Gets virtualNetworkGateway bgp peer status |
> | Akce | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Gets virtualnetworkgateway learned routes |
> | Akce | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Get Per Vpn Client Connection Health for VirtualNetworkGateway |
> | Akce | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Get Vpnclient Ipsec parameters for VirtualNetworkGateway P2S client. |
> | Akce | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Gets the URL of a pre-generated vpn client profile package |
> | Akce | Microsoft.Network/virtualNetworkGateways/read | Gets a VirtualNetworkGateway |
> | Akce | microsoft.network/virtualnetworkgateways/reset/action | Resets a virtualNetworkGateway |
> | Akce | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reset Vpnclient shared key for VirtualNetworkGateway P2S client. |
> | Akce | Microsoft. Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Nastavte parametry protokolu IPSec vpnclient pro klienta VirtualNetworkGateway P2S. |
> | Akce | Microsoft. Network/virtualnetworkgateways/startpacketcapture/Action | Spustí zachytávání paketů Virtual Network brány. |
> | Akce | Microsoft. Network/virtualnetworkgateways/stoppacketcapture/Action | Zastaví zachytávání paketů Virtual Network brány. |
> | Akce | Microsoft. Network/virtualnetworkgateways/supportedvpndevices/Action | Seznam podporovaných zařízení VPN |
> | Akce | Microsoft. Network/virtualNetworkGateways/Write | Vytvoří nebo aktualizuje VirtualNetworkGateway. |
> | Akce | Microsoft. Network/virtualNetworks/BastionHosts/Action | Získá odkazy na hostitele bastionu v Virtual Network. |
> | Akce | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Získá odkazy na hostitele bastionu v Virtual Network. |
> | Akce | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ověřte, jestli je v zadané virtuální síti dostupná IP adresa. |
> | Akce | Microsoft.Network/virtualNetworks/delete | Odstraní virtuální síť. |
> | Akce | Microsoft. Network/virtualNetworks/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Akce | Microsoft. Network/virtualNetworks/peer/Action | Navázání partnerského vztahu virtuální sítě s jinou virtuální sítí |
> | Akce | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Akce | Microsoft. Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/DELETE | Odstraní zásady koncového bodu kontextové služby. |
> | Akce | Microsoft. Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/Read | Načte zásady koncového bodu kontextové služby. |
> | Akce | Microsoft. Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/Write | Vytvoří zásadu koncového bodu kontextové služby nebo aktualizuje stávající zásady koncového bodu služby kontextu. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/delete | Odstraní podsíť virtuální sítě. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Připraví podsíť použitím nezbytných síťových zásad. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/read | Získá definici podsítě virtuální sítě. |
> | Akce | Microsoft. Network/virtualNetworks/podsítí/unprepareNetworkPolicies/Action | Zrušení přípravy podsítě odebráním použitých zásad sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Získá odkazy na všechny virtuální počítače v podsíti virtuální sítě. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/write | Vytvoří podsíť virtuální sítě nebo aktualizuje existující podsíť virtuální sítě. |
> | Akce | Microsoft. Network/virtualNetworks/využití/čtení | Získat využití IP adres pro každou podsíť virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/virtualMachines/read | Získá odkazy na všechny virtuální počítače ve virtuální síti. |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Odstraní partnerský vztah virtuální sítě. |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Získá definici partnerského vztahu virtuální sítě. |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Vytvoří partnerský vztah virtuální sítě nebo aktualizuje stávající partnerský vztah virtuální sítě. |
> | Akce | Microsoft.Network/virtualNetworks/write | Vytvoří virtuální síť nebo aktualizuje stávající virtuální síť. |
> | Akce | Microsoft.Network/virtualNetworkTaps/delete | Virtual Network klepněte na tlačítko Odstranit |
> | Akce | Microsoft.Network/virtualNetworkTaps/join/action | Připojí se k virtuální síti klepnutím. Nelze upozorňovat. |
> | Akce | Microsoft.Network/virtualNetworkTaps/read | Získat Virtual Network klepněte |
> | Akce | Microsoft.Network/virtualNetworkTaps/write | Vytvořit nebo aktualizovat Virtual Network klepněte |
> | Akce | Microsoft. Network/virtualRouters/DELETE | Odstraní VirtualRouter |
> | Akce | Microsoft. Network/virtualRouters/JOIN/Action | Připojí se k VirtualRouter. Nelze upozorňovat. |
> | Akce | Microsoft. Network/virtualRouters/peering/DELETE | Odstraní VirtualRouterPeering |
> | Akce | Microsoft. Network/virtualRouters/peering/Read | Načte VirtualRouterPeering |
> | Akce | Microsoft. Network/virtualRouters/peering/Write | Vytvoří VirtualRouterPeering nebo aktualizuje existující VirtualRouterPeering. |
> | Akce | Microsoft. Network/virtualRouters/Read | Načte VirtualRouter |
> | Akce | Microsoft. Network/virtualRouters/Write | Vytvoří VirtualRouter nebo aktualizuje existující VirtualRouter. |
> | Akce | Microsoft. Network/virtualWans/DELETE | Odstraní virtuální síť WAN. |
> | Akce | Microsoft. Network/virtualwans/generateVpnProfile/Action | Generovat VirtualWanVpnServerConfiguration VpnProfile |
> | Akce | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Odstraní virtuální síť WAN P2SVpnServerConfiguration. |
> | Akce | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Načte virtuální síť WAN P2SVpnServerConfiguration. |
> | Akce | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Vytvoří virtuální síť WAN P2SVpnServerConfiguration nebo aktualizuje stávající virtuální síť WAN P2SVpnServerConfiguration. |
> | Akce | Microsoft. Network/virtualWans/Read | Získat virtuální síť WAN |
> | Akce | Microsoft. Network/virtualwans/supportedSecurityProviders/Read | Získá podporované poskytovatele zabezpečení VirtualWan. |
> | Akce | Microsoft. Network/virtualWans/virtualHubs/Read | Načte všechna virtuální centra, která odkazují na virtuální síť WAN. |
> | Akce | Microsoft. Network/virtualwans/vpnconfiguration/Action | Načte konfiguraci sítě VPN. |
> | Akce | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | Získat VirtualWanVpnServerConfigurations |
> | Akce | Microsoft. Network/virtualWans/vpnSites/Read | Načte všechny weby sítě VPN, které odkazují na virtuální síť WAN. |
> | Akce | Microsoft. Network/virtualWans/Write | Vytvoření nebo aktualizace virtuální sítě WAN |
> | Akce | Microsoft.Network/vpnGateways/delete | Odstraní objekt VpnGateway. |
> | Akce | Microsoft. Network/vpngateways/listvpnconnectionshealth/Action | Získá stav připojení pro všechny nebo jen podmnožinu připojení VpnGateway. |
> | Akce | Microsoft. Network/vpnGateways/Read | Načte objekt VpnGateway. |
> | Akce | Microsoft. Network/vpngateways/Reset/Action | Resetuje VpnGateway |
> | Akce | microsoft.network/vpnGateways/vpnConnections/delete | Odstraní objekt VpnConnection. |
> | Akce | microsoft.network/vpnGateways/vpnConnections/read | Načte objekt VpnConnection. |
> | Akce | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Získá připojení VPN Link. |
> | Akce | microsoft.network/vpnGateways/vpnConnections/write | Vloží VpnConnection. |
> | Akce | Microsoft. Network/vpnGateways/Write | Vloží VpnGateway. |
> | Akce | Microsoft. Network/vpnServerConfigurations/DELETE | Odstranit VpnServerConfiguration |
> | Akce | Microsoft. Network/vpnServerConfigurations/Read | Získat VpnServerConfiguration |
> | Akce | Microsoft. Network/vpnServerConfigurations/Write | Vytvořit nebo aktualizovat VpnServerConfiguration |
> | Akce | Microsoft.Network/vpnsites/delete | Odstraní prostředek sítě VPN. |
> | Akce | Microsoft. Network/vpnsites/Read | Získá prostředek sítě VPN. |
> | Akce | Microsoft. Network/vpnSites/vpnSiteLinks/Read | Získá odkaz na síť VPN. |
> | Akce | Microsoft.Network/vpnsites/write | Vytvoří nebo aktualizuje prostředek sítě VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. NotificationHubs/CheckNamespaceAvailability/Action | Checks whether or not a given Namespace resource name is available within the NotificationHub service. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Získání připojovacího řetězce k oboru názvů |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Namespace Authorization Rule Regenerate Primary/SecondaryKey, Specify the Key that needs to be regenerated |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Checks whether or not a given NotificationHub name is available inside a Namespace. |
> | Akce | Microsoft.NotificationHubs/Namespaces/Delete | Odstranit prostředek oboru názvů |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Get the list of Notification Hub Authorization Rules |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Delete Notification Hub Authorization Rules |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Get the Connection String to the Notification Hub |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Get the list of Notification Hub Authorization Rules |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub Authorization Rule Regenerate Primary/SecondaryKey, Specify the Key that needs to be regenerated |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Create Notification Hub Authorization Rules and Update its properties. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Send a test push notification. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Delete Notification Hub Resource |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Get All Notification Hub PNS Credentials. This includes, WNS, MPNS, APNS, GCM and Baidu credentials |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Get list of Notification Hub Resource Descriptions |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Create a Notification Hub and Update its properties. Its properties mainly include PNS Credentials. Authorization Rules and TTL |
> | Akce | Microsoft.NotificationHubs/Namespaces/read | Získat seznam popisu prostředku oboru názvů |
> | Akce | Microsoft.NotificationHubs/Namespaces/write | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.NotificationHubs/operationResults/read | Returns operation results for Notification Hubs provider |
> | Akce | Microsoft.NotificationHubs/operations/read | Returns a list of supported operations for Notification Hubs provider |
> | Akce | Microsoft.NotificationHubs/register/action | Registers the subscription for the NotificationHubs resource provider and enables the creation of Namespaces and NotificationHubs |
> | Akce | Microsoft.NotificationHubs/unregister/action | Unregisters the subscription for the NotificationHubs resource provider and enables the creation of Namespaces and NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OffAzure/HyperVSites/clusters/read | Gets the properties of a Hyper-V cluster |
> | Akce | Microsoft.OffAzure/HyperVSites/clusters/write | Creates or updates the Hyper-V cluster |
> | Akce | Microsoft.OffAzure/HyperVSites/delete | Deletes the Hyper-V site |
> | Akce | Microsoft.OffAzure/HyperVSites/healthsummary/read | Gets the health summary for Hyper-V resource |
> | Akce | Microsoft.OffAzure/HyperVSites/hosts/read | Gets the properties of a Hyper-V host |
> | Akce | Microsoft.OffAzure/HyperVSites/hosts/write | Creates or updates the Hyper-V host |
> | Akce | Microsoft.OffAzure/HyperVSites/jobs/read | Gets the properties of a Hyper-V jobs |
> | Akce | Microsoft.OffAzure/HyperVSites/machines/read | Gets the properties of a Hyper-V machines |
> | Akce | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Gets the properties of a Hyper-V operation status |
> | Akce | Microsoft.OffAzure/HyperVSites/read | Gets the properties of a Hyper-V site |
> | Akce | Microsoft.OffAzure/HyperVSites/refresh/action | Refreshes the objects within a Hyper-V site |
> | Akce | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Gets the properties of a Hyper-V run as accounts |
> | Akce | Microsoft.OffAzure/HyperVSites/usage/read | Gets the usages of a Hyper-V site |
> | Akce | Microsoft.OffAzure/HyperVSites/write | Creates or updates the Hyper-V site |
> | Akce | Microsoft.OffAzure/Operations/read | Reads the exposed operations |
> | Akce | Microsoft.OffAzure/register/action | Registers Subscription with Microsoft.OffAzure resource provider |
> | Akce | Microsoft.OffAzure/ServerSites/jobs/read | Gets the properties of a Server jobs |
> | Akce | Microsoft.OffAzure/ServerSites/machines/read | Gets the properties of a Server machines |
> | Akce | Microsoft.OffAzure/ServerSites/machines/write | Write the properties of a Server machines |
> | Akce | Microsoft.OffAzure/ServerSites/operationsstatus/read | Gets the properties of a Server operation status |
> | Akce | Microsoft.OffAzure/ServerSites/read | Gets the properties of a Server site |
> | Akce | Microsoft.OffAzure/ServerSites/refresh/action | Refreshes the objects within a Server site |
> | Akce | Microsoft.OffAzure/ServerSites/runasaccounts/read | Gets the properties of a Server run as accounts |
> | Akce | Microsoft.OffAzure/ServerSites/usage/read | Gets the usages of a Server site |
> | Akce | Microsoft.OffAzure/ServerSites/write | Creates or updates the Server site |
> | Akce | Microsoft.OffAzure/VMwareSites/delete | Deletes the VMware site |
> | Akce | Microsoft.OffAzure/VMwareSites/healthsummary/read | Gets the health summary for VMware resource |
> | Akce | Microsoft.OffAzure/VMwareSites/jobs/read | Gets the properties of a VMware jobs |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/read | Gets the properties of a VMware machines |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/start/action | Start VMware machines |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stops the VMware machines |
> | Akce | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Gets the properties of a VMware operation status |
> | Akce | Microsoft.OffAzure/VMwareSites/read | Gets the properties of a VMware site |
> | Akce | Microsoft.OffAzure/VMwareSites/refresh/action | Refreshes the objects within a VMware site |
> | Akce | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Gets the properties of a VMware run as accounts |
> | Akce | Microsoft.OffAzure/VMwareSites/usage/read | Gets the usages of a VMware site |
> | Akce | Microsoft.OffAzure/VMwareSites/vcenters/read | Gets the properties of a VMware vCenter |
> | Akce | Microsoft.OffAzure/VMwareSites/vcenters/write | Creates or updates the VMware vCenter |
> | Akce | Microsoft.OffAzure/VMwareSites/write | Creates or updates the VMware site |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OperationalInsights/linkTargets/read | Lists existing accounts that are not associated with an Azure subscription. To link this Azure subscription to a workspace, use a customer id returned by this operation in the customer id property of the Create Workspace operation. |
> | Akce | microsoft.operationalinsights/operations/read | Lists all of the available OperationalInsights Rest API operations. |
> | Akce | microsoft.operationalinsights/register/action | Rergisters the subscription. |
> | Akce | Microsoft.OperationalInsights/register/action | Registrace předplatného pro poskytovatele prostředků. |
> | Akce | microsoft.operationalinsights/unregister/action | Unregisters the subscription. |
> | Akce | Microsoft.OperationalInsights/workspaces/analytics/query/action | Search using new engine. |
> | Akce | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Get search schema V2. |
> | Akce | Microsoft.OperationalInsights/workspaces/api/query/action | Search using new engine. |
> | Akce | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Get search schema V2. |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Delete Configuration Scope |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Get Configuration Scope |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Set Configuration Scope |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/delete | Delete datasources under a workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/read | Get datasources under a workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/write | Create/Update datasources under a workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/delete | Deletes a workspace. If the workspace was linked to an existing workspace at creation time then the workspace it was linked to is not deleted. |
> | Akce | Microsoft.OperationalInsights/workspaces/gateways/delete | Removes a gateway configured for the workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generates Registration Certificate for the workspace. This Certificate is used to connect Microsoft System Center Operation Manager to the workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Disables an intelligence pack for a given workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Enables an intelligence pack for a given workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lists all intelligence packs that are visible for a given workspace and also lists whether the pack is enabled or disabled for that workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Delete linked services under given workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/read | Get linked services under given workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/write | Create/Update linked services under given workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/listKeys/action | Retrieves the list keys for the workspace. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/listKeys/read | Retrieves the list keys for the workspace. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/managementGroups/read | Gets the names and metadata for System Center Operations Manager management groups connected to this workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Get Metric Definitions under workspace |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Delete the user's notification settings for the workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Get the user's notification settings for the workspace. |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Set the user's notification settings for the workspace. |
> | Akce | microsoft.operationalinsights/workspaces/operations/read | Gets the status of an OperationalInsights workspace operation. |
> | Akce | Microsoft.OperationalInsights/workspaces/purge/action | Delete specified data from workspace |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Read data from the AADDomainServicesAccountLogon table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Read data from the AADDomainServicesAccountManagement table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Read data from the AADDomainServicesDirectoryServiceAccess table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Read data from the AADDomainServicesLogonLogoff table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Read data from the AADDomainServicesPolicyChange table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Read data from the AADDomainServicesPrivilegeUse table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Read data from the AADDomainServicesSystemSecurity table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Read data from the ADAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupAlerts/read | Read data from the AddonAzureBackupAlerts table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupJobs/read | Read data from the AddonAzureBackupJobs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupPolicy/read | Read data from the AddonAzureBackupPolicy table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupProtectedInstance/read | Read data from the AddonAzureBackupProtectedInstance table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupStorage/read | Read data from the AddonAzureBackupStorage table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Read data from the ADFActivityRun table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Read data from the ADFPipelineRun table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Read data from the ADFTriggerRun table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Read data from the ADReplicationResult table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Read data from the ADSecurityAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AegDeliveryFailureLogs/read | Read data from the AegDeliveryFailureLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AegPublishFailureLogs/read | Read data from the AegPublishFailureLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Alert/read | Read data from the Alert table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Read data from the AlertHistory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterEvent/read | Read data from the AmlComputeClusterEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterNodeEvent/read | Read data from the AmlComputeClusterNodeEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AmlComputeJobEvent/read | Read data from the AmlComputeJobEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ApiManagementGatewayLogs/read | Read data from the ApiManagementGatewayLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Read data from the AppCenterError table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Read data from the ApplicationInsights table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppPlatformLogsforSpring/read | Read data from the AppPlatformLogsforSpring table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppPlatformSystemLogs/read | Read data from the AppPlatformSystemLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppServiceAppLogs/read | Read data from the AppServiceAppLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppServiceAuditLogs/read | Read data from the AppServiceAuditLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppServiceConsoleLogs/read | Read data from the AppServiceConsoleLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppServiceEnvironmentPlatformLogs/read | Read data from the AppServiceEnvironmentPlatformLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppServiceHTTPLogs/read | Read data from the AppServiceHTTPLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Read data from the AuditLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Read data from the AutoscaleEvaluationsLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Read data from the AutoscaleScaleActionsLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Read data from the AWSCloudTrail table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Read data from the AzureActivity table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Read data from the AzureAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Read data from the AzureMetrics table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BaiClusterEvent/read | Read data from the BaiClusterEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BaiClusterNodeEvent/read | Read data from the BaiClusterNodeEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BaiJobEvent/read | Read data from the BaiJobEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Read data from the BlockchainApplicationLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Read data from the BlockchainProxyLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Read data from the BoundPort table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Read data from the CommonSecurityLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Read data from the ComputerGroup table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Read data from the ConfigurationChange table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Read data from the ConfigurationData table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Read data from the ContainerImageInventory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Read data from the ContainerInventory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Read data from the ContainerLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Read data from the ContainerNodeInventory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryLoginEvents/read | Read data from the ContainerRegistryLoginEvents table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryRepositoryEvents/read | Read data from the ContainerRegistryRepositoryEvents table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Read data from the ContainerServiceLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/CoreAzureBackup/read | Read data from the CoreAzureBackup table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Read data from the DatabricksAccounts table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Read data from the DatabricksClusters table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Read data from the DatabricksDBFS table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksInstancePools/read | Read data from the DatabricksInstancePools table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Read data from the DatabricksJobs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Read data from the DatabricksNotebook table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Read data from the DatabricksSecrets table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Read data from the DatabricksSQLPermissions table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Read data from the DatabricksSSH table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Read data from the DatabricksTables table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Read data from the DatabricksWorkspace table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Read data from the DeviceAppCrash table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Read data from the DeviceAppLaunch table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Read data from the DeviceCalendar table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Read data from the DeviceCleanup table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Read data from the DeviceConnectSession table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Read data from the DeviceEtw table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Read data from the DeviceHardwareHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Read data from the DeviceHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Read data from the DeviceHeartbeat table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Read data from the DeviceSkypeHeartbeat table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Read data from the DeviceSkypeSignIn table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Read data from the DeviceSleepState table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Read data from the DHAppFailure table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Read data from the DHAppReliability table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHCPActivity/read | Read data from the DHCPActivity table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Read data from the DHDriverReliability table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Read data from the DHLogonFailures table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Read data from the DHLogonMetrics table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Read data from the DHOSCrashData table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Read data from the DHOSReliability table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Read data from the DHWipAppLearning table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Read data from the DnsEvents table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Read data from the DnsInventory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Read data from the ETWEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Event/read | Read data from the Event table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Read data from the ExchangeAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Read data from the ExchangeOnlineAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/FailedIngestion/read | Read data from the FailedIngestion table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/FunctionAppLogs/read | Read data from the FunctionAppLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Read data from the Heartbeat table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Read data from the HuntingBookmark table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Read data from the IISAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Read data from the InboundConnection table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Read data from the InsightsMetrics table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Read data from the IntuneAuditLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | Read data from the IntuneDeviceComplianceOrg table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Read data from the IntuneOperationalLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Read data from the KubeEvents table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeHealth/read | Read data from the KubeHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeMonAgentEvents/read | Read data from the KubeMonAgentEvents table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Read data from the KubeNodeInventory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Read data from the KubePodInventory table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Read data from the KubeServices table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Read data from the LinuxAuditLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Read data from the MAApplication table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Read data from the MAApplicationHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Read data from the MAApplicationHealthAlternativeVersions table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Read data from the MAApplicationHealthIssues table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Read data from the MAApplicationInstance table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Read data from the MAApplicationInstanceReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Read data from the MAApplicationReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Read data from the MADeploymentPlan table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Read data from the MADevice table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Read data from the MADeviceNotEnrolled table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Read data from the MADeviceNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Read data from the MADevicePnPHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Read data from the MADevicePnPHealthAlternativeVersions table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Read data from the MADevicePnPHealthIssues table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Read data from the MADeviceReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Read data from the MADriverInstanceReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Read data from the MADriverReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Read data from the MAOfficeAddin table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Read data from the MAOfficeAddinEntityHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Read data from the MAOfficeAddinHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Read data from the MAOfficeAddinHealthEventNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Read data from the MAOfficeAddinHealthIssues table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Read data from the MAOfficeAddinInstance table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Read data from the MAOfficeAddinInstanceReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Read data from the MAOfficeAddinReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Read data from the MAOfficeApp table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Read data from the MAOfficeAppCrashesNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Read data from the MAOfficeAppHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Read data from the MAOfficeAppInstance table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Read data from the MAOfficeAppInstanceHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Read data from the MAOfficeAppReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Read data from the MAOfficeAppSessionsNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Read data from the MAOfficeBuildInfo table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Read data from the MAOfficeCurrencyAssessment table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Read data from the MAOfficeCurrencyAssessmentDailyCounts table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Read data from the MAOfficeDeploymentStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Read data from the MAOfficeDeploymentStatusNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Read data from the MAOfficeMacroErrorNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Read data from the MAOfficeMacroGlobalHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Read data from the MAOfficeMacroHealth table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Read data from the MAOfficeMacroHealthIssues table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Read data from the MAOfficeMacroIssueInstanceReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Read data from the MAOfficeMacroIssueReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Read data from the MAOfficeMacroSummary table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Read data from the MAOfficeSuite table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Read data from the MAOfficeSuiteInstance table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Read data from the MAProposedPilotDevices table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Read data from the MAWindowsBuildInfo table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Read data from the MAWindowsCurrencyAssessment table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Read data from the MAWindowsCurrencyAssessmentDailyCounts table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Read data from the MAWindowsDeploymentStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Read data from the MAWindowsDeploymentStatusNRT table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Read data from the MAWindowsSysReqInstanceReadiness table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/McasShadowItReporting/read | Read data from the McasShadowItReporting table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftAzureBastionAuditLogs/read | Read data from the MicrosoftAzureBastionAuditLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareReceivedSnapshotLog/read | Read data from the MicrosoftDataShareReceivedSnapshotLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareSentSnapshotLog/read | Read data from the MicrosoftDataShareSentSnapshotLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareShareLog/read | Read data from the MicrosoftDataShareShareLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetryPerformanceLogs/read | Read data from the MicrosoftDynamicsTelemetryPerformanceLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/read | Read data from the MicrosoftDynamicsTelemetrySystemMetricsLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftHealthcareApisAuditLogs/read | Read data from the MicrosoftHealthcareApisAuditLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MicrosoftInsightsAzureActivityLog/read | Read data from the MicrosoftInsightsAzureActivityLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Read data from the NetworkMonitoring table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Read data from the OfficeActivity table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Operation/read | Read data from the Operation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Read data from the OutboundConnection table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Perf/read | Read data from the Perf table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Read data from the ProtectionStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/read | Run queries over the data in the workspace |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Read data from the ReservedCommonFields table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Read data from the SCCMAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Read data from the SCOMAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Read data from the SecurityAlert table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Read data from the SecurityBaseline table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Read data from the SecurityBaselineSummary table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Read data from the SecurityDetection table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Read data from the SecurityEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Read data from the SecurityIoTRawEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Read data from the SecurityRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Read data from the ServiceFabricOperationalEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Read data from the ServiceFabricReliableActorEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Read data from the ServiceFabricReliableServiceEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Read data from the SfBAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Read data from the SfBOnlineAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Read data from the SharePointOnlineAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SignalRServiceDiagnosticLogs/read | Read data from the SignalRServiceDiagnosticLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Read data from the SigninLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Read data from the SPAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Read data from the SQLAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Read data from the SQLQueryPerformance table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Read data from the SqlThreatProtectionLoginAudits table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Read data from the SqlVulnerabilityAssessmentResult table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/StorageBlobLogs/read | Read data from the StorageBlobLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/StorageFileLogs/read | Read data from the StorageFileLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/StorageQueueLogs/read | Read data from the StorageQueueLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/StorageTableLogs/read | Read data from the StorageTableLogs table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SucceededIngestion/read | Read data from the SucceededIngestion table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Read data from the Syslog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Read data from the SysmonEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Reading data from any custom log |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Read data from the ThreatIntelligenceIndicator table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Read data from the UAApp table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Read data from the UAComputer table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Read data from the UAComputerRank table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Read data from the UADriver table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Read data from the UADriverProblemCodes table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Read data from the UAFeedback table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Read data from the UAHardwareSecurity table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Read data from the UAIESiteDiscovery table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Read data from the UAOfficeAddIn table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Read data from the UAProposedActionPlan table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Read data from the UASysReqIssue table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Read data from the UAUpgradedComputer table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Update/read | Read data from the Update table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Read data from the UpdateRunProgress table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Read data from the UpdateSummary table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Usage/read | Read data from the Usage table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Read data from the VMBoundPort table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/VMComputer/read | Read data from the VMComputer table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Read data from the VMConnection table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/VMProcess/read | Read data from the VMProcess table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Read data from the W3CIISLog table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Read data from the WaaSDeploymentStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Read data from the WaaSInsiderStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Read data from the WaaSUpdateStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Read data from the WDAVStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Read data from the WDAVThreat table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Read data from the WindowsClientAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Read data from the WindowsEvent table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Read data from the WindowsFirewall table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Read data from the WindowsServerAssessmentRecommendation table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WireData/read | Read data from the WireData table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Read data from the WorkloadMonitoringPerf table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Read data from the WUDOAggregatedStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Read data from the WUDOStatus table |
> | Akce | Microsoft.OperationalInsights/workspaces/read | Gets an existing workspace |
> | Akce | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenerates the specified workspace shared key |
> | Akce | microsoft.operationalinsights/workspaces/rules/read | Get all alert rules. |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Deletes a saved search query |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/read | Gets a saved search query |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/results/read | Get saved searches results. Zastaralé |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Delete scheduled search actions. |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Get scheduled search actions. |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Create or update scheduled search actions. |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Delete scheduled searches. |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Get scheduled searches. |
> | Akce | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Create or update scheduled searches. |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/write | Creates a saved search query |
> | Akce | Microsoft.OperationalInsights/workspaces/schema/read | Gets the search schema for the workspace.  Search schema includes the exposed fields and their types. |
> | Akce | Microsoft.OperationalInsights/workspaces/search/action | Executes a search query |
> | Akce | microsoft.operationalinsights/workspaces/search/read | Get search results. Zastaralý parametr. |
> | Akce | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Odstraní konfiguraci úložiště. Tím se zastaví, aby Microsoft Operational Insights číst data z účtu úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Získá konfiguraci úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Vytvoří novou konfiguraci úložiště. Tyto konfigurace se používají k vyžádání dat z umístění v existujícím účtu úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Získat protokol selhání překladu upgradu pro pracovní prostor |
> | Akce | Microsoft.OperationalInsights/workspaces/usages/read | Načte data o využití pro pracovní prostor, včetně množství dat, které pracovní prostor přečetl. |
> | Akce | Microsoft.OperationalInsights/workspaces/write | Vytvoří nový pracovní prostor nebo vytvoří odkaz na existující pracovní prostor tím, že mu poskytne ID zákazníka z existujícího pracovního prostoru. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. OperationsManagement/managementAssociations/DELETE | Odstranit existující přidružení správy |
> | Akce | Microsoft. OperationsManagement/managementAssociations/Read | Získat existující přidružení správy |
> | Akce | Microsoft.OperationsManagement/managementAssociations/write | Vytvořit nové přidružení pro správu |
> | Akce | Microsoft. OperationsManagement/managementConfigurations/DELETE | Odstranit existující konfiguraci správy |
> | Akce | Microsoft. OperationsManagement/managementConfigurations/Read | Získat existující konfiguraci správy |
> | Akce | Microsoft. OperationsManagement/managementConfigurations/Write | Vytvořit novou konfiguraci správy |
> | Akce | Microsoft. OperationsManagement/Register/Action | Registrace předplatného pro poskytovatele prostředků. |
> | Akce | Microsoft. OperationsManagement/Solutions/DELETE | Odstranit existující řešení OMS |
> | Akce | Microsoft. OperationsManagement/Solutions/Read | Získat ukončení řešení OMS |
> | Akce | Microsoft. OperationsManagement/Solutions/Write | Vytvořit nové řešení OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.PolicyInsights/asyncOperationResults/read | Načte výsledek asynchronní operace. |
> | Akce dataaction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Zkontroluje stav dodržování předpisů dané součásti proti zásadám dat. |
> | Akce | Microsoft.PolicyInsights/operations/read | Načte podporované operace v oboru názvů Microsoft. PolicyInsights. |
> | Akce dataaction | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Zaprotokoluje události zásad součásti prostředků. |
> | Akce | Microsoft.PolicyInsights/policyEvents/queryResults/action | Dotaz na informace o událostech zásad. |
> | Akce | Microsoft.PolicyInsights/policyEvents/queryResults/read | Dotaz na informace o událostech zásad. |
> | Akce | Microsoft. PolicyInsights/policyMetadata/Read | Získejte prostředky metadat zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/queryResults/action | Dotaz na informace o stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/queryResults/read | Dotaz na informace o stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/summarize/action | Dotaz na souhrnné informace o aktuálních stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/summarize/read | Dotaz na souhrnné informace o aktuálních stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Aktivuje nové vyhodnocení dodržování předpisů pro vybraný obor. |
> | Akce | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Dotaz na informace o prostředcích vyžadovaných zásadami DeployIfNotExists |
> | Akce | Microsoft.PolicyInsights/register/action | Zaregistruje poskytovatele prostředků Microsoft Policy Insights a povolí akce. |
> | Akce | Microsoft. PolicyInsights/nápravy/zrušit/akce | Zruší probíhající nápravu zásad Microsoftu. |
> | Akce | Microsoft.PolicyInsights/remediations/delete | Odstraňte nápravy zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/listDeployments/read | Zobrazí seznam nasazení vyžadovaných nápravou zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/read | Získejte nápravy zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/write | Vytvoří nebo aktualizuje nápravy zásad Microsoftu. |
> | Akce | Microsoft.PolicyInsights/unregister/action | Zruší registraci poskytovatele prostředků Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Portal/konzoly/DELETE | Odebere instanci Cloud Shell. |
> | Akce | Microsoft. Portal/konzoly/číst | Přečte instanci Cloud Shell. |
> | Akce | Microsoft. Portal/konzoly/zápis | Vytvoří nebo aktualizuje instanci Cloud Shell. |
> | Akce | Microsoft. Portal/řídicí panely/odstranit | Odebere řídicí panel z předplatného. |
> | Akce | Microsoft. Portal/řídicí panely/číst | Přečte řídicí panely pro předplatné. |
> | Akce | Microsoft. Portal/řídicí panely/zápis | Umožňuje přidat nebo upravit řídicí panel k předplatnému. |
> | Akce | Microsoft. Portal/registrace/akce | Zaregistrovat na portál |
> | Akce | Microsoft.Portal/usersettings/delete | Odebere Cloud Shell nastavení uživatele. |
> | Akce | Microsoft.Portal/usersettings/read | Přečte nastavení Cloud Shell uživatele. |
> | Akce | Microsoft.Portal/usersettings/write | Vytvoří nebo aktualizuje nastavení uživatele Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. PowerBIDedicated/kapacit/DELETE | Odstraní vyhrazenou kapacitu Power BI. |
> | Akce | Microsoft. PowerBIDedicated/kapacit/čtení | Načte informace o zadané Power BI vyhrazené kapacitě. |
> | Akce | Microsoft. PowerBIDedicated/kapacit/pokračování/akce | Obnoví kapacitu. |
> | Akce | Microsoft. PowerBIDedicated/kapacity/SKU/číst | Načíst dostupné informace o SKU pro kapacitu |
> | Akce | Microsoft. PowerBIDedicated/kapacit/pozastavit/akce | Pozastaví kapacitu. |
> | Akce | Microsoft. PowerBIDedicated/kapacit/Write | Vytvoří nebo aktualizuje zadanou vyhrazenou kapacitu Power BI. |
> | Akce | Microsoft. PowerBIDedicated/Locations/checkNameAvailability/Action | Kontroluje, že zadaný název vyhrazené kapacity Power BI je platný a nepoužívá se. |
> | Akce | Microsoft.PowerBIDedicated/locations/operationresults/read | Načte informace o výsledku zadané operace. |
> | Akce | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Načte informace o stavu zadané operace. |
> | Akce | Microsoft.PowerBIDedicated/operations/read | Načte informace o operacích. |
> | Akce | Microsoft. PowerBIDedicated/Register/Action | Zaregistruje Power BI vyhrazeného poskytovatele prostředků. |
> | Akce | Microsoft. PowerBIDedicated/SKU/číst | Načte informace o SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | Akce | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp je interní operace, kterou používá služba. |
> | Akce | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Akce | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Create a backup Protected Item |
> | Akce | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Akce | Microsoft. RecoveryServices/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Ověřit funkce |
> | Akce | Microsoft. RecoveryServices/Locations/checkNameAvailability/Action | Check Resource Name Availability is an API to check if resource name is available |
> | Akce | Microsoft.RecoveryServices/locations/operationStatus/read | Gets Operation Status for a given Operation |
> | Akce | Microsoft.RecoveryServices/operations/read | Operation returns the list of Operations for a Resource Provider |
> | Akce | Microsoft.RecoveryServices/register/action | Registers subscription for given Resource Provider |
> | Akce | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returns Configuration for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupconfig/write | Updates Configuration for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/read | Gets Backup Resource Encryption Configuration. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/write | Updates Backup Resource Encryption Configuration |
> | Akce | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returns all the backup management servers registered with vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Delete a backup Protection Intent |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Get a backup Protection Intent |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Create a backup Protection Intent |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returns status of the operation |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/operationsStatus/read | Returns status of the operation |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Get all protectable containers |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Deletes the registered Container |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Do inquiry for workloads within a container |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Get all items in a container |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Gets result of Operation performed on Protection Container. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationsStatus/read | Gets status of Operation performed on Protection Container. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Performs Backup for Protected Item. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Deletes Protected Item |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Gets Result of Operation Performed on Protected Items. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returns the status of Operation performed on Protected Items. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returns object details of the Protected Item |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provision Instant Item Recovery for Protected Item |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Get Recovery Points for Protected Items. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restore Recovery Points for Protected Items. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoke Instant Item Recovery for Protected Item |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Create a backup Protected Item |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returns all registered containers |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Creates a registered container |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Refreshes the container list |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancel the Job |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returns the Result of Job Operation. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/operationsStatus/read | Returns the status of Job Operation. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returns all Job Objects |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export Jobs |
> | Akce | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returns Backup Operation Result for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returns Backup Operation Status for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Delete a Protection Policy |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Get Results of Policy Operation. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Get Status of Policy Operation. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returns all Protection Policies |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Creates Protection Policy |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returns list of all Protectable Items. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returns all containers belonging to the subscription |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | List all backup Protection Intents |
> | Akce | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Returns Security PIN Information for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returns Storage Configuration for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Updates Storage Configuration for Recovery Services Vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returns summaries for Protected Items and Protected Servers for a Recovery Services . |
> | Akce | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validate Operation on Protected Item |
> | Akce | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | Akce | Microsoft.RecoveryServices/Vaults/delete | Operace odstranění trezoru odstraní zadaný prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolves the alert. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Gets the Recovery services vault notification configuration. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configures e-mail notifications to Recovery services vault. |
> | Akce | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | The UnRegister Container operation can be used to unregister a container. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | The Register Service Container operation can be used to register a container with Recovery Service. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Read any Alerts Settings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Create or Update any Alerts Settings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationEvents/read | Read any Events |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Checks Consistency of the Fabric |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Delete any Fabrics |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Deploy Process Server Image |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrate Fabric To AAD |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/operationresults/read | Track the results of an asynchronous operation on the resource Fabrics |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Read any Fabrics |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociate Gateway |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remove Fabric |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renew Certificate for Fabric |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Read any Logical Networks |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Čtení všech sítí |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Delete any Network Mappings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Číst všechna mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Create or Update any Network Mappings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Discover Protectable Item |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/operationresults/read | Track the results of an asynchronous operation on the resource Protection Containers |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Čtení všech kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remove Protection Container |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Delete any Migration Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrate Item |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Read any Migration Recovery Points |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | Track the results of an asynchronous operation on the resource Migration Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Čtení všech položek migrace |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Test Migrate |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Test Migrate Cleanup |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Create or Update any Migration Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Read any Protectable Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Přidání disků |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Apply Recovery Point |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Delete any Protected Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | Track the results of an asynchronous operation on the resource Protected Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Plánované převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Čtení jakýchkoli chráněných položek |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Read any Replication Recovery Points |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remove Protected Item |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Remove disks |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Repair replication |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | ReProtect Protected Item |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Submit Feedback |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Read any Target Compute Sizes |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testovací převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Test Failover Cleanup |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Update Mobility Service |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Create or Update any Protected Items |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Delete any Protection Container Mappings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | Track the results of an asynchronous operation on the resource Protection Container Mappings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Přečíst všechna mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remove Protection Container Mapping |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Create or Update any Protection Container Mappings |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Switch Protection Container |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Create or Update any Protection Containers |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Delete any Recovery Services Providers |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | Track the results of an asynchronous operation on the resource Recovery Services Providers |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Číst všechny poskytovatele Recovery Services |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Refresh Provider |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remove Recovery Services Provider |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Create or Update any Recovery Services Providers |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/DELETE | Odstranit všechna mapování klasifikace úložiště |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/číst | Sledování výsledků asynchronní operace v mapování klasifikace úložiště prostředků |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Write | Vytvoří nebo aktualizuje všechna mapování klasifikace úložiště. |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/DELETE | Odstranit všechny servery vCenter |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/operationresults/Read | Sledování výsledků asynchronní operace na servery vCenter prostředku |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst libovolný servery vCenter |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/Write | Vytvořit nebo aktualizovat libovolný servery vCenter |
> | Akce | Microsoft. RecoveryServices/trezory/replicationFabrics/Write | Vytvoření nebo aktualizace prostředků infrastruktury |
> | Akce | Microsoft. RecoveryServices/trezory/replicationJobs/Cancel/Action | Zrušit úlohu |
> | Akce | Microsoft. RecoveryServices/trezory/replicationJobs/operationresults/Read | Sledování výsledků asynchronní operace v úlohách prostředků |
> | Akce | Microsoft. RecoveryServices/trezory/replicationJobs/Read | Čtení všech úloh |
> | Akce | Microsoft. RecoveryServices/trezory/replicationJobs/restart/Action | Restartovat úlohu |
> | Akce | Microsoft. RecoveryServices/trezory/replicationJobs/Resume/Action | Pokračovat v úloze |
> | Akce | Microsoft. RecoveryServices/trezory/replicationMigrationItems/Read | Čtení všech položek migrace |
> | Akce | Microsoft. RecoveryServices/trezory/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | Akce | Microsoft. RecoveryServices/trezory/replicationNetworks/Read | Čtení všech sítí |
> | Akce | Microsoft. RecoveryServices/trezory/replicationOperationStatus/Read | Přečtěte si stav operace replikace trezoru |
> | Akce | Microsoft. RecoveryServices/trezory/replicationPolicies/DELETE | Odstranit všechny zásady |
> | Akce | Microsoft. RecoveryServices/trezory/replicationPolicies/operationresults/Read | Sledování výsledků asynchronní operace v zásadách prostředků |
> | Akce | Microsoft. RecoveryServices/trezory/replicationPolicies/Read | Čtení všech zásad |
> | Akce | Microsoft. RecoveryServices/trezory/replicationPolicies/Write | Vytvořit nebo aktualizovat všechny zásady |
> | Akce | Microsoft. RecoveryServices/trezory/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | Akce | Microsoft. RecoveryServices/trezory/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | Akce | Microsoft. RecoveryServices/trezory/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/DELETE | Odstranění všech plánů obnovení |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/failoverCommit/Action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/operationresults/Read | Sledování výsledků asynchronní operace v plánech obnovení prostředků |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánovaného převzetí služeb při selhání |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/Read | Čtení všech plánů obnovení |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/reprotect/Action | Znovu zapnout ochranu plánu obnovení |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/testFailover/Action | Plán obnovení testovacího převzetí služeb při selhání |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/testFailoverCleanup/Action | Plán obnovení pro vyčištění testovacího převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/Write | Vytvoření nebo aktualizace jakýchkoli plánů obnovení |
> | Akce | Microsoft. RecoveryServices/trezory/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | Akce | Microsoft. RecoveryServices/trezory/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | Akce | Microsoft. RecoveryServices/trezory/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | Akce | Microsoft. RecoveryServices/trezory/replicationSupportedOperatingSystems/Read | Přečíst vše  |
> | Akce | Microsoft. RecoveryServices/trezory/replicationUsages/Read | Čtení všech využití replikace trezoru |
> | Akce | Microsoft. RecoveryServices/trezory/replicationVaultHealth/operationresults/Read | Sledování výsledků asynchronní operace ve stavu replikace trezoru prostředků |
> | Akce | Microsoft. RecoveryServices/trezory/replicationVaultHealth/Read | Přečtěte si stav replikace trezoru |
> | Akce | Microsoft. RecoveryServices/trezory/replicationVaultHealth/Refresh/Action | Aktualizovat stav trezoru |
> | Akce | Microsoft. RecoveryServices/trezory/replicationVaultSettings/Read | Přečíst vše  |
> | Akce | Microsoft. RecoveryServices/trezory/replicationVaultSettings/Write | Vytvořit nebo aktualizovat libovolnou  |
> | Akce | Microsoft. RecoveryServices/trezory/replicationvCenters/Read | Číst libovolný servery vCenter |
> | Akce | Microsoft. RecoveryServices/trezory/používání/čtení | Čtení všech použití trezoru |
> | Akce | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/write | Operace vytvořit trezor vytvoří prostředek Azure typu trezor. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Relay/checkNameAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. |
> | Akce | Microsoft. Relay/checkNamespaceAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. Toto rozhraní API je zastaralé, použijte místo toho CheckNameAvailability. |
> | Akce | Microsoft. Relay/obory názvů/autorizačních pravidel/Action | Aktualizuje autorizační pravidlo oboru názvů. Toto rozhraní API je zastaralé. Místo toho prosím použijte volání PUT k aktualizaci autorizačního pravidla oboru názvů.. Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. Relay/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Akce | Microsoft. Relay/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Akce | Microsoft. Relay/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Akce | Microsoft. Relay/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. Relay/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft. Relay/obory názvů/odstranění | Odstranit prostředek oboru názvů |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/klíče listkey/Action | Získá klíče autorizačních pravidel pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/Read | Získat autorizační pravidla primárního oboru názvů pro zotavení po havárii |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/breakPairing/Action | Zakáže zotavení po havárii a zastaví replikaci změn z primárních oborů názvů na sekundární. |
> | Akce | Microsoft. Relay/obory názvů/disasterrecoveryconfigs/checkNameAvailability/Action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/DELETE | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tuto operaci lze vyvolat pouze prostřednictvím primárního oboru názvů. |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/převzetí služeb při selhání/akce | Vyvolá převzetí služeb při selhání GEOGRAFICKého zotavení po havárii a změní konfiguraci aliasu oboru názvů tak, aby odkazoval na sekundární obor názvů. |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/Read | Získá konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Akce | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/Write | Vytvoří nebo aktualizuje konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/Action | Operace pro aktualizaci HybridConnection Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel HybridConnection |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce pro HybridConnection |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/Read |  Získat seznam autorizačních pravidel HybridConnection |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/regeneratekeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/Write | Vytvořte autorizační pravidla HybridConnection a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/DELETE | Operace odstranění prostředku HybridConnection |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/Read | Získá seznam popisů prostředků HybridConnection. |
> | Akce | Microsoft. Relay/obory názvů/HybridConnections/Write | Vytvoří nebo aktualizuje vlastnosti HybridConnection. |
> | Akce | Microsoft. Relay/obory názvů/messagingPlan/Read | Získá plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. Relay/obory názvů/messagingPlan/Write | Aktualizuje plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. Relay/obory názvů/operationresults/Read | Získat stav operace oboru názvů |
> | Akce | Microsoft. Relay/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Akce | Microsoft. Relay/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Write | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Akce | Microsoft. Relay/obory názvů/Providers/Microsoft. Insights/logDefinitions/Read | Získá seznam popisů prostředků v názvech oborů názvů. |
> | Akce | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Akce | Microsoft.Relay/namespaces/read | Získat seznam popisu prostředku oboru názvů |
> | Akce | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Odebrat obor názvů ACS |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operation to update WcfRelay. Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operation to delete WcfRelay Authorization Rules |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Get the Connection String to WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Get the list of WcfRelay Authorization Rules |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Create WcfRelay Authorization Rules and Update its properties. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/Delete | Operation to delete WcfRelay Resource |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/read | Get list of WcfRelay Resource Descriptions |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/write | Create or Update WcfRelay properties. |
> | Akce | Microsoft.Relay/namespaces/write | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.Relay/operations/read | Získat operace |
> | Akce | Microsoft.Relay/register/action | Registers the subscription for the Relay resource provider and enables the creation of Relay resources |
> | Akce | Microsoft.Relay/unregister/action | Registers the subscription for the Relay resource provider and enables the creation of Relay resources |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Gets the availability status for the specified resource |
> | Akce | Microsoft.ResourceHealth/AvailabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Akce | Microsoft.ResourceHealth/events/read | Get Service Health Events for given subscription |
> | Akce | Microsoft.Resourcehealth/healthevent/action | Denotes the change in health state for the specified resource |
> | Akce | Microsoft.Resourcehealth/healthevent/Activated/action | Denotes the change in health state for the specified resource |
> | Akce | Microsoft.Resourcehealth/healthevent/InProgress/action | Denotes the change in health state for the specified resource |
> | Akce | Microsoft.Resourcehealth/healthevent/Pending/action | Denotes the change in health state for the specified resource |
> | Akce | Microsoft.Resourcehealth/healthevent/Resolved/action | Denotes the change in health state for the specified resource |
> | Akce | Microsoft.Resourcehealth/healthevent/Updated/action | Denotes the change in health state for the specified resource |
> | Akce | Microsoft.ResourceHealth/impactedResources/read | Get Impacted Resources for given subscription |
> | Akce | Microsoft.ResourceHealth/metadata/read | Gets Metadata |
> | Akce | Microsoft.ResourceHealth/Notifications/read | Receives Azure Resource Manager notifications |
> | Akce | Microsoft.ResourceHealth/Operations/read | Get the operations available for the Microsoft ResourceHealth |
> | Akce | Microsoft.ResourceHealth/register/action | Registers the subscription for the Microsoft ResourceHealth |
> | Akce | Microsoft.ResourceHealth/unregister/action | Unregisters the subscription for the Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Resources/calculateTemplateHash/action | Calculate the hash of provided template. |
> | Akce | Microsoft.Resources/checkPolicyCompliance/read | Check the compliance status of a given resource against resource policies. |
> | Akce | Microsoft.Resources/checkResourceName/action | Check the resource name for validity. |
> | Akce | Microsoft.Resources/deployments/cancel/action | Cancels a deployment. |
> | Akce | Microsoft.Resources/deployments/delete | Deletes a deployment. |
> | Akce | Microsoft.Resources/deployments/exportTemplate/action | Export template for a deployment |
> | Akce | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Akce | Microsoft.Resources/deployments/operationstatuses/read | Gets or lists deployment operation statuses. |
> | Akce | Microsoft.Resources/deployments/read | Gets or lists deployments. |
> | Akce | Microsoft.Resources/deployments/validate/action | Validates an deployment. |
> | Akce | Microsoft.Resources/deployments/whatIf/action | Predicts template deployment changes. |
> | Akce | Microsoft.Resources/deployments/write | Creates or updates an deployment. |
> | Akce | Microsoft.Resources/deploymentScripts/delete | Deletes a deployment script |
> | Akce | Microsoft.Resources/deploymentScripts/logs/read | Gets or lists deployment script logs |
> | Akce | Microsoft.Resources/deploymentScripts/read | Gets or lists deployment scripts |
> | Akce | Microsoft.Resources/deploymentScripts/write | Creates or updates a deployment script |
> | Akce | Microsoft.Resources/links/delete | Deletes a resource link. |
> | Akce | Microsoft.Resources/links/read | Gets or lists resource links. |
> | Akce | Microsoft.Resources/links/write | Creates or updates a resource link. |
> | Akce | Microsoft.Resources/marketplace/purchase/action | Purchases a resource from the marketplace. |
> | Akce | Microsoft.Resources/providers/read | Get the list of providers. |
> | Akce | Microsoft.Resources/resources/read | Get the list of resources based upon filters. |
> | Akce | Microsoft.Resources/subscriptions/locations/read | Gets the list of locations supported. |
> | Akce | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Akce | Microsoft.Resources/subscriptions/providers/read | Gets or lists resource providers. |
> | Akce | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/delete | Deletes a resource group and all its resources. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Gets or lists deployment operations. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Gets or lists deployment operation statuses. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Gets or lists deployments. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Creates or updates an deployment. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Moves resources from one resource group to another. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Gets the resources for the resource group. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validate move of resources from one resource group to another. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/write | Creates or updates a resource group. |
> | Akce | Microsoft.Resources/subscriptions/resources/read | Gets resources of a subscription. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/delete | Deletes a subscription tag. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/read | Gets or lists subscription tags. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Deletes a subscription tag value. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Gets or lists subscription tag values. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adds a subscription tag value. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/write | Adds a subscription tag. |
> | Akce | Microsoft.Resources/tags/delete | Removes all the tags on a resource. |
> | Akce | Microsoft.Resources/tags/read | Gets all the tags on a resource. |
> | Akce | Microsoft.Resources/tags/write | Updates the tags on a resource by replacing or merging existing tags with a new set of tags, or removing existing tags. |
> | Akce | Microsoft.Resources/tenants/read | Gets the list of tenants. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Scheduler/jobcollections/delete | Deletes job collection. |
> | Akce | Microsoft.Scheduler/jobcollections/disable/action | Disables job collection. |
> | Akce | Microsoft.Scheduler/jobcollections/enable/action | Enables job collection. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/delete | Deletes job. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generates Logic App definition based on a Scheduler Job. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Gets job history. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/read | Gets job. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/run/action | Runs job. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/write | Creates or updates job. |
> | Akce | Microsoft.Scheduler/jobcollections/read | Get Job Collection |
> | Akce | Microsoft.Scheduler/jobcollections/write | Creates or updates job collection. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Search/checkNameAvailability/action | Checks availability of the service name. |
> | Akce | Microsoft.Search/operations/read | Lists all of the available operations of the Microsoft.Search provider. |
> | Akce | Microsoft.Search/register/action | Registers the subscription for the search resource provider and enables the creation of search services. |
> | Akce | Microsoft.Search/searchServices/createQueryKey/action | Creates the query key. |
> | Akce | Microsoft.Search/searchServices/delete | Deletes the search service. |
> | Akce | Microsoft.Search/searchServices/deleteQueryKey/delete | Deletes the query key. |
> | Akce | Microsoft.Search/searchServices/listAdminKeys/action | Reads the admin keys. |
> | Akce | Microsoft.Search/searchServices/listQueryKeys/action | Returns the list of query API keys for the given Azure Search service. |
> | Akce | Microsoft.Search/searchServices/listQueryKeys/read | Returns the list of query API keys for the given Azure Search service. |
> | Akce | Microsoft.Search/searchServices/privateEndpointConnectionProxies/delete | Odstraní existující proxy připojení privátního koncového bodu. |
> | Akce | Microsoft.Search/searchServices/privateEndpointConnectionProxies/read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy |
> | Akce | Microsoft.Search/searchServices/privateEndpointConnectionProxies/validate/action | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Akce | Microsoft.Search/searchServices/privateEndpointConnectionProxies/write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy |
> | Akce | Microsoft.Search/searchServices/read | Reads the search service. |
> | Akce | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenerates the admin key. |
> | Akce | Microsoft.Search/searchServices/start/action | Starts the search service. |
> | Akce | Microsoft.Search/searchServices/stop/action | Stops the search service. |
> | Akce | Microsoft.Search/searchServices/write | Creates or updates the search service. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Enforces the given traffic hardening rules by creating matching security rules on the given Network Security Group(s) |
> | Akce | Microsoft.Security/adaptiveNetworkHardenings/read | Gets Adaptive Network Hardening recommendations of an Azure protected resource |
> | Akce | Microsoft.Security/advancedThreatProtectionSettings/read | Gets the Advanced Threat Protection Settings for the resource |
> | Akce | Microsoft.Security/advancedThreatProtectionSettings/write | Updates the Advanced Threat Protection Settings for the resource |
> | Akce | Microsoft.Security/alerts/read | Gets all available security alerts |
> | Akce | Microsoft.Security/applicationWhitelistings/read | Gets the application whitelistings |
> | Akce | Microsoft.Security/applicationWhitelistings/write | Creates a new application whitelisting or updates an existing one |
> | Akce | Microsoft.Security/assessmentMetadata/read | Get available security assessment metadata on your subscription |
> | Akce | Microsoft.Security/assessmentMetadata/write | Create or update a security assessment metadata |
> | Akce | Microsoft.Security/assessments/read | Get security assessments on your subscription |
> | Akce | Microsoft.Security/assessments/write | Create or update security assessments on your subscription |
> | Akce | Microsoft.Security/complianceResults/read | Gets the compliance results for the resource |
> | Akce | Microsoft.Security/informationProtectionPolicies/read | Gets the information protection policies for the resource |
> | Akce | Microsoft.Security/informationProtectionPolicies/write | Updates the information protection policies for the resource |
> | Akce | Microsoft.Security/locations/alerts/activate/action | Activate a security alert |
> | Akce | Microsoft.Security/locations/alerts/dismiss/action | Dismiss a security alert |
> | Akce | Microsoft.Security/locations/alerts/read | Gets all available security alerts |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Deletes the just-in-time network access policy |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initiates a just-in-time network access policy request |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Gets the just-in-time network access policies |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Creates a new just-in-time network access policy or updates an existing one |
> | Akce | Microsoft.Security/locations/read | Gets the security data location |
> | Akce | Microsoft.Security/locations/tasks/activate/action | Activate a security recommendation |
> | Akce | Microsoft.Security/locations/tasks/dismiss/action | Dismiss a security recommendation |
> | Akce | Microsoft.Security/locations/tasks/read | Gets all available security recommendations |
> | Akce | Microsoft.Security/locations/tasks/resolve/action | Resolve a security recommendation |
> | Akce | Microsoft.Security/locations/tasks/start/action | Start a security recommendation |
> | Akce | Microsoft.Security/policies/read | Gets the security policy |
> | Akce | Microsoft.Security/policies/write | Updates the security policy |
> | Akce | Microsoft.Security/pricings/delete | Deletes the pricing settings for the scope |
> | Akce | Microsoft.Security/pricings/read | Gets the pricing settings for the scope |
> | Akce | Microsoft.Security/pricings/write | Updates the pricing settings for the scope |
> | Akce | Microsoft.Security/register/action | Registers the subscription for Azure Security Center |
> | Akce | Microsoft.Security/securityContacts/delete | Deletes the security contact |
> | Akce | Microsoft.Security/securityContacts/read | Gets the security contact |
> | Akce | Microsoft.Security/securityContacts/write | Updates the security contact |
> | Akce | Microsoft.Security/securitySolutions/delete | Deletes a security solution |
> | Akce | Microsoft.Security/securitySolutions/read | Gets the security solutions |
> | Akce | Microsoft.Security/securitySolutions/write | Creates a new security solution or updates an existing one |
> | Akce | Microsoft.Security/securitySolutionsReferenceData/read | Gets the security solutions reference data |
> | Akce | Microsoft.Security/securityStatuses/read | Gets the security health statuses for Azure resources |
> | Akce | Microsoft.Security/securityStatusesSummaries/read | Gets the security statuses summaries for the scope |
> | Akce | Microsoft.Security/settings/read | Gets the settings for the scope |
> | Akce | Microsoft.Security/settings/write | Updates the settings for the scope |
> | Akce | Microsoft.Security/tasks/read | Gets all available security recommendations |
> | Akce | Microsoft.Security/unregister/action | Unregisters the subscription from Azure Security Center |
> | Akce | Microsoft.Security/webApplicationFirewalls/delete | Deletes a web application firewall |
> | Akce | Microsoft.Security/webApplicationFirewalls/read | Gets the web application firewalls |
> | Akce | Microsoft.Security/webApplicationFirewalls/write | Creates a new web application firewall or updates an existing one |
> | Akce | Microsoft.Security/workspaceSettings/connect/action | Change workspace settings reconnection settings |
> | Akce | Microsoft.Security/workspaceSettings/delete | Deletes the workspace settings |
> | Akce | Microsoft.Security/workspaceSettings/read | Gets the workspace settings |
> | Akce | Microsoft.Security/workspaceSettings/write | Updates the workspace settings |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/delete | Deleting a diagnostic setting |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/read | Reading a diagnostic setting |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/write | Writing a diagnostic setting |
> | Akce | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Reading a diagnostic setting categories |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.SecurityInsights/Aggregations/read | Gets aggregated information |
> | Akce | Microsoft.SecurityInsights/alertRules/actions/delete | Deletes the response actions of an alert rule |
> | Akce | Microsoft.SecurityInsights/alertRules/actions/read | Gets the response actions of an alert rule |
> | Akce | Microsoft.SecurityInsights/alertRules/actions/write | Updates the response actions of an alert rule |
> | Akce | Microsoft.SecurityInsights/alertRules/delete | Deletes alert rules |
> | Akce | Microsoft.SecurityInsights/alertRules/read | Gets the alert rules |
> | Akce | Microsoft.SecurityInsights/alertRules/write | Updates alert rules |
> | Akce | Microsoft.SecurityInsights/Bookmarks/delete | Deletes bookmarks |
> | Akce | Microsoft.SecurityInsights/Bookmarks/expand/action | Gets related entities of an entity by a specific expansion |
> | Akce | Microsoft.SecurityInsights/Bookmarks/read | Gets bookmarks |
> | Akce | Microsoft.SecurityInsights/Bookmarks/write | Updates bookmarks |
> | Akce | Microsoft.SecurityInsights/cases/comments/read | Gets the case comments |
> | Akce | Microsoft.SecurityInsights/cases/comments/write | Creates the case comments |
> | Akce | Microsoft.SecurityInsights/cases/delete | Deletes a case |
> | Akce | Microsoft.SecurityInsights/cases/investigations/read | Gets the case investigations |
> | Akce | Microsoft.SecurityInsights/cases/investigations/write | Updates the metadata of a case |
> | Akce | Microsoft.SecurityInsights/cases/read | Gets a case |
> | Akce | Microsoft.SecurityInsights/cases/write | Updates a case |
> | Akce | Microsoft.SecurityInsights/dataConnectors/delete | Deletes a data connector |
> | Akce | Microsoft.SecurityInsights/dataConnectors/read | Gets the data connectors |
> | Akce | Microsoft. SecurityInsights/dataconnects/Write | Aktualizuje datový konektor. |
> | Akce | Microsoft. SecurityInsights/Register/Action | Zaregistruje předplatné do Azure Sentinel. |
> | Akce | Microsoft. SecurityInsights/nastavení/číst | Načte nastavení |
> | Akce | Microsoft. SecurityInsights/nastavení/Write | Nastavení aktualizací |
> | Akce | Microsoft. SecurityInsights/zrušit registraci/akce | Zruší registraci předplatného z Azure Sentinel. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. ServiceBus/checkNameAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. |
> | Akce | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. Toto rozhraní API je zastaralé, použijte místo toho CheckNameAvailability. |
> | Akce | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla virtuální sítě v poskytovateli prostředků ServiceBus pro zadanou virtuální síť. |
> | Akce | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/Action | Aktualizuje autorizační pravidlo oboru názvů. Toto rozhraní API je zastaralé. Místo toho prosím použijte volání PUT k aktualizaci autorizačního pravidla oboru názvů.. Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Akce | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Akce | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft. ServiceBus/obory názvů/DELETE | Odstranit prostředek oboru názvů |
> | Akce | Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/klíče listkey/Action | Získá klíče autorizačních pravidel pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat autorizační pravidla primárního oboru názvů pro zotavení po havárii |
> | Akce | Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/breakPairing/Action | Zakáže zotavení po havárii a zastaví replikaci změn z primárních oborů názvů na sekundární. |
> | Akce | Microsoft. ServiceBus/obory názvů/disasterrecoveryconfigs/checkNameAvailability/Action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tuto operaci lze vyvolat pouze prostřednictvím primárního oboru názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/převzetí služeb při selhání/akce | Vyvolá převzetí služeb při selhání GEOGRAFICKého zotavení po havárii a změní konfiguraci aliasu oboru názvů tak, aby odkazoval na sekundární obor názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Odstraní filtr Event Grid přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Získá filtr Event Grid přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event Grid přidružený k oboru názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/eventhubs/Read | Získá seznam popisů prostředků EventHub. |
> | Akce | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Odstranit prostředek filtru IP |
> | Akce | Microsoft. ServiceBus/obory názvů/ipFilterRules/Read | Získat prostředek filtru IP |
> | Akce | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Vytvořit prostředek filtru IP |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/přijmout/akce | Příjem zpráv |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/odeslání/akce | Odesílání zpráv |
> | Akce | Microsoft. ServiceBus/obory názvů/messagingPlan/Read | Získá plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. ServiceBus/obory názvů/messagingPlan/Write | Aktualizuje plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft. ServiceBus/obory názvů/migrace/akce | Migrovat operaci oboru názvů |
> | Akce | Microsoft. ServiceBus/obory názvů/migrationConfigurations/DELETE | Odstraní konfiguraci migrace. |
> | Akce | Microsoft. ServiceBus/obory názvů/migrationConfigurations/Read | Získá konfiguraci migrace, která indikuje stav migrace a probíhající operace replikace. |
> | Akce | Microsoft. ServiceBus/obory názvů/migrationConfigurations/REVERT/Action | Obnoví migraci oboru názvů Standard na Premium. |
> | Akce | Microsoft. ServiceBus/obory názvů/migrationConfigurations/upgrade/akce | Přiřadí DNS přidružené k oboru názvů Standard do oboru názvů Premium, který dokončí migraci a zastaví synchronizaci prostředků z oboru názvů Standard na Premium. |
> | Akce | Microsoft. ServiceBus/obory názvů/migrationConfigurations/Write | Vytvoří nebo aktualizuje konfiguraci migrace. Tím se spustí synchronizace prostředků z úrovně Standard na obor názvů Premium. |
> | Akce | Microsoft. ServiceBus/obory názvů/networkruleset/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. ServiceBus/obory názvů/networkruleset/Read | Získá prostředek NetworkRuleSet. |
> | Akce | Microsoft. ServiceBus/obory názvů/networkruleset/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. ServiceBus/obory názvů/networkrulesets/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. ServiceBus/obory názvů/networkrulesets/Read | Získá prostředek NetworkRuleSet. |
> | Akce | Microsoft. ServiceBus/obory názvů/networkrulesets/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. ServiceBus/obory názvů/operationresults/Read | Získat stav operace oboru názvů |
> | Akce | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Write | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/logDefinitions/Read | Získá seznam popisů prostředků v názvech oborů názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/Action | Operace pro aktualizaci fronty Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel fronty |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/klíče listkey/Action | Získá připojovací řetězec k zařazení do fronty. |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/Read |  Získá seznam autorizačních pravidel fronty. |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/Write | Vytvořte autorizační pravidla fronty a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Akce | Microsoft. ServiceBus/obory názvů/Queues/DELETE | Operace odstranění prostředku fronty |
> | Akce | Microsoft. ServiceBus/obory názvů/fronty/čtení | Získá seznam popisů prostředků fronty. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/write | Vytvoří nebo aktualizuje vlastnosti fronty. |
> | Akce | Microsoft. ServiceBus/obory názvů/číst | Získat seznam popisu prostředku oboru názvů |
> | Akce | Microsoft. ServiceBus/obory názvů/removeAcsNamepsace/Action | Odebrat obor názvů ACS |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/Action | Operace pro aktualizaci tématu Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel tématu |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k tématu |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/číst |  Získat seznam autorizačních pravidel pro témata |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/Write | Vytvořte pravidla autorizace tématu a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/DELETE | Operace odstranění prostředku tématu |
> | Akce | Microsoft. ServiceBus/obory názvů/témata/číst | Získá seznam popisů prostředků tématu. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operace odstranění prostředku TopicSubscription |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Získá seznam popisů prostředků TopicSubscription. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operace odstranění prostředku pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Získá seznam popisů prostředků pravidel. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Vytvoří nebo aktualizuje vlastnosti pravidla. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Vytvoří nebo aktualizuje vlastnosti TopicSubscription. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/write | Vytvoří nebo aktualizuje vlastnosti tématu. |
> | Akce | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. ServiceBus/obory názvů/virtualNetworkRules/Read | Získá prostředek pravidla virtuální sítě. |
> | Akce | Microsoft. ServiceBus/obory názvů/virtualNetworkRules/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft. ServiceBus/obory názvů/zápis | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.ServiceBus/operations/read | Získat operace |
> | Akce | Microsoft. ServiceBus/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |
> | Akce | Microsoft. ServiceBus/SKU/číst | Získá seznam popisů prostředků SKU. |
> | Akce | Microsoft. ServiceBus/SKU/Regions/Read | Získá seznam popisů prostředků SkuRegions. |
> | Akce | Microsoft. ServiceBus/zrušit registraci/akce | Zaregistruje předplatné pro poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ServiceFabric/clusters/applications/delete | Odstranění libovolné aplikace |
> | Akce | Microsoft. ServiceFabric/clustery/aplikace/číst | Čtení libovolné aplikace |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/delete | Odstranění jakékoli služby |
> | Akce | Microsoft. ServiceFabric/Clusters/Applications/Services/partitions/Read | Číst libovolný oddíl |
> | Akce | Microsoft. ServiceFabric/clustery/aplikace/služby/oddíly/repliky/čtení | Čtení libovolné repliky |
> | Akce | Microsoft. ServiceFabric/Clusters/Applications/Services/Ready | Čtení libovolné služby |
> | Akce | Microsoft. ServiceFabric/Clusters/Applications/Services/STATUSS/Read | Číst libovolný stav služby |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/write | Vytvoření nebo aktualizace jakékoli služby |
> | Akce | Microsoft.ServiceFabric/clusters/applications/write | Vytvořit nebo aktualizovat libovolnou aplikaci |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Odstranění libovolného typu aplikace |
> | Akce | Microsoft. ServiceFabric/Clusters/applicationTypes/Read | Čtení libovolného typu aplikace |
> | Akce | Microsoft. ServiceFabric/Clusters/applicationTypes/Versions/DELETE | Odstranění libovolné verze typu aplikace |
> | Akce | Microsoft. ServiceFabric/Clusters/applicationTypes/verze/číst | Read any Application Type Version |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Create or Update any Application Type Version |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/write | Create or Update any Application Type |
> | Akce | Microsoft.ServiceFabric/clusters/delete | Delete any Cluster |
> | Akce | Microsoft.ServiceFabric/clusters/nodes/read | Read any Node |
> | Akce | Microsoft.ServiceFabric/clusters/read | Read any Cluster |
> | Akce | Microsoft.ServiceFabric/clusters/statuses/read | Read any Cluster Status |
> | Akce | Microsoft.ServiceFabric/clusters/write | Create or Update any Cluster |
> | Akce | Microsoft.ServiceFabric/locations/clusterVersions/read | Read any Cluster Version |
> | Akce | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Read any Cluster Version for a specific environment |
> | Akce | Microsoft.ServiceFabric/locations/operationresults/read | Read any Operation Results |
> | Akce | Microsoft.ServiceFabric/locations/operations/read | Read any Operations by location |
> | Akce | Microsoft.ServiceFabric/operations/read | Read any Available Operations |
> | Akce | Microsoft.ServiceFabric/register/action | Register any Action |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.SignalRService/locations/checknameavailability/action | Checks if a name is available for use with a new SignalR service |
> | Akce | Microsoft.SignalRService/locations/operationresults/signalr/read | Query the status of an asynchronous operation |
> | Akce | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Query the status of an asynchronous operation |
> | Akce | Microsoft.SignalRService/locations/usages/read | Get the quota usages for Azure SignalR service |
> | Akce | Microsoft.SignalRService/operationresults/read | Query the status of an asynchronous operation |
> | Akce | Microsoft.SignalRService/operations/read | List the operations for Azure SignalR service. |
> | Akce | Microsoft.SignalRService/operationstatus/read | Query the status of an asynchronous operation |
> | Akce | Microsoft.SignalRService/register/action | Registers the 'Microsoft.SignalRService' resource provider with a subscription |
> | Akce | Microsoft.SignalRService/SignalR/delete | Delete the entire SignalR service |
> | Akce | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Delete an event grid filter from a SignalR. |
> | Akce | Microsoft.SignalRService/SignalR/eventGridFilters/read | Get the properties of the specified event grid filter or lists all the event grid filters for the specified SignalR. |
> | Akce | Microsoft.SignalRService/SignalR/eventGridFilters/write | Create or update an event grid filter for a SignalR with the specified parameters. |
> | Akce | Microsoft.SignalRService/SignalR/listkeys/action | View the value of SignalR access keys in the management portal or through API |
> | Akce | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/delete | Delete a Private Endpoint Connection Proxy |
> | Akce | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/read | Read a Private Endpoint Connetion Proxy |
> | Akce | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/validate/action | Validate a Private Endpoint Connection Proxy |
> | Akce | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/write | Create a Private Endpoint Connection Proxy |
> | Akce | Microsoft.SignalRService/SignalR/privateEndpointConnections/read | Read a Private Endpoint Connection |
> | Akce | Microsoft.SignalRService/SignalR/privateEndpointConnections/write | Approve or reject a Private Endpoint Connection |
> | Akce | Microsoft.SignalRService/SignalR/privateLinkResources/read | List all SignalR Private Link Resources |
> | Akce | Microsoft.SignalRService/SignalR/read | View the SignalR's settings and configurations in the management portal or through API |
> | Akce | Microsoft.SignalRService/SignalR/regeneratekey/action | Change the value of SignalR access keys in the management portal or through API |
> | Akce | Microsoft.SignalRService/SignalR/restart/action | To restart an Azure SignalR service in the management portal or through API. There will be certain downtime. |
> | Akce | Microsoft.SignalRService/SignalR/write | Modify the SignalR's settings and configurations in the management portal or through API |
> | Akce | Microsoft.SignalRService/unregister/action | Unregisters the 'Microsoft.SignalRService' resource provider with a subscription |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lists application artifacts of application definition. |
> | Akce | Microsoft.Solutions/applicationDefinitions/delete | Removes an application definition. |
> | Akce | Microsoft.Solutions/applicationDefinitions/read | Retrieves a list of application definitions. |
> | Akce | Microsoft.Solutions/applicationDefinitions/write | Add or modify an application definition. |
> | Akce | Microsoft.Solutions/applications/applicationArtifacts/read | Lists application artifacts. |
> | Akce | Microsoft.Solutions/applications/delete | Removes an application. |
> | Akce | Microsoft.Solutions/applications/read | Retrieves a list of applications. |
> | Akce | Microsoft.Solutions/applications/refreshPermissions/action | Refreshes application permission(s). |
> | Akce | Microsoft.Solutions/applications/updateAccess/action | Updates application access. |
> | Akce | Microsoft.Solutions/applications/write | Vytvoří aplikaci. |
> | Akce | Microsoft.Solutions/jitRequests/delete | Remove a JitRequest |
> | Akce | Microsoft.Solutions/jitRequests/read | Retrieves a list of JitRequests |
> | Akce | Microsoft.Solutions/jitRequests/write | Creates a JitRequest |
> | Akce | Microsoft.Solutions/locations/operationStatuses/read | Reads the operation status for the resource. |
> | Akce | Microsoft.Solutions/operations/read | Gets the list of operations. |
> | Akce | Microsoft.Solutions/register/action | Register to Solutions. |
> | Akce | Microsoft.Solutions/unregister/action | Unregisters from Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Sql/checkNameAvailability/action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Akce | Microsoft.Sql/instancePools/delete | Deletes an instance pool |
> | Akce | Microsoft.Sql/instancePools/read | Gets an instance pool |
> | Akce | Microsoft.Sql/instancePools/usages/read | Gets an instance pool's usage info |
> | Akce | Microsoft.Sql/instancePools/write | Creates or updates an instance pool |
> | Akce | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Načíst výsledek operace set zásad auditování objektu BLOB rozšířeného serveru |
> | Akce | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Načíst výsledek operace nastavení zásad auditování objektu BLOB serveru |
> | Akce | Microsoft. SQL/umístění/možnosti/číst | Získá možnosti pro toto předplatné v daném umístění. |
> | Akce | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Získá stav databázové operace. |
> | Akce | Microsoft. SQL/Locations/databaseOperationResults/Read | Získá stav databázové operace. |
> | Akce | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Načte probíhající operace na odstraněném serveru. |
> | Akce | Microsoft.Sql/locations/deletedServerOperationResults/read | Načte probíhající operace na odstraněném serveru. |
> | Akce | Microsoft.Sql/locations/deletedServers/read | Vrátí seznam odstraněných serverů nebo získá vlastnosti určeného odstraněného serveru. |
> | Akce | Microsoft. SQL/Locations/deletedServers/Recovery/Action | Obnovení odstraněného serveru |
> | Akce | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Získá asynchronní operaci Azure pro asynchronní operaci elastického fondu. |
> | Akce | Microsoft. SQL/Locations/elasticPoolOperationResults/Read | Získá výsledek operace elastického fondu. |
> | Akce | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Načte probíhající operace na transparentní ochraně šifrování šifrování dat. |
> | Akce | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Načte probíhající operace na transparentní ochraně šifrování šifrování dat. |
> | Akce | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Načíst výsledek operace set zásad auditování objektu BLOB rozšířeného serveru |
> | Akce | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Načíst výsledek operace set zásad auditování objektu BLOB rozšířeného serveru |
> | Akce | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Získá stav operace pravidla brány firewall. |
> | Akce | Microsoft. SQL/Locations/firewallRulesOperationResults/Read | Získá stav operace pravidla brány firewall. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/delete | Odstraní skupinu převzetí služeb při selhání existující instance. |
> | Akce | Microsoft. SQL/Locations/instanceFailoverGroups/převzetí služeb při selhání/akce | Spustí plánované převzetí služeb při selhání v existující skupině převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Spustí vynucené převzetí služeb při selhání existující skupiny instancí. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/read | Vrátí seznam skupin instancí převzetí služeb při selhání nebo získá vlastnosti pro zadanou skupinu převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/write | Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti nebo značky zadané instance skupiny převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Získá stav operace fondu instancí. |
> | Akce | Microsoft. SQL/Locations/instancePoolOperationResults/Read | Získá výsledek pro operaci fondu instancí. |
> | Akce | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Vrátí podrobnosti o konkrétní operaci asynchronního koncového bodu rozhraní Azure. |
> | Akce | Microsoft. SQL/Locations/interfaceEndpointProfileOperationResults/Read | Vrátí podrobné informace o zadané operaci profilu koncového bodu rozhraní. |
> | Akce | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Získá stav operace agenta úlohy. |
> | Akce | Microsoft. SQL/Locations/jobAgentOperationResults/Read | Získá výsledek operace agenta úlohy. |
> | Akce | Microsoft. SQL/Locations/longTermRetentionBackups/Read | Uvádí dlouhodobé zálohy uchovávání pro každou databázi na každém serveru v lokalitě. |
> | Akce | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read | Uvádí dlouhodobé zálohy uchovávání pro každou databázi na serveru. |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Odstraní zálohu dlouhodobého uchovávání. |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Uvádí dlouhodobé zálohy uchovávání pro databázi. |
> | Akce | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Completes managed database restore operation |
> | Akce | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Načte probíhající operace na transparentní ochraně šifrování spravované instance šifrování dat. |
> | Akce | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Načte probíhající operace na transparentní ochraně šifrování spravované instance šifrování dat. |
> | Akce | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Načte probíhající operace na transparentních klíčích instance spravovaného šifrování dat. |
> | Akce | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Načte probíhající operace na transparentních klíčích instance spravovaného šifrování dat. |
> | Akce | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/Read | Získá stav operace dlouhodobé zásady uchovávání informací pro spravovanou databázi. |
> | Akce | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyOperationResults/Read | Získá stav operace dlouhodobé zásady uchovávání informací pro spravovanou databázi. |
> | Akce | Microsoft. SQL/Locations/managedShortTermRetentionPolicyOperationResults/Read | Získá stav operace krátkodobého ukládání zásad uchovávání informací. |
> | Akce | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Načte probíhající operace na transparentní šifrování dat spravované databáze. |
> | Akce | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Načte probíhající operace na transparentní šifrování dat spravované databáze. |
> | Akce | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Získá výsledek operace připojení privátního koncového bodu. |
> | Akce | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. SQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Akce | Microsoft. SQL/umístění/čtení | Získá dostupná umístění pro dané předplatné. |
> | Akce | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft. SQL/Locations/serverKeyOperationResults/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Akce | Microsoft. SQL/Locations/shortTermRetentionPolicyOperationResults/Read | Získá stav operace krátkodobého ukládání zásad uchovávání informací. |
> | Akce | Microsoft. SQL/Locations/syncAgentOperationResults/Read | Načíst výsledek operace prostředků agenta synchronizace |
> | Akce | Microsoft.Sql/locations/syncDatabaseIds/read | Načtení ID synchronizační databáze pro určitou oblast a předplatné |
> | Akce | Microsoft.Sql/locations/syncGroupOperationResults/read | Načíst výsledek operace prostředku skupiny synchronizace |
> | Akce | Microsoft. SQL/Locations/syncMemberOperationResults/Read | Načíst výsledek operace prostředku člena synchronizace |
> | Akce | Microsoft. SQL/umístění/použití/čtení | Získá kolekci metrik využití pro toto předplatné v daném umístění. |
> | Akce | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Returns the details of the specified virtual network rules azure async operation  |
> | Akce | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Returns the details of the specified virtual network rules operation  |
> | Akce | Microsoft.Sql/managedInstances/administrators/delete | Deletes an existing administrator of managed instance. |
> | Akce | Microsoft.Sql/managedInstances/administrators/read | Gets a list of managed instance administrators. |
> | Akce | Microsoft.Sql/managedInstances/administrators/write | Creates or updates managed instance administrator with the specified parameters. |
> | Akce | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/read | Gets a long term retention policy for a managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/write | Updates a long term retention policy for a managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Gets a short term retention policy for a managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Updates a short term retention policy for a managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/columns/read | Return a list of columns for a managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/completeRestore/action | Completes managed database restore operation |
> | Akce | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Popisky citlivosti aktualizace v dávce |
> | Akce | Microsoft.Sql/managedInstances/databases/delete | Deletes an existing managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for managed instance databases |
> | Akce | Microsoft.Sql/managedInstances/databases/read | Gets existing managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/write | Doporučené popisky citlivosti aktualizace Batch |
> | Akce | Microsoft.Sql/managedInstances/databases/restoreDetails/read | Returns managed database restore details while restore is in progress. |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/read | Get a managed database schema. (schema only) |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Get a managed database column (schema only) |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Odstraní popisek citlivosti daného sloupce. |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Zakázat doporučení citlivosti na daném sloupci |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Povolit doporučení citlivosti na daném sloupci |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Získá popisek citlivosti daného sloupce. |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Vytvoří nebo aktualizuje popisek citlivosti daného sloupce. |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Get a managed database table (schema only) |
> | Akce | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Retrieve a list of managed database threat detection policies configured for a given server |
> | Akce | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Change the database threat detection policy for a given managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/securityEvents/read | Retrieves the managed database security events |
> | Akce | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Retrieve details of the database Transparent Data Encryption on a given managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Change the database Transparent Data Encryption for a given managed database |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Načtení zásad posouzení ohrožení zabezpečení na givendatabase |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Odebrání směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Získat směrný plán pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Změna směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Převede existující výsledek kontroly na formát pro lidské čtení. Pokud už existuje, nic se nestane. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Provést kontrolu databáze posouzení ohrožení zabezpečení. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Vrátí seznam vyhodnocování ohrožení zabezpečení databáze nebo získá záznam o kontrole pro zadané ID vyhledávání. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Změna posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/write | Creates a new database or updates an existing database. |
> | Akce | Microsoft.Sql/managedInstances/delete | Deletes an existing  managed instance. |
> | Akce | Microsoft.Sql/managedInstances/encryptionProtector/read | Vrátí seznam chráněných šifrovacích serverů nebo získá vlastnosti pro zadané ochrany šifrování serveru. |
> | Akce | Microsoft.Sql/managedInstances/encryptionProtector/revalidate/action | Aktualizuje vlastnosti pro zadanou ochranu pomocí šifrování serveru. |
> | Akce | Microsoft.Sql/managedInstances/encryptionProtector/write | Aktualizuje vlastnosti pro zadanou ochranu pomocí šifrování serveru. |
> | Akce | Microsoft.Sql/managedInstances/inaccessibleManagedDatabases/read | Gets a list of inaccessible managed databases in a managed instance |
> | Akce | Microsoft.Sql/managedInstances/keys/delete | Deletes an existing Azure SQL Managed Instance  key. |
> | Akce | Microsoft.Sql/managedInstances/keys/read | Return the list of managed instance keys or gets the properties for the specified managed instance key. |
> | Akce | Microsoft.Sql/managedInstances/keys/write | Creates a key with the specified parameters or update the properties or tags for the specified managed instance key. |
> | Akce | Microsoft.Sql/managedInstances/metricDefinitions/read | Get managed instance metric definitions |
> | Akce | Microsoft.Sql/managedInstances/metrics/read | Get managed instance metrics |
> | Akce | Microsoft.Sql/managedInstances/operations/cancel/action | Cancels Azure SQL Managed Instance pending asynchronous operation that is not finished yet. |
> | Akce | Microsoft.Sql/managedInstances/operations/read | Get managed instance operations |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for managed instances |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for managed instances |
> | Akce | Microsoft.Sql/managedInstances/read | Return the list of managed instances or gets the properties for the specified managed instance. |
> | Akce | Microsoft.Sql/managedInstances/recoverableDatabases/read | Returns a list of recoverable managed databases |
> | Akce | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Gets a short term retention policy for a dropped managed database |
> | Akce | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Updates a short term retention policy for a dropped managed database |
> | Akce | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Returns a list of restorable dropped managed databases. |
> | Akce | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Retrieve a list of managed server threat detection policies configured for a given server |
> | Akce | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Změna zásad zjišťování hrozeb spravovaného serveru pro daný spravovaný server |
> | Akce | Microsoft.Sql/managedInstances/tdeCertificates/action | Vytvořit nebo aktualizovat certifikát TDE |
> | Akce | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro danou spravovanou instanci |
> | Akce | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Načtení zásad posouzení ohrožení zabezpečení v dané spravované instanci |
> | Akce | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Změna posouzení ohrožení zabezpečení pro danou spravovanou instanci |
> | Akce | Microsoft.Sql/managedInstances/write | Vytvoří spravovanou instanci se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou spravovanou instanci. |
> | Akce | Microsoft.Sql/operations/read | Získá dostupné operace REST. |
> | Akce | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Určuje, jestli má uživatel povolený souhlas s připojením privátního koncového bodu. |
> | Akce | Microsoft. SQL/registr/Action | Zaregistruje předplatné pro poskytovatele prostředků Microsoft SQL Database a povolí vytváření databází Microsoft SQL. |
> | Akce | Microsoft. SQL/servery/Administrators/DELETE | Odstraní určitý objekt správce Azure Active Directory. |
> | Akce | Microsoft. SQL/servery/správci/číst | Získá konkrétní objekt správce Azure Active Directory. |
> | Akce | Microsoft. SQL/servery/správci/zapisovat | Přidá nebo aktualizuje určitý objekt správce Azure Active Directory. |
> | Akce | Microsoft. SQL/servery/poradci/číst | Vrátí seznam poradců dostupných pro server. |
> | Akce | Microsoft. SQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí zadaného Poradce pro server. |
> | Akce | Microsoft. SQL/servery/Advisory/recommendedActions/Write | Použití doporučené akce na serveru |
> | Akce | Microsoft. SQL/servery/poradci/zapisovat | Aktualizuje stav automatického spuštění služby Advisor na úrovni serveru. |
> | Akce | Microsoft. SQL/servery/auditingPolicies/Read | Načte podrobnosti o výchozích zásadách auditování tabulek serveru nakonfigurovaných na daném serveru. |
> | Akce | Microsoft.Sql/servers/auditingPolicies/write | Změna výchozího auditování tabulky serveru pro daný server |
> | Akce | Microsoft.Sql/servers/auditingSettings/operationResults/read | Načíst výsledek operace nastavení zásad auditování objektu BLOB serveru |
> | Akce | Microsoft.Sql/servers/auditingSettings/read | Načte podrobnosti o zásadách auditování objektů BLOB serveru nakonfigurovaných na daném serveru. |
> | Akce | Microsoft.Sql/servers/auditingSettings/write | Změna auditování objektů BLOB serveru pro daný server |
> | Akce | Microsoft. SQL/servery/automaticTuning/Read | Vrátí nastavení automatického ladění pro server. |
> | Akce | Microsoft. SQL/servery/automaticTuning/Write | Aktualizuje nastavení automatického ladění pro server a vrátí aktualizované nastavení. |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Odstraní existující archivační Trezor záloh. |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Tato operace slouží k získání dlouhodobého trezoru pro dlouhodobé uchovávání záloh. Vrátí informace o trezoru zaregistrovanému na tomto serveru. |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Tato operace se používá k registraci trezoru dlouhodobého uchovávání záloh na server. |
> | Akce | Microsoft. SQL/servery/communicationLinks/DELETE | Odstraní existující odkaz na komunikaci serveru. |
> | Akce | Microsoft. SQL/servery/communicationLinks/Read | Vrátí seznam komunikačních propojení určeného serveru. |
> | Akce | Microsoft. SQL/servery/communicationLinks/Write | Vytvoří nebo aktualizuje odkaz na komunikaci serveru. |
> | Akce | Microsoft. SQL/servery/connectionPolicies/Read | Vrátí seznam zásad připojení k serveru u zadaného serveru. |
> | Akce | Microsoft. SQL/servery/connectionPolicies/Write | Vytvoří nebo aktualizuje zásadu připojení serveru. |
> | Akce | Microsoft.Sql/servers/databases/advisors/read | Vrátí seznam poradců dostupných pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Vrátí seznam doporučených akcí zadaného Poradce pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Použití doporučené akce v databázi |
> | Akce | Microsoft.Sql/servers/databases/advisors/write | Aktualizuje stav automatického spuštění služby Advisor na úrovni databáze. |
> | Akce | Microsoft.Sql/servers/databases/auditingPolicies/read | Načíst podrobnosti o zásadách auditování tabulek konfigurovaných pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingPolicies/write | Změna zásad auditování v tabulce pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingSettings/read | Načte podrobnosti o zásadách auditování objektů BLOB konfigurovaných pro danou databázi. |
> | Akce | Microsoft.Sql/servers/databases/auditingSettings/write | Změna zásad auditování objektů BLOB pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu objektu BLOB databáze |
> | Akce | Microsoft.Sql/servers/databases/automaticTuning/read | Vrátí nastavení automatického ladění pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje nastavení automatického ladění pro databázi a vrátí aktualizovaná nastavení. |
> | Akce | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Získá stav databázové operace. |
> | Akce | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Vrátí seznam zásad archivace zálohy zadané databáze. |
> | Akce | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Vytvoří nebo aktualizuje zásadu archivace zálohy databáze. |
> | Akce | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Získá krátkodobé zásady uchovávání informací pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Aktualizuje krátkodobé zásady uchovávání informací pro databázi. |
> | Akce | Microsoft. SQL/servery/databáze/sloupce/číst | Vrátí seznam sloupců pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/connectionPolicies/read | Načíst podrobnosti o zásadách připojení konfigurovaných pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/connectionPolicies/write | Změna zásad připojení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Akce | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Popisky citlivosti aktualizace v dávce |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Vrátí seznam zásad pro maskování dat databáze. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Odstraní pravidlo zásad maskování dat pro danou databázi. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Načte Podrobnosti pravidla pro maskování dat nakonfigurovaného pro danou databázi. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Změna pravidla zásad maskování dat pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Změna zásad maskování dat pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Vrátí informace o kroku distribuovaného dotazu dotazu datového skladu pro vybrané ID kroku. |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Vrátí informace o distribučním dotazu datového skladu pro vybrané ID dotazu. |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Načte aktivity uživatelů SQL Data Warehouse instance, která zahrnuje běžící a pozastavené dotazy. |
> | Akce | Microsoft.Sql/servers/databases/delete | Odstraní existující databázi. |
> | Akce | Microsoft. SQL/servery/databáze/export/akce | Exportovat Azure SQL Database |
> | Akce | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Načte podrobnosti o rozšířených zásadách auditování objektů BLOB konfigurovaných pro danou databázi. |
> | Akce | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Změna zásad auditování rozšířeného objektu BLOB pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/extensions/read | Získá kolekci rozšíření pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/extensions/write | Změna rozšíření pro danou databázi |
> | Akce | Microsoft. SQL/servery/databáze/převzetí služeb při selhání/akce | Převzetí služeb při selhání databáze iniciované zákazníkem. |
> | Akce | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Načtení zásad geografického zálohování pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Vytvoření nebo aktualizace zásad pro biozálohování databáze |
> | Akce | Microsoft.Sql/servers/databases/importExportOperationResults/read | Načte probíhající operace importu a exportu. |
> | Akce | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Načte seznam dostupných oken údržby pro vybranou databázi. |
> | Akce | Microsoft.Sql/servers/databases/maintenanceWindows/read | Získá nastavení systému Windows údržby pro vybranou databázi. |
> | Akce | Microsoft.Sql/servers/databases/maintenanceWindows/write | Nastaví nastavení systému Windows pro správu pro vybranou databázi. |
> | Akce | Microsoft. SQL/servery/databáze/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Akce | Microsoft. SQL/servery/databáze/metriky/čtení | Návratové metriky pro databáze |
> | Akce | Microsoft. SQL/servery/databáze/přesunout/akce | Změňte název existující databáze. |
> | Akce | Microsoft.Sql/servers/databases/operationResults/read | Získá stav databázové operace. |
> | Akce | Microsoft.Sql/servers/databases/operations/cancel/action | Zruší Azure SQL Database čeká na asynchronní operaci, která ještě není dokončená. |
> | Akce | Microsoft.Sql/servers/databases/operations/read | Vrátí seznam operací provedených v databázi. |
> | Akce | Microsoft. SQL/servery/databáze/pozastavit/akce | Pozastavení databáze Azure SQL DataWarehouse |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. SQL/servery/databáze/poskytovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro databáze. |
> | Akce | Microsoft. SQL/servery/databáze/poskytovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Akce | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Vrátí kolekci textů dotazů, které odpovídají zadaným parametrům. |
> | Akce | Microsoft.Sql/servers/databases/queryStore/read | Vrátí aktuální hodnoty nastavení úložiště dotazů pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje nastavení úložiště dotazů pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti zadané databáze. |
> | Akce | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Akce | Microsoft. SQL/servery/databáze/recommendedSensitivityLabels/Write | Doporučené popisky citlivosti aktualizace Batch |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/delete | Nuceně ukončit vztah replikace a s potenciální ztrátou dat |
> | Akce | Microsoft. SQL/servery/databáze/replicationLinks/převzetí služeb při selhání/akce | Převzetí služeb při selhání po synchronizaci všech změn z primární databáze, aby se tato databáze relationship\u0027s jako primární a prováděla se vzdálená primární databáze sekundární |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Převzetí služeb při selhání hned s možnou ztrátou dat, provedení této databáze na primárním relationship\u0027s replikace a provedení vzdálené primární databáze do sekundárního počítače |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/read | Vrátí seznam replikačních propojení nebo získá vlastnosti pro zadané replikační odkazy. |
> | Akce | Microsoft. SQL/servery/databáze/replicationLinks/zrušení propojení/akce | Vynucené ukončení vztahu replikace nebo po synchronizaci s partnerem |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizovat režim replikace pro odkaz na synchronní nebo asynchronní režim |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/action | Vytvoří nový bod obnovení. |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/delete | Odstraní bod obnovení pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/read | Vrátí body obnovení pro databázi. |
> | Akce | Microsoft. SQL/servery/databáze/pokračovat/akce | Obnoví databázi Azure SQL DataWarehouse. |
> | Akce | Microsoft.Sql/servers/databases/schemas/read | Získá schéma databáze (pouze schéma). |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Získá sloupec databáze (pouze schéma). |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Odstraní popisek citlivosti daného sloupce. |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Zakázat doporučení citlivosti na daném sloupci |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Povolit doporučení citlivosti na daném sloupci |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Získá popisek citlivosti daného sloupce. |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Vytvoří nebo aktualizuje popisek citlivosti daného sloupce. |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/read | Získat databázovou tabulku (pouze schéma). |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Načíst seznam doporučení indexu pro databázi |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Použít doporučení indexu |
> | Akce | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Načte seznam zásad detekce hrozeb databáze nakonfigurovaných pro daný server. |
> | Akce | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Změna zásad detekce hrozeb databáze pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/securityMetrics/read | Získá kolekci metrik zabezpečení databáze. |
> | Akce | Microsoft.Sql/servers/databases/sensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Akce | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Vrátí návrhy o škálování databáze nahoru nebo dolů na základě statistik provádění dotazů za účelem zvýšení výkonu nebo snížení nákladů. |
> | Akce | Microsoft. SQL/servery/databáze/SKU/číst | Získá kolekci SKU, které jsou k dispozici pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Zrušit synchronizaci skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/delete | Odstraní existující skupinu synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Vrátí seznam schémat databáze centra synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/logs/read | Vrátí seznam protokolů skupin synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/read | Vrátí seznam skupin synchronizace nebo získá vlastnosti zadané skupiny synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Aktualizovat schéma databáze centra synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu centra synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Odstraní stávajícího člena synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Vrátí seznam členů synchronizace nebo získá vlastnosti zadaného člena synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Aktualizovat schéma člena synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu člena synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Vrátí seznam schémat členů databáze synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Vytvoří člena synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadaného člena synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Aktivovat synchronizaci skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/write | Vytvoří skupinu synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadané skupiny synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/topQueries/queryText/action | Vrátí text Transact-SQL pro vybrané ID dotazu. |
> | Akce | Microsoft.Sql/servers/databases/topQueries/read | Vrátí agregovanou statistiku modulu runtime pro vybraný dotaz ve vybraném časovém období. |
> | Akce | Microsoft.Sql/servers/databases/topQueries/statistics/read | Vrátí agregovanou statistiku modulu runtime pro vybraný dotaz ve vybraném časovém období. |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Načte probíhající operace na transparentním šifrování dat. |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Načte stav a Podrobnosti funkce zabezpečení transparentního šifrování dat pro danou databázi. |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Změna transparentního stavu šifrování dat |
> | Akce | Microsoft. SQL/servery/databáze/upgradeDataWarehouse/Action | Upgrade databáze Azure SQL DataWarehouse |
> | Akce | Microsoft.Sql/servers/databases/usages/read | Získá informace o využití Azure SQL Database. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Načtení zásad posouzení ohrožení zabezpečení na givendatabase |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Odebrání směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Získat směrný plán pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Změna směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/prověřování/export/akce | Převede existující výsledek kontroly na formát pro lidské čtení. Pokud už existuje, nic se nestane. |
> | Akce | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/prověřování/initiateScan/Action | Provést kontrolu databáze posouzení ohrožení zabezpečení. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Vrátí seznam vyhodnocování ohrožení zabezpečení databáze nebo získá záznam o kontrole pro zadané ID vyhledávání. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Změna posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentScans/Action | Provést kontrolu databáze posouzení ohrožení zabezpečení. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Načíst výsledek operace provedení kontroly vyhodnocení ohrožení zabezpečení databáze |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Načte podrobnosti posouzení ohrožení zabezpečení nakonfigurovaného pro danou databázi. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Změna posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft. SQL/servery/databáze/workloadGroups/DELETE | Uvolní konkrétní skupinu úloh. |
> | Akce | Microsoft. SQL/servery/databáze/workloadGroups/Read | Vypíše skupiny úloh pro vybranou databázi. |
> | Akce | Microsoft. SQL/servery/databáze/workloadGroups/workloadClassifiers/DELETE | Zruší konkrétní třídění úloh. |
> | Akce | Microsoft. SQL/servery/databáze/workloadGroups/workloadClassifiers/Read | Vypisuje klasifikátory úloh pro vybranou databázi. |
> | Akce | Microsoft. SQL/servery/databáze/workloadGroups/workloadClassifiers/Write | Nastaví vlastnosti pro konkrétní třídění úloh. |
> | Akce | Microsoft. SQL/servery/databáze/workloadGroups/Write | Nastaví vlastnosti pro určitou skupinu úloh. |
> | Akce | Microsoft.Sql/servers/databases/write | Vytvoří databázi se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou databázi. |
> | Akce | Microsoft.Sql/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Odstraní existující konfigurace zotavení po havárii pro daný server. |
> | Akce | Microsoft. SQL/servery/disasterRecoveryConfiguration/převzetí služeb při selhání/akce | Převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Vynucení převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Získá kolekci konfigurací zotavení po havárii, které zahrnují tento server. |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Změna konfigurace zotavení po havárii serveru |
> | Akce | Microsoft.Sql/servers/elasticPoolEstimates/read | Vrátí seznam odhadů elastických fondů již vytvořených pro tento server. |
> | Akce | Microsoft.Sql/servers/elasticPoolEstimates/write | Vytvoří nový odhad elastického fondu pro seznam poskytnutých databází. |
> | Akce | Microsoft. SQL/servery/elasticPools/Advisors/Read | Vrátí seznam poradců dostupných pro elastický fond. |
> | Akce | Microsoft. SQL/servery/elasticPools/Advisors/recommendedActions/Read | Vrátí seznam doporučených akcí zadaného Poradce pro elastický fond. |
> | Akce | Microsoft. SQL/servery/elasticPools/Advisory/recommendedActions/Write | Použít doporučenou akci pro elastický fond |
> | Akce | Microsoft. SQL/servery/elasticPools/Advisors/Write | Aktualizuje stav automatického spuštění služby Advisor na úrovni elastického fondu. |
> | Akce | Microsoft. SQL/servery/elasticPools/databáze/čtení | Načte seznam databází pro elastický fond. |
> | Akce | Microsoft. SQL/servery/elasticPools/DELETE | Odstranit existující elastický fond |
> | Akce | Microsoft. SQL/servery/elasticPools/elasticPoolActivity/Read | Načte aktivity a podrobnosti v daném fondu elastické databáze. |
> | Akce | Microsoft. SQL/servery/elasticPools/elasticPoolDatabaseActivity/Read | Načte aktivity a podrobnosti v dané databázi, která je součástí fondu elastické databáze. |
> | Akce | Microsoft. SQL/servery/elasticPools/převzetí služeb při selhání/akce | Převzetí služeb při selhání elastického fondu iniciované zákazníkem |
> | Akce | Microsoft. SQL/servery/elasticPools/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro fondy elastické databáze |
> | Akce | Microsoft. SQL/servery/elasticPools/metriky/číst | Návratové metriky pro fondy elastické databáze |
> | Akce | Microsoft. SQL/servery/elasticPools/Operations/Cancel/Action | Zruší nedokončenou asynchronní operaci pro elastický fond Azure SQL, který ještě není hotový. |
> | Akce | Microsoft. SQL/servery/elasticPools/Operations/Read | Vrátí seznam operací provedených v elastickém fondu. |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Akce | Microsoft. SQL/servery/elasticPools/Providers/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro fondy elastické databáze |
> | Akce | Microsoft. SQL/servery/elasticPools/Read | Načte podrobnosti elastického fondu na daném serveru. |
> | Akce | Microsoft. SQL/servery/elasticPools/SKU/Read | Získá kolekci SKU, která je k dispozici pro elastický fond. |
> | Akce | Microsoft. SQL/servery/elasticPools/Write | Vytvoří nové nebo změnit vlastnosti existujícího elastického fondu. |
> | Akce | Microsoft.Sql/servers/encryptionProtector/read | Vrátí seznam chráněných šifrovacích serverů nebo získá vlastnosti pro zadané ochrany šifrování serveru. |
> | Akce | Microsoft. SQL/servery/encryptionProtector/revalidate/Action | Aktualizuje vlastnosti pro zadanou ochranu pomocí šifrování serveru. |
> | Akce | Microsoft. SQL/servery/encryptionProtector/Write | Aktualizuje vlastnosti pro zadanou ochranu pomocí šifrování serveru. |
> | Akce | Microsoft.Sql/servers/extendedAuditingSettings/read | Načíst podrobnosti o zásadách auditování rozšířených objektů BLOB serveru nakonfigurovaných na daném serveru |
> | Akce | Microsoft.Sql/servers/extendedAuditingSettings/write | Změna auditování rozšířeného objektu BLOB serveru pro daný server |
> | Akce | Microsoft.Sql/servers/failoverGroups/delete | Odstraní existující skupinu převzetí služeb při selhání. |
> | Akce | Microsoft. SQL/servery/failoverGroups/převzetí služeb při selhání/akce | Provede plánované převzetí služeb při selhání v existující skupině převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Spustí vynucené převzetí služeb při selhání v existující skupině převzetí služeb při selhání |
> | Akce | Microsoft.Sql/servers/failoverGroups/read | Vrátí seznam skupin převzetí služeb při selhání nebo získá vlastnosti zadané skupiny převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/write | Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou skupinu převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/firewallRules/delete | Odstraní existující pravidlo brány firewall serveru. |
> | Akce | Microsoft. SQL/servery/firewallRules/Read | Vrátí seznam pravidel brány firewall serveru nebo získá vlastnosti zadaného pravidla brány firewall serveru. |
> | Akce | Microsoft. SQL/servery/firewallRules/Write | Vytvoří pravidlo brány firewall serveru se zadanými parametry, aktualizuje vlastnosti zadaného pravidla nebo přepíše všechna stávající pravidla novými pravidly brány firewall serveru. |
> | Akce | Microsoft. SQL/servery/import/akce | Vytvoření nové databáze na serveru a nasazení schématu a dat z balíčku DacPac |
> | Akce | Microsoft.Sql/servers/importExportOperationResults/read | Načte probíhající operace importu a exportu. |
> | Akce | Microsoft. SQL/servery/inaccessibleDatabases/Read | Vrátí seznam nepřístupných databází na logickém serveru. |
> | Akce | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Odstraní zadaný profil koncového bodu rozhraní. |
> | Akce | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Vrátí vlastnosti pro zadaný profil koncového bodu rozhraní. |
> | Akce | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Vytvoří profil koncového bodu rozhraní se zadanými parametry nebo aktualizuje vlastnosti nebo značky zadaného koncového bodu rozhraní. |
> | Akce | Microsoft. SQL/servery/jobAgents/DELETE | Odstraní agenta úloh služby Azure SQL DB. |
> | Akce | Microsoft. SQL/servery/jobAgents/Read | Načte agenta úloh služby Azure SQL DB. |
> | Akce | Microsoft. SQL/servery/jobAgents/Write | Vytvoří nebo aktualizuje agenta úloh služby Azure SQL DB. |
> | Akce | Microsoft.Sql/servers/keys/delete | Odstraní existující klíč serveru. |
> | Akce | Microsoft. SQL/servery/klíče/číst | Vrátí seznam klíčů serveru nebo získá vlastnosti pro zadaný klíč serveru. |
> | Akce | Microsoft. SQL/servery/klíče/zapisovat | Vytvoří klíč se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný klíč serveru. |
> | Akce | Microsoft.Sql/servers/operationResults/read | Načte probíhající operace serveru. |
> | Akce | Microsoft. SQL/servery/operace/číst | Vrátí seznam operací provedených na serveru. |
> | Akce | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Odstraní existující proxy připojení privátního koncového bodu. |
> | Akce | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Vrátí seznam proxy serverů připojení privátního koncového bodu nebo získá vlastnosti zadaného proxy serveru připojení privátního koncového bodu. |
> | Akce | Microsoft. SQL/servery/privateEndpointConnectionProxies/ověřit/akce | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Akce | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Vytvoří proxy server připojení privátního koncového bodu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný proxy privátního koncového bodu. |
> | Akce | Microsoft. SQL/servery/privateEndpointConnections/DELETE | Odstraní existující připojení privátního koncového bodu. |
> | Akce | Microsoft. SQL/servery/privateEndpointConnections/Read | Vrátí seznam připojení privátního koncového bodu nebo získá vlastnosti pro zadané připojení privátního koncového bodu. |
> | Akce | Microsoft. SQL/servery/privateEndpointConnections/Write | Schválí nebo odmítne existující připojení privátního koncového bodu. |
> | Akce | Microsoft. SQL/servery/privateEndpointConnectionsApproval/Action | Určuje, jestli má uživatel povolený souhlas s připojením privátního koncového bodu. |
> | Akce | Microsoft.Sql/servers/privateLinkResources/read | Získat prostředky privátního propojení pro odpovídající SQL Server |
> | Akce | Microsoft. SQL/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro servery |
> | Akce | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Načtení metrik pro doporučené fondy elastické databáze pro daný server |
> | Akce | Microsoft.Sql/servers/recommendedElasticPools/read | Načtěte doporučení pro fondy elastické databáze, abyste snížili náklady nebo vylepšili výkon na základě historických využití prostředků. |
> | Akce | Microsoft.Sql/servers/recoverableDatabases/read | Tato operace se používá pro zotavení po havárii živé databáze k obnovení databáze do posledního známého funkčního bodu zálohování. Vrátí informace o poslední dobré záloze, ale doesn\u0027t ve skutečnosti obnoví databázi. |
> | Akce | Microsoft. SQL/servery/replicationLinks/Read | Vrátí seznam replikačních propojení nebo získá vlastnosti pro zadané replikační odkazy. |
> | Akce | Microsoft.Sql/servers/restorableDroppedDatabases/read | Získat seznam databází, které byly na daném serveru vyřazeny, které jsou stále v rámci zásad uchovávání informací. |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Načíst výsledky operace zápisu zásad detekce hrozeb serveru |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/read | Načte seznam zásad detekce hrozeb serveru nakonfigurovaných pro daný server. |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Akce | Microsoft. SQL/servery/serviceObjectives/Read | Načte seznam cílů na úrovni služby (označovaných také jako úrovně výkonu), které jsou k dispozici na daném serveru. |
> | Akce | Microsoft.Sql/servers/syncAgents/delete | Odstraní stávajícího agenta synchronizace. |
> | Akce | Microsoft. SQL/servery/syncAgents/generateKey/Action | Generovat registrační klíč agenta synchronizace |
> | Akce | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Vrátí seznam propojených databází agenta synchronizace. |
> | Akce | Microsoft. SQL/servery/syncAgents/Read | Vrátí seznam agentů synchronizace nebo získá vlastnosti zadaného agenta synchronizace. |
> | Akce | Microsoft. SQL/servery/syncAgents/Write | Vytvoří agenta synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadaného agenta synchronizace. |
> | Akce | Microsoft.Sql/servers/tdeCertificates/action | Vytvořit nebo aktualizovat certifikát TDE |
> | Akce | Microsoft. SQL/servery/použití/číst | Vrátí kvótu DTU serveru a aktuální spotřebu DTU všemi databázemi v rámci serveru. |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/delete | Odstraní existující pravidlo Virtual Network. |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Akce | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro daný server |
> | Akce | Microsoft. SQL/servery/vulnerabilityAssessments/Read | Načtěte zásady posouzení ohrožení zabezpečení na daném serveru. |
> | Akce | Microsoft. SQL/servery/vulnerabilityAssessments/Write | Změna posouzení ohrožení zabezpečení pro daný server |
> | Akce | Microsoft. SQL/servery/Write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |
> | Akce | Microsoft.Sql/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků Microsoft SQL Database a povolí vytváření databází Microsoft SQL. |
> | Akce | Microsoft.Sql/virtualClusters/delete | Odstraní existující virtuální cluster. |
> | Akce | Microsoft. SQL/virtualClusters/Read | Vrátí seznam virtuálních clusterů nebo získá vlastnosti pro zadaný virtuální cluster. |
> | Akce | Microsoft. SQL/virtualClusters/Write | Aktualizuje značky virtuálního clusteru. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. SqlVirtualMachine/Locations/availabilityGroupListenerOperationResults/Read | Získání výsledku operace naslouchacího procesu skupiny dostupnosti |
> | Akce | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineGroupOperationResults/Read | Získání výsledku operace skupiny virtuálních počítačů SQL |
> | Akce | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineOperationResults/Read | Získat výsledek operace virtuálního počítače SQL |
> | Akce | Microsoft. SqlVirtualMachine/Operations/Read |  |
> | Akce | Microsoft. SqlVirtualMachine/Register/Action | Registrovat předplatné u poskytovatele prostředků Microsoft. SqlVirtualMachine |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/DELETE | Odstranit existující naslouchací proces skupiny dostupnosti |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Read | Načíst podrobnosti naslouchacího procesu skupiny dostupnosti SQL v dané skupině virtuálních počítačů SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Write | Vytvoří nové nebo změní vlastnosti stávajícího naslouchacího procesu skupiny dostupnosti SQL. |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/DELETE | Odstranit existující skupinu virtuálních počítačů SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Read | Podrobnosti o načtení skupiny virtuálních počítačů SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/Read | Vypsat virtuální počítače SQL pomocí konkrétní skupiny virtuálních počítačů SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Write | Vytvořit nové nebo změnit vlastnosti existující skupiny virtuálních počítačů SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachines/DELETE | Odstranit stávající virtuální počítač s SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Read | Načíst podrobnosti o virtuálním počítači SQL |
> | Akce | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Write | Vytvořit nové nebo změnit vlastnosti stávajícího virtuálního počítače SQL |
> | Akce | Microsoft. SqlVirtualMachine/zrušit registraci/akce | Zrušení registrace předplatného u poskytovatele prostředků Microsoft. SqlVirtualMachine |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Storage/checknameavailability/Read | Kontroluje, že název účtu je platný a nepoužívá se. |
> | Akce | Microsoft. Storage/Locations/checknameavailability/Read | Kontroluje, že název účtu je platný a nepoužívá se. |
> | Akce | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoft. Storage, že se odstraňuje virtuální síť nebo podsíť. |
> | Akce | Microsoft. Storage/umístění/využití/čtení | Vrátí limit a aktuální počet použití pro prostředky v zadaném předplatném. |
> | Akce | Microsoft. Storage/Operations/Read | Cyklické dotazování na stav asynchronní operace. |
> | Akce | Microsoft. Storage/registr/Action | Zaregistruje předplatné pro poskytovatele prostředků úložiště a povolí vytváření účtů úložiště. |
> | Akce | Microsoft. Storage/SKU/čtení | Zobrazuje seznam SKU podporovaný Microsoft. Storage. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Add/Action | Vrátí výsledek přidání obsahu objektu BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/DELETE | Vrátí výsledek odstranění objektu BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/deleteBlobVersion/Action | Vrátí výsledek odstranění verze objektu BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Filter/Action | Vrátí seznam objektů BLOB v rámci účtu se shodným filtrem značek. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/runAsSuperUser/Action | Vrátí výsledek příkazu objektu BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Read | Vrátí výsledek čtení značek objektů BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Write | Vrátí výsledek zápisu značek objektů BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Write | Vrátí výsledek zápisu objektu BLOB. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Vymazat právní blokování kontejneru objektů BLOB |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Vrátí výsledek odstranění kontejneru. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Odstranit zásady neměnnosti kontejneru objektů BLOB |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Extended/Action | Rozšíří zásady neměnnosti kontejneru objektů BLOB. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Lock/Action | Zamknout zásady neměnnosti kontejneru objektů BLOB |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Read | Získat zásady neměnnosti kontejneru objektů BLOB |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Vložit zásady neměnnosti kontejneru objektů BLOB |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/Lease/Action | Vrátí výsledek zapůjčení kontejneru objektů BLOB. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí kontejner. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí seznam kontejnerů. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/setLegalHold/Action | Nastavit právní blokování kontejneru objektů BLOB |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/Write | Vrátí výsledek opravy kontejneru objektů BLOB. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Containers/Write | Vrátí výsledek Put kontejneru objektů BLOB. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro službu BLOB Service. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Read |  |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Read | Vrátí vlastnosti nebo statistiky služby BLOB Service. |
> | Akce | Microsoft. Storage/storageAccounts/blobServices/Write | Vrátí výsledek vložení vlastností služby BLOB Service. |
> | Akce | Microsoft.Storage/storageAccounts/delete | Odstraní existující účet úložiště. |
> | Akce | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Akce | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Akce | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Akce | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Akce | Microsoft. Storage/storageAccounts/převzetí služeb při selhání/akce | Zákazník dokáže řídit převzetí služeb při selhání v případě problémů s dostupností. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/actassuperuser/Action | Získat oprávnění správce souborů |
> | Akce dataaction | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/odstranění | Vrátí výsledek odstranění souboru nebo složky. |
> | Akce dataaction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Vrátí výsledek úpravy oprávnění pro soubor nebo složku. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/čtení | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | Akce dataaction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/read | Získat vlastnosti souborové služby |
> | Akce | Microsoft. Storage/storageAccounts/služby/služby/sdílené složky/odstranění |  |
> | Akce | Microsoft. Storage/storageAccounts/služby/služby/sdílené/číst |  |
> | Akce | Microsoft. Storage/storageAccounts/služby/služby/sdílené/číst |  |
> | Akce | Microsoft. Storage/storageAccounts/služby/služby/sdílení/zápis |  |
> | Akce | Microsoft. Storage/storageAccounts/služby/služby/zápis |  |
> | Akce | Microsoft. Storage/storageAccounts/listAccountSas/Action | Vrátí token SAS účtu pro zadaný účet úložiště. |
> | Akce | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Akce | Microsoft. Storage/storageAccounts/listServiceSas/Action | Vrátí token SAS služby pro zadaný účet úložiště. |
> | Akce | Microsoft. Storage/storageAccounts/managementPolicies/DELETE | Odstranit zásady správy účtu úložiště |
> | Akce | Microsoft. Storage/storageAccounts/managementPolicies/Read | Získání zásad účtu správy úložiště |
> | Akce | Microsoft. Storage/storageAccounts/managementPolicies/Write | Vložit zásady správy účtu úložiště |
> | Akce | Microsoft. Storage/storageAccounts/objectReplicationPolicies/DELETE |  |
> | Akce | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Read |  |
> | Akce | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Akce | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Odstranit proxy připojení privátního koncového bodu |
> | Akce | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Read | Získat proxy připojení privátního koncového bodu |
> | Akce | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Vložit proxy privátního koncového bodu připojení |
> | Akce | Microsoft. Storage/storageAccounts/privateEndpointConnections/DELETE | Odstranit připojení privátního koncového bodu |
> | Akce | Microsoft. Storage/storageAccounts/privateEndpointConnections/Read | Získat připojení privátního koncového bodu |
> | Akce | Microsoft. Storage/storageAccounts/privateEndpointConnections/Write | Vložit připojení privátního koncového bodu |
> | Akce | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Approve Private Endpoint Connections |
> | Akce | Microsoft.Storage/storageAccounts/privateLinkResources/read | Získat StorageAccount groupids |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Vrátí výsledek odstranění fronty. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Add/Action | Vrátí výsledek přidávání zprávy. |
> | Akce dataaction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Vrátí výsledek odstranění zprávy. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Process/Action | Vrátí výsledek zpracování zprávy. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Read | Vrátí zprávu. |
> | Akce dataaction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Vrátí výsledek zápisu zprávy. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/write | Vrátí výsledek zápisu fronty. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/read | Získat vlastnosti Služba front |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/read | Vrátí vlastnosti nebo statistiky služby Queue. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/write | Vrátí výsledek nastavení vlastností služby Queue. |
> | Akce | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Akce | Microsoft. Storage/storageAccounts/RegenerateKey/Action | Znovu vygeneruje přístupové klíče pro zadaný účet úložiště. |
> | Akce | Microsoft. Storage/storageAccounts/restoreBlobRanges/Action | Obnovte rozsahy objektů blob do stavu určeného časem. |
> | Akce | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odvolá všechny klíče delegování uživatelů pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky účtu úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/tableServices/read | Získat vlastnosti Table service |
> | Akce | Microsoft.Storage/storageAccounts/write | Vytvoří účet úložiště se zadanými parametry, aktualizuje vlastnosti nebo značky nebo přidá vlastní doménu pro zadaný účet úložiště. |
> | Akce | Microsoft. Storage/použití/čtení | Vrátí limit a aktuální počet použití pro prostředky v zadaném předplatném. |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. storagesync/Locations/checkNameAvailability/Action | Kontroluje, jestli je název služby synchronizace úložiště platný a nepoužívá se. |
> | Akce | microsoft.storagesync/locations/workflows/operations/read | Získá stav asynchronní operace. |
> | Akce | Microsoft. storagesync/Operations/Read | Načte seznam podporovaných operací. |
> | Akce | Microsoft. storagesync/Register/Action | Zaregistruje předplatné pro zprostředkovatele synchronizace úložiště. |
> | Akce | microsoft.storagesync/storageSyncServices/delete | Odstranit všechny služby synchronizace úložiště |
> | Akce | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro služby synchronizace úložiště. |
> | Akce | microsoft.storagesync/storageSyncServices/read | Přečtěte si všechny služby synchronizace úložiště |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/delete | Odstranit jakýkoli registrovaný Server |
> | Akce | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro registrovaný Server. |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/read | Číst libovolný registrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/write | Vytvořit nebo aktualizovat jakýkoli registrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Odstranit všechny koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Získá stav asynchronní operace zálohování a obnovení. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Zavolat tuto akci po zálohování |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Zavolat tuto akci po obnovení |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Před zálohováním volejte tuto akci |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Před obnovením volejte tuto akci |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Čtení všech koncových bodů cloudu |
> | Akce | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Obnovit prezenční signál |
> | Akce | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/Action | Tuto akci zavolejte, pokud chcete aktivovat detekci změn ve sdílené složce koncového bodu cloudu. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Vytvoří nebo aktualizuje všechny koncové body cloudu. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/delete | Odstranění všech skupin synchronizace |
> | Akce | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro skupiny synchronizace. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/read | Čtení všech skupin synchronizace |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Odstranění libovolných koncových bodů serveru |
> | Akce | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro koncové body serveru. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Čtení libovolných koncových bodů serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Zavolejte tuto akci, pokud chcete soubory znovu navrátit na server. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Vytvořit nebo aktualizovat libovolné koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/write | Vytvoření nebo aktualizace skupin synchronizace |
> | Akce | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Získá stav asynchronní operace. |
> | Akce | microsoft.storagesync/storageSyncServices/workflows/operations/read | Získá stav asynchronní operace. |
> | Akce | microsoft.storagesync/storageSyncServices/workflows/read | Čtení pracovních postupů |
> | Akce | microsoft.storagesync/storageSyncServices/write | Vytvořit nebo aktualizovat všechny služby synchronizace úložiště |
> | Akce | Microsoft. storagesync/zrušit registraci/akce | Zruší registraci předplatného pro zprostředkovatele synchronizace úložiště. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. StorSimple/manažeři/accessControlRecords/DELETE | Odstraní záznamy Access Control. |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/accessControlRecords/Read | Zobrazí nebo získá záznamy Access Control. |
> | Akce | Microsoft. StorSimple/manažeři/accessControlRecords/Write | Vytvoření nebo aktualizace záznamů Access Control |
> | Akce | Microsoft. StorSimple/manažeři/výstrahy/číst | Zobrazí nebo získá výstrahy. |
> | Akce | Microsoft. StorSimple/manažeři/zálohy/číst | Zobrazí nebo získá zálohovací sklad. |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/delete | Odstraní existující nastavení šířky pásma (jenom řady 8000). |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Vypíše výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/read | Zobrazit seznam nastavení šířky pásma (jenom řada 8000) |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/write | Vytvoří nové nebo aktualizuje nastavení šířky pásma (jenom série 8000). |
> | Akce | Microsoft. StorSimple/manažeři/certifikáty/zapisovat | Vytvoření nebo aktualizace certifikátů |
> | Akce | Microsoft.StorSimple/Managers/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | Akce | Microsoft. StorSimple/manažeři/clearAlerts/Action | Vymažte všechny výstrahy spojené se správcem zařízení. |
> | Akce | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Seznam podporovaných konfigurací cloudového zařízení |
> | Akce | Microsoft. StorSimple/manažeři/configureDevice/Action | Nakonfiguruje zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/odstranit | Odstraní Správce zařízení. |
> | Akce | Microsoft.StorSimple/Managers/delete | Operace odstranění trezoru odstraní zadaný prostředek Azure typu trezor. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/alertSettings/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/alertSettings/číst | Zobrazí nebo získá nastavení upozornění. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/alertSettings/Write | Vytvořit nebo aktualizovat nastavení výstrah |
> | Akce | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizovat pro výměnu šifrovacího klíče služby na zařízeních |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/záloha/akce | Pokud chcete vytvořit zálohu na vyžádání všech svazků chráněných zásadou, proveďte ruční zálohování. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/DELETE | Odstraní existující zásady zálohování (jenom řada 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/operationResults/číst | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/číst | Seznam zásad zálohování (jenom řada 8000) |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/Schedules/DELETE | Odstraní existující plány. |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/plány/čtení | Výpis plánů |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Vytvoří nové nebo aktualizované plány. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/Write | Vytvoří nové nebo aktualizuje zásady zálohování (jenom řada 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zálohy/odstranit | Odstraní zálohovací sklad. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zálohy/prvky/klonování/akce | Naklonujte sdílenou složku nebo svazek pomocí elementu zálohování. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zálohy/elementy/operationResults/Read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zálohy/operationResults/Read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zálohy/číst | Zobrazí nebo získá zálohovací sklad. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zálohy/obnovení/akce | Obnovte všechny svazky ze zálohovacího skladu. |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Odstraní skupiny plánu zálohování. |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Zobrazí nebo získá skupiny plánu zálohování. |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Vytvoření nebo aktualizace skupin plánu zálohování |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/delete | Odstraní nastavení protokolu CHAP. |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/read | Zobrazí nebo získá nastavení protokolu CHAP. |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/write | Vytvořit nebo aktualizovat nastavení protokolu CHAP |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/deaktivovat/akce | Deaktivuje zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/odstranit | Odstraní zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/disky/číst | Vypíše nebo získá disky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/stáhnout/akce | Stažení aktualizací pro zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/převzetí služeb při selhání/akce | Převzetí služeb při selhání zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/převzetí služeb při selhání/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/failoverTargets/číst | Vypíše nebo získá cíle převzetí služeb při selhání zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/zálohy/akce | Vybere zálohu souborového serveru. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/odstranit | Odstraní souborové servery. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/a metriky/číst | Vypíše nebo získá metriky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/servery/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/servery/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/servery/číst | Zobrazí nebo získá souborové servery. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené složky/odstranit | Odstraní sdílené složky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/akcie/metriky/čtení | Vypíše nebo získá metriky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/číst | Zobrazí nebo získá sdílené složky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/zapisovat | Vytvořit nebo aktualizovat sdílené složky |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/servery/zápisy/zapisovat | Vytvořit nebo aktualizovat souborové servery |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Změna stavu napájení řadiče pro skupiny hardwarových součástí |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Vypíše výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Seznam skupin hardwarových součástí |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/instalace/akce | Nainstalujte do zařízení aktualizace. |
> | Akce | Microsoft.StorSimple/managers/devices/installUpdates/action | Nainstaluje aktualizace na zařízení (jenom řada 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/záloha/akce | Pořídit zálohu serveru iSCSI. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Odstraní servery iSCSI. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Odstraní disky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/disky/metriky/čtení | Vypíše nebo získá metriky. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Zobrazí nebo získá definice metrik. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/disky/číst | Vypíše nebo získá disky. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Vytvořit nebo aktualizovat disky |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/metriky/číst | Vypíše nebo získá metriky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/číst | Vypíše nebo získá servery iSCSI. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/write | Vytvoření nebo aktualizace serverů iSCSI |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/úlohy/zrušit/akce | Zrušení spuštěné úlohy |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/úlohy/operationResults/číst | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/úlohy/číst | Zobrazí nebo získá úlohy. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/listFailoverSets/Action | Vypíše sady převzetí služeb při selhání pro existující zařízení (jenom řada 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/listFailoverTargets/Action | Vypíše cíle převzetí služeb při selhání zařízení (jenom řada 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/metriky/číst | Vypíše nebo získá metriky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/confirmMigration/Action | Potvrdí úspěšnou migraci a potvrdí ji. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/confirmMigrationStatus/číst | Seznam potvrzení stavu migrace |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/fetchConfirmMigrationStatus/Action | Načtěte stav potvrzení migrace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/fetchMigrationEstimate/Action | Načtěte stav úlohy odhadu migrace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/fetchMigrationStatus/Action | Načtěte stav migrace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/import/akce | Importovat konfigurace zdrojů pro migraci |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/migrationEstimate/číst | Výpis odhadu migrace |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/migrationStatus/číst | Výpis stavu migrace |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/operationResults/číst | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/startMigration/Action | Spustit migraci pomocí konfigurací zdrojů |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/startMigrationEstimate/Action | Spusťte úlohu pro odhad doby trvání procesu migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Vypíše výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/read | Zobrazí nebo získá nastavení sítě. |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/write | Vytvoří nové nebo aktualizuje nastavení sítě. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/publicEncryptionKey/Action | Vypsat veřejný šifrovací klíč Správce zařízení |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/publishSupportPackage/Action | Publikování balíčku pro podporu pro existující zařízení. Balíček podpory StorSimple je snadno použitelný mechanismus, který shromažďuje všechny relevantní protokoly, které vám pomůžou podpora Microsoftu s řešením potíží s problémy s StorSimple zařízeními. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/číst | Zobrazí nebo získá zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Vyhledat aktualizace v zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/securitySettings/číst | Zobrazit seznam nastavení zabezpečení |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizuje certifikát vzdálené správy pro zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aktualizujte nastavení zabezpečení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/write | Vytvoří nové nebo aktualizuje nastavení zabezpečení. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/sendTestAlertEmail/Action | Odešlete e-mailem upozornění testu nakonfigurovaným příjemcům e-mailu |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/sdílet/číst | Zobrazí nebo získá sdílené složky. |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/timeSettings/číst | Zobrazí nebo získá nastavení času. |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/write | Vytvoří nové nebo aktualizace nastavení času. |
> | Akce | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/devices/updateSummary/read | Zobrazí nebo získá souhrn aktualizace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/DELETE | Odstraní existující kontejnery svazků (jenom řada 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/metriky/číst | Vypsat metriky |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/metricsDefinitions/číst | Seznam definic metrik |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/operationResults/číst | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/číst | Seznam kontejnerů svazků (jenom řada 8000) |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/odstranit | Odstraní existující svazky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/metriky/číst | Vypsat metriky |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/metricsDefinitions/Read | Seznam definic metrik |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/operationResults/Read | Vypíše výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/číst | Vypsat svazky |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/zapisovat | Vytvoří nové nebo aktualizuje svazky. |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/Write | Vytvoří nové nebo aktualizuje kontejnery svazků (jenom série 8000). |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/svazky/číst | Vypsat svazky |
> | Akce | Microsoft. StorSimple/manažeři/zařízení/zapisovat | Vytvoření nebo aktualizace zařízení |
> | Akce | Microsoft.StorSimple/managers/encryptionSettings/read | Zobrazí nebo získá nastavení šifrování. |
> | Akce | Microsoft.StorSimple/managers/extendedInformation/delete | Odstraní rozšířené informace o trezoru. |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/delete | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Akce | Microsoft.StorSimple/managers/extendedInformation/read | Zobrazí nebo získá informace o rozšířeném trezoru. |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Akce | Microsoft.StorSimple/managers/extendedInformation/write | Vytvoří nebo aktualizuje rozšířené informace o trezoru. |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/write | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Akce | Microsoft. StorSimple/manažeři/funkce/číst | Seznam funkcí |
> | Akce | Microsoft. StorSimple/manažeři/servery/číst | Zobrazí nebo získá souborové servery. |
> | Akce | Microsoft. StorSimple/manažeři/getEncryptionKey/Action | Získejte šifrovací klíč pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/iscsiservers/read | Vypíše nebo získá servery iSCSI. |
> | Akce | Microsoft. StorSimple/manažeři/úlohy/číst | Zobrazí nebo získá úlohy. |
> | Akce | Microsoft. StorSimple/manažeři/listActivationKey/Action | Získá aktivační klíč StorSimple Device Manager. |
> | Akce | Microsoft. StorSimple/manažeři/listPublicEncryptionKey/Action | Vypíše veřejné šifrovací klíče StorSimple Device Manager. |
> | Akce | Microsoft. StorSimple/manažeři/metriky/číst | Vypíše nebo získá metriky. |
> | Akce | Microsoft. StorSimple/manažeři/metricsDefinitions/Read | Zobrazí nebo získá definice metrik. |
> | Akce | Microsoft. StorSimple/manažeři/migrateClassicToResourceManager/Action | Migrace z modelu Classic na Správce prostředků |
> | Akce | Microsoft. StorSimple/manažeři/migrationSourceConfigurations/Read | Seznam konfigurací zdrojů migrace (jenom řada 8000) |
> | Akce | Microsoft.StorSimple/managers/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Vytvořte nové cloudové zařízení. |
> | Akce | Microsoft. StorSimple/manažeři/číst | Zobrazí nebo získá Správce zařízení. |
> | Akce | Microsoft.StorSimple/Managers/read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft. StorSimple/manažeři/regenerateActivationKey/Action | Znovu vygenerujte aktivační klíč pro existující Device Manager StorSimple. |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Odstraní přihlašovací údaje účtu úložiště. |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/read | Zobrazí nebo získá přihlašovací údaje účtu úložiště. |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/write | Vytvořit nebo aktualizovat přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageDomains/delete | Odstraní domény úložiště. |
> | Akce | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Akce | Microsoft. StorSimple/manažeři/storageDomains/Read | Vypíše nebo získá domény úložiště. |
> | Akce | Microsoft. StorSimple/manažeři/storageDomains/Write | Vytvoření nebo aktualizace domén úložiště |
> | Akce | Microsoft. StorSimple/manažeři/zapisovat | Vytvořit nebo aktualizovat Správce zařízení |
> | Akce | Microsoft.StorSimple/Managers/write | Operace vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/operations/read | Zobrazí nebo získá operace. |
> | Akce | Microsoft.StorSimple/register/action | Registrovat poskytovatele Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. StreamAnalytics/umístění/kvóty/číst | Kvóta pro čtení Stream Analytics předplatného |
> | Akce | Microsoft. StreamAnalytics/Operations/Read | Čtení Stream Analyticsch operací |
> | Akce | Microsoft. StreamAnalytics/Register/Action | Registrovat předplatné u Stream Analyticsho poskytovatele prostředků |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/DELETE | Odstranit úlohu Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Functions/DELETE | Odstranit funkci úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Načíst výsledky operace pro funkci Stream Analytics úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Funkce pro čtení Stream Analytics úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Načtení výchozí definice funkce úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Functions/test/Action | Funkce úlohy testovacího Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Funkce úlohy Write Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Inputs/DELETE | Odstranění vstupu úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Inputs/operationresults/Read | Načíst výsledky operace pro vstup Stream Analytics úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Inputs/Read | Číst Stream Analytics vstup úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Inputs/Sample/Action | Ukázka Stream Analyticsho vstupu úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Testovací Stream Analytics – vstup úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Inputs/Write | Zápis Stream Analytics vstup úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Čtení definic metriky |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/operationresults/Read | Načtení výsledků operace Stream Analytics úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/výstupy/odstranit | Odstranit výstup úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/výstupy/operationresults/Read | Načíst výsledky operace pro výstup úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/výstupy/čtení | Načíst výstup úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/výstupy/test/Action | Výstup úlohy testovacího Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/výstupy/Write | Zápis výstupu úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Read | Přečte nastavení diagnostiky. |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Write | Zapsat nastavení diagnostiky. |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro streamingjobs. |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro streamingjobs. |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/PublishEdgePackage/Action | Publikovat hraniční balíček pro úlohu Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Read | Načtení Stream Analytics úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Scale/Action | Škálování Stream Analytics úlohy |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Start/Action | Spuštění úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/stop/Action | Zastavit úlohu Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/transformes/DELETE | Odstranění transformace úlohy Stream Analytics |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/transformes/Read | Transformace úlohy Stream Analytics čtení |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/transformes/Write | Transformace úlohy Stream Analytics zápisu |
> | Akce | Microsoft. StreamAnalytics/streamingjobs/Write | Stream Analytics úloha zápisu |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. Subscription/Cancel/Action | Zruší předplatné. |
> | Akce | Microsoft. Subscription/CreateSubscription/Action | Vytvořit předplatné Azure |
> | Akce | Microsoft. Subscription/registr/Action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. Subscription. |
> | Akce | Microsoft. Subscription/přejmenovat/Action | Přejmenuje předplatné. |
> | Akce | Microsoft. Subscription/SubscriptionDefinitions/Read | Získání definice předplatného Azure v rámci skupiny pro správu |
> | Akce | Microsoft. Subscription/SubscriptionDefinitions/Write | Vytvoření definice předplatného Azure |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Support/register/action | Registruje poskytovatele prostředků podpory. |
> | Akce | Microsoft.Support/supportTickets/read | Umožňuje získat podrobnosti lístku podpory (včetně stavu, závažnosti, podrobností kontaktu a komunikací) nebo seznam lístků podpory pro všechna předplatná. |
> | Akce | Microsoft.Support/supportTickets/write | Umožňuje vytvořit nebo aktualizovat lístek podpory. Můžete vytvořit lístek podpory pro technické problémy, problémy s fakturací nebo kvótami nebo problémy související se správou předplatného. U stávajících lístků podpory můžete aktualizovat závažnost, podrobnosti kontaktu a komunikace. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. TimeSeriesInsights/Environments/accesspolicies/DELETE | Odstraní zásady přístupu. |
> | Akce | Microsoft. TimeSeriesInsights/Environment/accesspolicies/Read | Získá vlastnosti zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Vytvoří novou zásadu přístupu pro prostředí nebo aktualizuje existující zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/delete | Odstraní prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Odstraní zdroj události. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/read | Získá vlastnosti zdroje události. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/write | Vytvoří nový zdroj události pro prostředí nebo aktualizuje existující zdroj události. |
> | Akce | Microsoft.TimeSeriesInsights/environments/read | Získá vlastnosti prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Odstraní referenční datovou sadu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Získá vlastnosti referenční sady dat. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Vytvoří novou referenční sadu dat pro prostředí nebo aktualizuje existující referenční datovou sadu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/status/read | Získejte stav prostředí, stav svých přidružených operací, jako je příchozí přenos dat. |
> | Akce | Microsoft.TimeSeriesInsights/environments/write | Vytvoří nové prostředí nebo aktualizuje existující prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/register/action | Zaregistruje předplatné pro poskytovatele prostředků Time Series Insights a povolí vytváření Time Series Insights prostředí. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.VisualStudio/Account/Delete | Odstranit účet |
> | Akce | Microsoft. VisualStudio/účet/rozšíření/čtení | Čtení účtu nebo rozšíření |
> | Akce | Microsoft.VisualStudio/Account/Project/Read | Čtení účtu nebo projektu |
> | Akce | Microsoft. VisualStudio/Account/Project/Write | Nastavit účet/projekt |
> | Akce | Microsoft.VisualStudio/Account/Read | Číst účet |
> | Akce | Microsoft.VisualStudio/Account/Write | Nastavit účet |
> | Akce | Microsoft. VisualStudio/Extension/DELETE | Odstranit rozšíření |
> | Akce | Microsoft. VisualStudio/rozšíření/čtení | Načíst rozšíření |
> | Akce | Microsoft. VisualStudio/rozšíření/Write | Nastavit rozšíření |
> | Akce | Microsoft.VisualStudio/Project/Delete | Odstranit projekt |
> | Akce | Microsoft.VisualStudio/Project/Read | Přečíst projekt |
> | Akce | Microsoft.VisualStudio/Project/Write | Nastavit projekt |
> | Akce | Microsoft. VisualStudio/Register/Action | Registrace předplatného Azure u poskytovatele Microsoft. VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | microsoft.web/apimanagementaccounts/apiacls/read | Získejte účty API Management Apiacls. |
> | Akce | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Odstraňte rozhraní API pro účty API Management Apiacls. |
> | Akce | microsoft.web/apimanagementaccounts/apis/apiacls/read | Získejte rozhraní API pro účty API Management Apiacls. |
> | Akce | microsoft.web/apimanagementaccounts/apis/apiacls/write | Aktualizujte účty API Management API Apiacls. |
> | Akce | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Získejte rozhraní API pro účty API Management Connectionacls. |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Connections/confirmconsentcode/Action | Potvrďte souhlasná rozhraní API Code Management Accounts Connections. |
> | Akce | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Odstraní účty API managementu rozhraní API připojení Connectionacls. |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Read | Získat účty API managementu připojení rozhraní API Connectionacls. |
> | Akce | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Aktualizujte účty API managementu. připojení rozhraní API Connectionacls. |
> | Akce | microsoft.web/apimanagementaccounts/apis/connections/delete | Odstraňte připojení rozhraní API pro účty API Management. |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Connections/getconsentlinks/Action | Získejte odkazy na souhlas pro připojení rozhraní API pro účty služby API Management. |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Connections/listconnectionkeys/Action | Vypíše seznam klíčů připojení rozhraní API pro účty API pro připojení. |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Connections/listsecrets/Action | Vypíše účty rozhraní API pro správu tajných kódů. |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Connections/Read | Získejte připojení rozhraní API pro účty API Management. |
> | Akce | microsoft.web/apimanagementaccounts/apis/connections/write | Aktualizujte připojení rozhraní API pro účty Management API. |
> | Akce | microsoft.web/apimanagementaccounts/apis/delete | Odstraňte rozhraní API pro účty API managementu. |
> | Akce | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Odstraňte lokalizované definice rozhraní API Management Accounts API. |
> | Akce | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Získejte lokalizované definice rozhraní API pro účty Management Management. |
> | Akce | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Aktualizovat lokalizované definice rozhraní API Management Accounts API |
> | Akce | Microsoft. Web/apimanagementaccounts/API/Read | Získejte rozhraní API pro účty API managementu. |
> | Akce | microsoft.web/apimanagementaccounts/apis/write | Aktualizujte rozhraní API pro účty API Management. |
> | Akce | microsoft.web/apimanagementaccounts/connectionacls/read | Získejte účty API Management Connectionacls. |
> | Akce | Microsoft. Web/availablestacks/Read | Získejte dostupné zásobníky. |
> | Akce | Microsoft.Web/certificates/Delete | Odstraní existující certifikát. |
> | Akce | Microsoft. Web/certifikáty/číst | Získá seznam certifikátů. |
> | Akce | Microsoft. Web/certifikáty/Write | Přidejte nový certifikát nebo aktualizujte stávající. |
> | Akce | microsoft.web/checknameavailability/read | Ověřte, zda je název prostředku k dispozici. |
> | Akce | microsoft.web/classicmobileservices/read | Získejte klasický Mobile Services. |
> | Akce | Microsoft.Web/connectionGateways/Delete | Odstraní bránu připojení. |
> | Akce | Microsoft. Web/connectionGateways/JOIN/Action | Připojí se k bráně připojení. |
> | Akce | Microsoft.Web/connectionGateways/ListStatus/Action | Zobrazí seznam stavů brány připojení. |
> | Akce | Microsoft. Web/connectionGateways/Move/Action | Přesune bránu připojení. |
> | Akce | Microsoft. Web/connectionGateways/Read | Získat seznam bran připojení. |
> | Akce | Microsoft. Web/connectionGateways/Write | Vytvoří nebo aktualizuje bránu připojení. |
> | Akce | Microsoft. Web/Connections/confirmconsentcode/Action | Potvrďte kód souhlasu připojení. |
> | Akce | Microsoft.Web/connections/Delete | Odstraní připojení. |
> | Akce | Microsoft. Web/Connections/JOIN/Action | Připojí se k připojení. |
> | Akce | microsoft.web/connections/listconsentlinks/action | Vypíše odkazy na vyjádření připojení. |
> | Akce | Microsoft. Web/Connections/Move/Action | Přesune připojení. |
> | Akce | Microsoft. Web/Connections/Read | Získá seznam připojení. |
> | Akce | Microsoft. Web/Connections/Write | Vytvoří nebo aktualizuje připojení. |
> | Akce | Microsoft.Web/customApis/Delete | Odstraní vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrahuje definici rozhraní API z WSDL. |
> | Akce | Microsoft. Web/customApis/JOIN/Action | Připojí se k vlastnímu rozhraní API. |
> | Akce | Microsoft.Web/customApis/listWsdlInterfaces/Action | Obsahuje rozhraní WSDL pro vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/Move/Action | Přesune vlastní rozhraní API. |
> | Akce | Microsoft. Web/customApis/Read | Získá seznam vlastního rozhraní API. |
> | Akce | Microsoft. Web/customApis/Write | Vytvoří nebo aktualizuje vlastní rozhraní API. |
> | Akce | Microsoft.Web/deletedSites/Read | Získat vlastnosti odstraněné webové aplikace |
> | Akce | Microsoft. Web/deploymentlocations/Read | Získá umístění nasazení. |
> | Akce | Microsoft. Web/geografická umístění/čtení | Získá seznam geografických oblastí. |
> | Akce | microsoft.web/hostingenvironments/capacities/read | Získejte kapacity hostujících prostředí. |
> | Akce | Microsoft.Web/hostingEnvironments/Delete | Odstranit App Service Environment |
> | Akce | microsoft.web/hostingenvironments/detectors/read | Získejte detektory hostujících prostředí. |
> | Akce | microsoft.web/hostingenvironments/diagnostics/read | Získejte diagnostiku prostředí hostování. |
> | Akce | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Získá koncové body sítě pro všechny příchozí závislosti. |
> | Akce | Microsoft. Web/hostingEnvironments/JOIN/Action | Připojí se k App Service Environment. |
> | Akce | microsoft.web/hostingenvironments/metricdefinitions/read | Získejte definice metrik prostředí hostování. |
> | Akce | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Získat definice metrik prostředí hostování s více rolemi fondy rolí. |
> | Akce | microsoft.web/hostingenvironments/multirolepools/metrics/read | Získejte prostředí hostování metrik fondů s více rolemi. |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Získání vlastností fondu front-endu v App Service Environment |
> | Akce | Microsoft. Web/hostingenvironments/multirolepools/SKU/Read | Získejte hostující prostředí fondy rolí. |
> | Akce | microsoft.web/hostingenvironments/multirolepools/usages/read | Získejte prostředí hostování více fondů rolí. |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Vytvoří nový fond front-endu ve App Service Environment nebo aktualizuje existující. |
> | Akce | microsoft.web/hostingenvironments/operations/read | Získejte operace hostování prostředí. |
> | Akce | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Získá koncové body sítě všech odchozích závislostí. |
> | Akce | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/Action | Approve Private Endpoint Connections |
> | Akce | Microsoft.Web/hostingEnvironments/Read | Získá vlastnosti App Service Environment. |
> | Akce | Microsoft. Web/hostingEnvironments/restart/Action | Restartovat všechny počítače v App Service Environment |
> | Akce | microsoft.web/hostingenvironments/resume/action | Obnoví hostující prostředí. |
> | Akce | microsoft.web/hostingenvironments/serverfarms/read | Získejte prostředí pro hostování App Service plány. |
> | Akce | microsoft.web/hostingenvironments/sites/read | Získat Web Apps hostování prostředí. |
> | Akce | microsoft.web/hostingenvironments/suspend/action | Pozastavit hostující prostředí. |
> | Akce | microsoft.web/hostingenvironments/usages/read | Získejte využití prostředí hostování. |
> | Akce | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Získejte prostředí hostování Workerpools definice metrik. |
> | Akce | Microsoft. Web/hostingenvironments/workerpools/metriky/čtení | Získejte prostředí hostování Workerpools metrikami. |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/Read | Získání vlastností fondu pracovních procesů ve App Service Environment |
> | Akce | microsoft.web/hostingenvironments/workerpools/skus/read | Získejte hostující prostředí Workerpools SKU. |
> | Akce | microsoft.web/hostingenvironments/workerpools/usages/read | Získejte prostředí hostování Workerpools využití. |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/Write | Vytvoří nový fond pracovních procesů ve App Service Environment nebo aktualizuje stávající. |
> | Akce | Microsoft. Web/hostingEnvironments/Write | Vytvořit nový App Service Environment nebo aktualizovat existující |
> | Akce | Microsoft. Web/ishostingenvironmentnameavailable/Read | Získat, pokud je k dispozici název hostitelského prostředí. |
> | Akce | Microsoft. Web/ishostnameavailable/Read | Ověřte, zda je název hostitele k dispozici. |
> | Akce | Microsoft. Web/isusernameavailable/Read | Ověřte, zda je uživatelské jméno k dispozici. |
> | Akce | Microsoft.Web/listSitesAssignedToHostName/Read | Získá názvy webů přiřazených k názvu hostitele. |
> | Akce | microsoft.web/locations/apioperations/read | Načte operace rozhraní API umístění. |
> | Akce | microsoft.web/locations/connectiongatewayinstallations/read | Získejte umístění pro instalaci brány pro připojení. |
> | Akce | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Oznámení o odstranění virtuální sítě nebo podsítě pro umístění. |
> | Akce | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrahujte definici rozhraní API ze WSDL pro umístění. |
> | Akce | microsoft.web/locations/listwsdlinterfaces/action | Vypíše rozhraní WSDL pro umístění. |
> | Akce | microsoft.web/locations/managedapis/apioperations/read | Získejte umístění spravovaných operací rozhraní API. |
> | Akce | Microsoft. Web/Locations/Inspirujte/JOIN/Action | Připojí spravované rozhraní API. |
> | Akce | microsoft.web/locations/managedapis/read | Získat umístění spravovaná rozhraní API. |
> | Akce | Microsoft. Web/Locations/operationResults/Read | Získat operace. |
> | Akce | Microsoft. Web/umístění/operace/čtení | Získat operace. |
> | Akce | microsoft.web/operations/read | Získat operace. |
> | Akce | microsoft.web/publishingusers/read | Získat uživatele pro publikování. |
> | Akce | microsoft.web/publishingusers/write | Aktualizujte uživatele publikování. |
> | Akce | Microsoft. Web/doporučení/čtení | Získejte seznam doporučení pro předplatná. |
> | Akce | microsoft.web/register/action | Zaregistrujte poskytovatele prostředků Microsoft. Web pro předplatné. |
> | Akce | microsoft.web/resourcehealthmetadata/read | Získá metadata Resource Health. |
> | Akce | microsoft.web/serverfarms/capabilities/read | Získejte možnosti plánů App Service. |
> | Akce | Microsoft.Web/serverfarms/Delete | Odstraní existující plán App Service. |
> | Akce | Microsoft. Web/serverových farem/eventGridFilters/DELETE | Odstranit Event Grid filtr na serverové farmě. |
> | Akce | Microsoft. Web/serverových farem/eventGridFilters/Read | Získat Event Grid filtr na serverové farmě. |
> | Akce | Microsoft. Web/serverových farem/eventGridFilters/Write | Vložte Event Grid filtr na serverovou farmu. |
> | Akce | microsoft.web/serverfarms/firstpartyapps/settings/delete | Odstranit nastavení aplikací pro první stranu App Service plánů |
> | Akce | microsoft.web/serverfarms/firstpartyapps/settings/read | Získá nastavení aplikací pro první stranu App Service plánů. |
> | Akce | microsoft.web/serverfarms/firstpartyapps/settings/write | Aktualizuje nastavení aplikací pro první stranu App Service plánů. |
> | Akce | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Odstranění App Service plánuje přenos oborů názvů Hybrid Connection. |
> | Akce | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Získá App Service naplánuje přenos oborů názvů Hybrid Connection. |
> | Akce | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Získá App Service přenáší obory názvů Hybrid Connection Web Apps. |
> | Akce | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Získá App Service plány plánů hybridního připojení. |
> | Akce | microsoft.web/serverfarms/hybridconnectionrelays/read | Získá App Service Relay naplánuje přenos Hybrid Connection. |
> | Akce | microsoft.web/serverfarms/metricdefinitions/read | Získá definice metriky pro plány App Service. |
> | Akce | microsoft.web/serverfarms/metrics/read | Získejte metriky plánů App Service. |
> | Akce | microsoft.web/serverfarms/operationresults/read | Získejte výsledky operace App Service plánů. |
> | Akce | Microsoft.Web/serverfarms/Read | Získat vlastnosti v plánu App Service |
> | Akce | Microsoft.Web/serverfarms/restartSites/Action | Restartování všech Web Apps v plánu App Service |
> | Akce | microsoft.web/serverfarms/sites/read | Získejte Web Apps plány App Service. |
> | Akce | microsoft.web/serverfarms/skus/read | Získejte skladové položky plánů App Service. |
> | Akce | microsoft.web/serverfarms/usages/read | Získejte využití plánů App Service. |
> | Akce | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Aktualizace plánů App Service Virtual Network připojení brány. |
> | Akce | microsoft.web/serverfarms/virtualnetworkconnections/read | Získá plány App Service Virtual Network připojení. |
> | Akce | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Odstraní plány App Service Virtual Network trasy připojení. |
> | Akce | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Získá plány App Service Virtual Network trasy připojení. |
> | Akce | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aktualizujte plány App Service Virtual Network trasy připojení. |
> | Akce | microsoft.web/serverfarms/workers/reboot/action | Restartuje App Service plánuje pracovní procesy. |
> | Akce | Microsoft.Web/serverfarms/Write | Vytvoří nový plán App Service nebo aktualizuje stávající. |
> | Akce | microsoft.web/sites/analyzecustomhostname/read | Analyzujte vlastní název hostitele. |
> | Akce | Microsoft.Web/sites/applySlotConfig/Action | Použít konfiguraci slotu webové aplikace z cílového slotu na aktuální webovou aplikaci |
> | Akce | Microsoft. Web/weby/zálohování/akce | Vytvoření nové zálohy webové aplikace |
> | Akce | microsoft.web/sites/backup/read | Získat zálohu Web Apps. |
> | Akce | microsoft.web/sites/backup/write | Aktualizujte Web Apps zálohu. |
> | Akce | microsoft.web/sites/backups/action | Zjistí existující zálohu aplikace, kterou je možné obnovit z objektu BLOB ve službě Azure Storage. |
> | Akce | microsoft.web/sites/backups/delete | Odstraňte zálohy Web Apps. |
> | Akce | microsoft.web/sites/backups/list/action | Vypíše Web Apps zálohy. |
> | Akce | Microsoft. Web/weby/zálohy/čtení | Získat vlastnosti zálohování webové aplikace |
> | Akce | microsoft.web/sites/backups/restore/action | Obnovte Web Apps zálohy. |
> | Akce | microsoft.web/sites/backups/write | Aktualizace záloh Web Apps. |
> | Akce | microsoft.web/sites/config/delete | Odstraní konfiguraci Web Apps. |
> | Akce | Microsoft. Web/weby/config/list/Action | Vypíše nastavení citlivá na zabezpečení webové aplikace, jako jsou například přihlašovací údaje pro publikování, nastavení aplikace a připojovací řetězce. |
> | Akce | Microsoft.Web/sites/config/Read | Získat nastavení konfigurace webové aplikace |
> | Akce | microsoft.web/sites/config/snapshots/read | Získat Web Apps konfigurační snímky. |
> | Akce | Microsoft.Web/sites/config/Write | Aktualizovat konfigurační nastavení webové aplikace |
> | Akce | microsoft.web/sites/containerlogs/action | Získejte protokoly kontejneru zip pro webovou aplikaci. |
> | Akce | Microsoft. Web/weby/containerlogs/stáhnout/akce | Stáhnout Web Apps protokoly kontejnerů. |
> | Akce | microsoft.web/sites/continuouswebjobs/delete | Odstraňte Web Apps průběžné webové úlohy. |
> | Akce | microsoft.web/sites/continuouswebjobs/read | Získejte Web Apps průběžné webové úlohy. |
> | Akce | Microsoft. Web/Sites/continuouswebjobs/Start/Action | Spusťte Web Apps průběžné webové úlohy. |
> | Akce | microsoft.web/sites/continuouswebjobs/stop/action | Zastavte Web Apps průběžné webové úlohy. |
> | Akce | Microsoft.Web/sites/Delete | Odstranění existující webové aplikace |
> | Akce | microsoft.web/sites/deployments/delete | Odstraňte Web Apps nasazení. |
> | Akce | microsoft.web/sites/deployments/log/read | Získá protokol nasazení Web Apps. |
> | Akce | microsoft.web/sites/deployments/read | Získat Web Apps nasazení. |
> | Akce | microsoft.web/sites/deployments/write | Aktualizujte nasazení Web Apps. |
> | Akce | microsoft.web/sites/detectors/read | Získat Web Apps detektory. |
> | Akce | Microsoft. Web/weby/Diagnostika/analýzy/provedení/akce | Spusťte analýzu diagnostiky Web Apps. |
> | Akce | microsoft.web/sites/diagnostics/analyses/read | Získat analýzu diagnostiky Web Apps. |
> | Akce | microsoft.web/sites/diagnostics/aspnetcore/read | Získejte diagnostiku Web Apps pro ASP.NET Core aplikaci. |
> | Akce | microsoft.web/sites/diagnostics/autoheal/read | Získejte diagnostiku Web Apps pro autoretušování. |
> | Akce | microsoft.web/sites/diagnostics/deployment/read | Získat Web Apps nasazení diagnostiky. |
> | Akce | microsoft.web/sites/diagnostics/deployments/read | Získat Web Apps nasazení diagnostiky. |
> | Akce | Microsoft. Web/weby/Diagnostika/detektory/provedení/akce | Spusťte detektor diagnostiky Web Apps. |
> | Akce | microsoft.web/sites/diagnostics/detectors/read | Získat Web Apps detektor diagnostiky |
> | Akce | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Získat neúspěšné požadavky diagnostiky Web Apps na identifikátor URI. |
> | Akce | microsoft.web/sites/diagnostics/frebanalysis/read | Získejte analýzu Web Apps FREB diagnostiky. |
> | Akce | microsoft.web/sites/diagnostics/loganalyzer/read | Get Web Apps Diagnostics Log Analyzer. |
> | Akce | microsoft.web/sites/diagnostics/read | Get Web Apps Diagnostics Categories. |
> | Akce | microsoft.web/sites/diagnostics/runtimeavailability/read | Get Web Apps Diagnostics Runtime Availability. |
> | Akce | microsoft.web/sites/diagnostics/servicehealth/read | Get Web Apps Diagnostics Service Health. |
> | Akce | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Get Web Apps Diagnostics Site CPU Analysis. |
> | Akce | microsoft.web/sites/diagnostics/sitecrashes/read | Get Web Apps Diagnostics Site Crashes. |
> | Akce | microsoft.web/sites/diagnostics/sitelatency/read | Get Web Apps Diagnostics Site Latency. |
> | Akce | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Get Web Apps Diagnostics Site Memory Analysis. |
> | Akce | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Get Web Apps Diagnostics Site Restart Setting Update. |
> | Akce | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Get Web Apps Diagnostics Site Restart User Initiated. |
> | Akce | microsoft.web/sites/diagnostics/siteswap/read | Get Web Apps Diagnostics Site Swap. |
> | Akce | microsoft.web/sites/diagnostics/threadcount/read | Get Web Apps Diagnostics Thread Count. |
> | Akce | microsoft.web/sites/diagnostics/workeravailability/read | Get Web Apps Diagnostics Workeravailability. |
> | Akce | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Get Web Apps Diagnostics Worker Process Recycle. |
> | Akce | microsoft.web/sites/domainownershipidentifiers/read | Get Web Apps Domain Ownership Identifiers. |
> | Akce | microsoft.web/sites/domainownershipidentifiers/write | Update Web Apps Domain Ownership Identifiers. |
> | Akce | Microsoft.Web/sites/eventGridFilters/delete | Delete Event Grid Filter on web app. |
> | Akce | Microsoft.Web/sites/eventGridFilters/read | Get Event Grid Filter on web app. |
> | Akce | Microsoft.Web/sites/eventGridFilters/write | Put Event Grid Filter on web app. |
> | Akce | microsoft.web/sites/extensions/delete | Delete Web Apps Site Extensions. |
> | Akce | microsoft.web/sites/extensions/read | Get Web Apps Site Extensions. |
> | Akce | microsoft.web/sites/extensions/write | Update Web Apps Site Extensions. |
> | Akce | microsoft.web/sites/functions/action | Functions Web Apps. |
> | Akce | microsoft.web/sites/functions/delete | Delete Web Apps Functions. |
> | Akce | microsoft.web/sites/functions/keys/delete | Delete Function keys. |
> | Akce | microsoft.web/sites/functions/keys/write | Update Function keys. |
> | Akce | microsoft.web/sites/functions/listkeys/action | List Function keys. |
> | Akce | microsoft.web/sites/functions/listsecrets/action | List Function secrets. |
> | Akce | microsoft.web/sites/functions/masterkey/read | Get Web Apps Functions Masterkey. |
> | Akce | microsoft.web/sites/functions/properties/read | Read Web Apps Functions Properties. |
> | Akce | microsoft.web/sites/functions/properties/write | Update Web Apps Functions Properties. |
> | Akce | microsoft.web/sites/functions/read | Get Web Apps Functions. |
> | Akce | microsoft.web/sites/functions/token/read | Get Web Apps Functions Token. |
> | Akce | microsoft.web/sites/functions/write | Update Web Apps Functions. |
> | Akce | microsoft.web/sites/host/functionkeys/delete | Delete Functions Host Function keys. |
> | Akce | microsoft.web/sites/host/functionkeys/write | Update Functions Host Function keys. |
> | Akce | microsoft.web/sites/host/listkeys/action | List Functions Host keys. |
> | Akce | microsoft.web/sites/host/listsyncstatus/action | List Sync Function Triggers Status. |
> | Akce | microsoft.web/sites/host/properties/read | Read Web Apps Functions Host Properties. |
> | Akce | microsoft.web/sites/host/sync/action | Aktivační události funkce synchronizace. |
> | Akce | microsoft.web/sites/host/systemkeys/delete | Delete Functions Host System keys. |
> | Akce | microsoft.web/sites/host/systemkeys/write | Update Functions Host System keys. |
> | Akce | microsoft.web/sites/hostnamebindings/delete | Delete Web Apps Hostname Bindings. |
> | Akce | microsoft.web/sites/hostnamebindings/read | Get Web Apps Hostname Bindings. |
> | Akce | microsoft.web/sites/hostnamebindings/write | Update Web Apps Hostname Bindings. |
> | Akce | microsoft.web/sites/hostruntime/functions/keys/read | Get Web Apps Hostruntime Functions Keys. |
> | Akce | Microsoft.Web/sites/hostruntime/host/_master/read | Get Function App's master key for admin operations |
> | Akce | Microsoft.Web/sites/hostruntime/host/action | Perform Function App runtime action like sync triggers, add functions, invoke functions, delete functions etc. |
> | Akce | microsoft.web/sites/hostruntime/host/read | Get Web Apps Hostruntime Host. |
> | Akce | microsoft.web/sites/hybridconnection/delete | Delete Web Apps Hybrid Connection. |
> | Akce | microsoft.web/sites/hybridconnection/read | Get Web Apps Hybrid Connection. |
> | Akce | microsoft.web/sites/hybridconnection/write | Update Web Apps Hybrid Connection. |
> | Akce | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Delete Web Apps Hybrid Connection Namespaces Relays. |
> | Akce | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | List Keys Web Apps Hybrid Connection Namespaces Relays. |
> | Akce | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Get Web Apps Hybrid Connection Namespaces Relays. |
> | Akce | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Update Web Apps Hybrid Connection Namespaces Relays. |
> | Akce | microsoft.web/sites/hybridconnectionrelays/read | Get Web Apps Hybrid Connection Relays. |
> | Akce | microsoft.web/sites/instances/deployments/delete | Delete Web Apps Instances Deployments. |
> | Akce | microsoft.web/sites/instances/deployments/read | Get Web Apps Instances Deployments. |
> | Akce | microsoft.web/sites/instances/extensions/log/read | Get Web Apps Instances Extensions Log. |
> | Akce | microsoft.web/sites/instances/extensions/processes/read | Get Web Apps Instances Extensions Processes. |
> | Akce | microsoft.web/sites/instances/extensions/read | Get Web Apps Instances Extensions. |
> | Akce | microsoft.web/sites/instances/processes/delete | Delete Web Apps Instances Processes. |
> | Akce | microsoft.web/sites/instances/processes/modules/read | Get Web Apps Instances Processes Modules. |
> | Akce | microsoft.web/sites/instances/processes/read | Get Web Apps Instances Processes. |
> | Akce | microsoft.web/sites/instances/processes/threads/read | Get Web Apps Instances Processes Threads. |
> | Akce | microsoft.web/sites/instances/read | Get Web Apps Instances. |
> | Akce | microsoft.web/sites/listsyncfunctiontriggerstatus/action | List Sync Function Trigger Status. |
> | Akce | microsoft.web/sites/metricdefinitions/read | Get Web Apps Metric Definitions. |
> | Akce | microsoft.web/sites/metrics/read | Get Web Apps Metrics. |
> | Akce | microsoft.web/sites/metricsdefinitions/read | Get Web Apps Metrics Definitions. |
> | Akce | microsoft.web/sites/migratemysql/action | Migrate MySql Web Apps. |
> | Akce | microsoft.web/sites/migratemysql/read | Get Web Apps Migrate MySql. |
> | Akce | microsoft.web/sites/networktrace/action | Network Trace Web Apps. |
> | Akce | microsoft.web/sites/networktraces/operationresults/read | Get Web Apps Network Trace Operation Results. |
> | Akce | microsoft.web/sites/newpassword/action | Newpassword Web Apps. |
> | Akce | microsoft.web/sites/operationresults/read | Get Web Apps Operation Results. |
> | Akce | microsoft.web/sites/operations/read | Get Web Apps Operations. |
> | Akce | microsoft.web/sites/perfcounters/read | Get Web Apps Performance Counters. |
> | Akce | microsoft.web/sites/premieraddons/delete | Delete Web Apps Premier Addons. |
> | Akce | microsoft.web/sites/premieraddons/read | Get Web Apps Premier Addons. |
> | Akce | microsoft.web/sites/premieraddons/write | Update Web Apps Premier Addons. |
> | Akce | microsoft.web/sites/privateaccess/read | Get data around private site access enablement and authorized Virtual Networks that can access the site. |
> | Akce | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Approve Private Endpoint Connections |
> | Akce | microsoft.web/sites/processes/modules/read | Get Web Apps Processes Modules. |
> | Akce | microsoft.web/sites/processes/read | Get Web Apps Processes. |
> | Akce | microsoft.web/sites/processes/threads/read | Get Web Apps Processes Threads. |
> | Akce | microsoft.web/sites/publiccertificates/delete | Delete Web Apps Public Certificates. |
> | Akce | microsoft.web/sites/publiccertificates/read | Get Web Apps Public Certificates. |
> | Akce | microsoft.web/sites/publiccertificates/write | Update Web Apps Public Certificates. |
> | Akce | Microsoft.Web/sites/publish/Action | Publish a Web App |
> | Akce | Microsoft.Web/sites/publishxml/Action | Get publishing profile xml for a Web App |
> | Akce | microsoft.web/sites/publishxml/read | Get Web Apps Publishing XML. |
> | Akce | Microsoft.Web/sites/Read | Get the properties of a Web App |
> | Akce | microsoft.web/sites/recommendationhistory/read | Get Web Apps Recommendation History. |
> | Akce | microsoft.web/sites/recommendations/disable/action | Disable Web Apps Recommendations. |
> | Akce | Microsoft.Web/sites/recommendations/Read | Get the list of recommendations for web app. |
> | Akce | microsoft.web/sites/recover/action | Recover Web Apps. |
> | Akce | Microsoft.Web/sites/resetSlotConfig/Action | Reset web app configuration |
> | Akce | microsoft.web/sites/resourcehealthmetadata/read | Get Web Apps Resource Health Metadata. |
> | Akce | Microsoft.Web/sites/restart/Action | Restart a Web App |
> | Akce | microsoft.web/sites/restore/read | Get Web Apps Restore. |
> | Akce | microsoft.web/sites/restore/write | Restore Web Apps. |
> | Akce | microsoft.web/sites/restorefrombackupblob/action | Restore Web App From Backup Blob. |
> | Akce | microsoft.web/sites/restorefromdeletedapp/action | Restore Web Apps From Deleted App. |
> | Akce | microsoft.web/sites/restoresnapshot/action | Restore Web Apps Snapshots. |
> | Akce | microsoft.web/sites/siteextensions/delete | Delete Web Apps Site Extensions. |
> | Akce | microsoft.web/sites/siteextensions/read | Get Web Apps Site Extensions. |
> | Akce | microsoft.web/sites/siteextensions/write | Update Web Apps Site Extensions. |
> | Akce | microsoft.web/sites/slots/analyzecustomhostname/read | Get Web Apps Slots Analyze Custom Hostname. |
> | Akce | Microsoft.Web/sites/slots/applySlotConfig/Action | Apply web app slot configuration from target slot to the current slot. |
> | Akce | Microsoft.Web/sites/slots/backup/Action | Create new Web App Slot backup. |
> | Akce | microsoft.web/sites/slots/backup/read | Get Web Apps Slots Backup. |
> | Akce | microsoft.web/sites/slots/backup/write | Update Web Apps Slots Backup. |
> | Akce | microsoft.web/sites/slots/backups/action | Discover Web Apps Slots Backups. |
> | Akce | microsoft.web/sites/slots/backups/delete | Delete Web Apps Slots Backups. |
> | Akce | microsoft.web/sites/slots/backups/list/action | List Web Apps Slots Backups. |
> | Akce | Microsoft.Web/sites/slots/backups/Read | Get the properties of a web app slots' backup |
> | Akce | microsoft.web/sites/slots/backups/restore/action | Restore Web Apps Slots Backups. |
> | Akce | microsoft.web/sites/slots/config/delete | Delete Web Apps Slots Config. |
> | Akce | Microsoft.Web/sites/slots/config/list/Action | List Web App Slot's security sensitive settings, such as publishing credentials, app settings and connection strings |
> | Akce | Microsoft.Web/sites/slots/config/Read | Get Web App Slot's configuration settings |
> | Akce | Microsoft.Web/sites/slots/config/Write | Update Web App Slot's configuration settings |
> | Akce | microsoft.web/sites/slots/containerlogs/action | Get Zipped Container Logs for Web App Slot. |
> | Akce | microsoft.web/sites/slots/containerlogs/download/action | Download Web Apps Slots Container Logs. |
> | Akce | microsoft.web/sites/slots/continuouswebjobs/delete | Delete Web Apps Slots Continuous Web Jobs. |
> | Akce | microsoft.web/sites/slots/continuouswebjobs/read | Get Web Apps Slots Continuous Web Jobs. |
> | Akce | microsoft.web/sites/slots/continuouswebjobs/start/action | Start Web Apps Slots Continuous Web Jobs. |
> | Akce | microsoft.web/sites/slots/continuouswebjobs/stop/action | Stop Web Apps Slots Continuous Web Jobs. |
> | Akce | Microsoft.Web/sites/slots/Delete | Delete an existing Web App Slot |
> | Akce | microsoft.web/sites/slots/deployments/delete | Delete Web Apps Slots Deployments. |
> | Akce | microsoft.web/sites/slots/deployments/log/read | Get Web Apps Slots Deployments Log. |
> | Akce | microsoft.web/sites/slots/deployments/read | Get Web Apps Slots Deployments. |
> | Akce | microsoft.web/sites/slots/deployments/write | Update Web Apps Slots Deployments. |
> | Akce | microsoft.web/sites/slots/detectors/read | Get Web Apps Slots Detectors. |
> | Akce | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Run Web Apps Slots Diagnostics Analysis. |
> | Akce | microsoft.web/sites/slots/diagnostics/analyses/read | Get Web Apps Slots Diagnostics Analysis. |
> | Akce | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Get Web Apps Slots Diagnostics for ASP.NET Core app. |
> | Akce | microsoft.web/sites/slots/diagnostics/autoheal/read | Get Web Apps Slots Diagnostics Autoheal. |
> | Akce | microsoft.web/sites/slots/diagnostics/deployment/read | Get Web Apps Slots Diagnostics Deployment. |
> | Akce | microsoft.web/sites/slots/diagnostics/deployments/read | Get Web Apps Slots Diagnostics Deployments. |
> | Akce | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Run Web Apps Slots Diagnostics Detector. |
> | Akce | microsoft.web/sites/slots/diagnostics/detectors/read | Get Web Apps Slots Diagnostics Detector. |
> | Akce | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Get Web Apps Slots Diagnostics FREB Analysis. |
> | Akce | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Get Web Apps Slots Diagnostics Log Analyzer. |
> | Akce | microsoft.web/sites/slots/diagnostics/read | Get Web Apps Slots Diagnostics. |
> | Akce | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Get Web Apps Slots Diagnostics Runtime Availability. |
> | Akce | microsoft.web/sites/slots/diagnostics/servicehealth/read | Get Web Apps Slots Diagnostics Service Health. |
> | Akce | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Get Web Apps Slots Diagnostics Site CPU Analysis. |
> | Akce | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Get Web Apps Slots Diagnostics Site Crashes. |
> | Akce | microsoft.web/sites/slots/diagnostics/sitelatency/read | Get Web Apps Slots Diagnostics Site Latency. |
> | Akce | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Get Web Apps Slots Diagnostics Site Memory Analysis. |
> | Akce | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Get Web Apps Slots Diagnostics Site Restart Setting Update. |
> | Akce | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Get Web Apps Slots Diagnostics Site Restart User Initiated. |
> | Akce | microsoft.web/sites/slots/diagnostics/siteswap/read | Get Web Apps Slots Diagnostics Site Swap. |
> | Akce | microsoft.web/sites/slots/diagnostics/threadcount/read | Get Web Apps Slots Diagnostics Thread Count. |
> | Akce | microsoft.web/sites/slots/diagnostics/workeravailability/read | Get Web Apps Slots Diagnostics Workeravailability. |
> | Akce | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Get Web Apps Slots Diagnostics Worker Process Recycle. |
> | Akce | microsoft.web/sites/slots/domainownershipidentifiers/read | Get Web Apps Slots Domain Ownership Identifiers. |
> | Akce | microsoft.web/sites/slots/functions/listsecrets/action | List Secrets Web Apps Slots Functions. |
> | Akce | microsoft.web/sites/slots/functions/read | Get Web Apps Slots Functions. |
> | Akce | microsoft.web/sites/slots/hostnamebindings/delete | Delete Web Apps Slots Hostname Bindings. |
> | Akce | microsoft.web/sites/slots/hostnamebindings/read | Get Web Apps Slots Hostname Bindings. |
> | Akce | microsoft.web/sites/slots/hostnamebindings/write | Update Web Apps Slots Hostname Bindings. |
> | Akce | microsoft.web/sites/slots/hybridconnection/delete | Delete Web Apps Slots Hybrid Connection. |
> | Akce | microsoft.web/sites/slots/hybridconnection/read | Get Web Apps Slots Hybrid Connection. |
> | Akce | microsoft.web/sites/slots/hybridconnection/write | Update Web Apps Slots Hybrid Connection. |
> | Akce | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Delete Web Apps Slots Hybrid Connection Namespaces Relays. |
> | Akce | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Update Web Apps Slots Hybrid Connection Namespaces Relays. |
> | Akce | microsoft.web/sites/slots/hybridconnectionrelays/read | Get Web Apps Slots Hybrid Connection Relays. |
> | Akce | microsoft.web/sites/slots/instances/deployments/read | Get Web Apps Slots Instances Deployments. |
> | Akce | microsoft.web/sites/slots/instances/processes/delete | Delete Web Apps Slots Instances Processes. |
> | Akce | microsoft.web/sites/slots/instances/processes/read | Get Web Apps Slots Instances Processes. |
> | Akce | microsoft.web/sites/slots/instances/read | Get Web Apps Slots Instances. |
> | Akce | microsoft.web/sites/slots/metricdefinitions/read | Get Web Apps Slots Metric Definitions. |
> | Akce | microsoft.web/sites/slots/metrics/read | Get Web Apps Slots Metrics. |
> | Akce | microsoft.web/sites/slots/migratemysql/read | Get Web Apps Slots Migrate MySql. |
> | Akce | microsoft.web/sites/slots/networktrace/action | Network Trace Web Apps Slots. |
> | Akce | microsoft.web/sites/slots/networktraces/operationresults/read | Get Web Apps Slots Network Trace Operation Results. |
> | Akce | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps Slots. |
> | Akce | microsoft.web/sites/slots/operationresults/read | Get Web Apps Slots Operation Results. |
> | Akce | microsoft.web/sites/slots/operations/read | Get Web Apps Slots Operations. |
> | Akce | microsoft.web/sites/slots/perfcounters/read | Get Web Apps Slots Performance Counters. |
> | Akce | microsoft.web/sites/slots/phplogging/read | Get Web Apps Slots Phplogging. |
> | Akce | microsoft.web/sites/slots/premieraddons/delete | Delete Web Apps Slots Premier Addons. |
> | Akce | microsoft.web/sites/slots/premieraddons/read | Get Web Apps Slots Premier Addons. |
> | Akce | microsoft.web/sites/slots/premieraddons/write | Update Web Apps Slots Premier Addons. |
> | Akce | microsoft.web/sites/slots/processes/read | Get Web Apps Slots Processes. |
> | Akce | microsoft.web/sites/slots/publiccertificates/delete | Delete Web Apps Slots Public Certificates. |
> | Akce | microsoft.web/sites/slots/publiccertificates/read | Get Web Apps Slots Public Certificates. |
> | Akce | microsoft.web/sites/slots/publiccertificates/write | Create or Update Web Apps Slots Public Certificates. |
> | Akce | Microsoft.Web/sites/slots/publish/Action | Publish a Web App Slot |
> | Akce | Microsoft.Web/sites/slots/publishxml/Action | Get publishing profile xml for Web App Slot |
> | Akce | Microsoft.Web/sites/slots/Read | Get the properties of a Web App deployment slot |
> | Akce | microsoft.web/sites/slots/recover/action | Recover Web Apps Slots. |
> | Akce | Microsoft.Web/sites/slots/resetSlotConfig/Action | Reset web app slot configuration |
> | Akce | microsoft.web/sites/slots/resourcehealthmetadata/read | Get Web Apps Slots Resource Health Metadata. |
> | Akce | Microsoft.Web/sites/slots/restart/Action | Restart a Web App Slot |
> | Akce | microsoft.web/sites/slots/restore/read | Get Web Apps Slots Restore. |
> | Akce | microsoft.web/sites/slots/restore/write | Restore Web Apps Slots. |
> | Akce | microsoft.web/sites/slots/restorefrombackupblob/action | Restore Web Apps Slot From Backup Blob. |
> | Akce | microsoft.web/sites/slots/restorefromdeletedapp/action | Restore Web App Slots From Deleted App. |
> | Akce | microsoft.web/sites/slots/restoresnapshot/action | Restore Web Apps Slots Snapshots. |
> | Akce | microsoft.web/sites/slots/siteextensions/delete | Delete Web Apps Slots Site Extensions. |
> | Akce | microsoft.web/sites/slots/siteextensions/read | Get Web Apps Slots Site Extensions. |
> | Akce | microsoft.web/sites/slots/siteextensions/write | Update Web Apps Slots Site Extensions. |
> | Akce | Microsoft.Web/sites/slots/slotsdiffs/Action | Získání rozdílů v konfiguraci mezi webovou aplikací a sloty |
> | Akce | Microsoft.Web/sites/slots/slotsswap/Action | Prohození slotů nasazení webové aplikace |
> | Akce | microsoft.web/sites/slots/snapshots/read | Get Web Apps Slots Snapshots. |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Delete | Delete Web App Slot's source control configuration settings |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Read | Získat nastavení konfigurace správy zdrojového kódu pro pozici webové aplikace |
> | Akce | Microsoft. Web/weby/sloty/sourcecontrols/Write | Aktualizovat konfigurační nastavení správy zdrojových kódů v pozici webové aplikace |
> | Akce | Microsoft. Web/weby/sloty/spustit/akce | Spustit slot webové aplikace |
> | Akce | Microsoft. Web/weby/sloty/zastavení/akce | Zastavit slot webové aplikace |
> | Akce | microsoft.web/sites/slots/sync/action | Synchronizuje Web Apps sloty. |
> | Akce | microsoft.web/sites/slots/triggeredwebjobs/delete | Odstraní Web Apps sloty aktivované WebJobs. |
> | Akce | microsoft.web/sites/slots/triggeredwebjobs/read | Získejte Web Apps sloty aktivované WebJobs. |
> | Akce | microsoft.web/sites/slots/triggeredwebjobs/run/action | Spusťte Web Apps sloty aktivované WebJobs. |
> | Akce | microsoft.web/sites/slots/usages/read | Získat Web Apps využití slotů. |
> | Akce | microsoft.web/sites/slots/virtualnetworkconnections/delete | Odstraní Web Apps sloty Virtual Network připojení. |
> | Akce | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Aktualizujte Web Apps sloty Virtual Network připojení brány. |
> | Akce | microsoft.web/sites/slots/virtualnetworkconnections/read | Získat Virtual Network připojení Web Apps sloty. |
> | Akce | microsoft.web/sites/slots/virtualnetworkconnections/write | Aktualizační sloty Web Apps Virtual Network připojení. |
> | Akce | microsoft.web/sites/slots/webjobs/read | Získejte WebJobs pro Web Apps sloty. |
> | Akce | Microsoft. Web/weby/sloty/zápis | Vytvořit nový slot webové aplikace nebo aktualizovat existující pozici |
> | Akce | Microsoft. Web/weby/slotsdiffs/Action | Získání rozdílů v konfiguraci mezi webovou aplikací a sloty |
> | Akce | Microsoft. Web/weby/slotsswap/Action | Prohození slotů nasazení webové aplikace |
> | Akce | microsoft.web/sites/snapshots/read | Získat Web Apps snímky. |
> | Akce | Microsoft.Web/sites/sourcecontrols/Delete | Odstranit konfigurační nastavení správy zdrojů webové aplikace |
> | Akce | Microsoft. Web/Sites/sourcecontrols/Read | Získat konfigurační nastavení správy zdrojů webové aplikace |
> | Akce | Microsoft. Web/weby/sourcecontrols/Write | Aktualizovat konfigurační nastavení správy zdrojů webové aplikace |
> | Akce | Microsoft. Web/Sites/Start/Action | Spuštění webové aplikace |
> | Akce | Microsoft. Web/weby/zastavení/akce | Zastavení webové aplikace |
> | Akce | microsoft.web/sites/sync/action | Synchronizace Web Apps. |
> | Akce | microsoft.web/sites/syncfunctiontriggers/action | Aktivační události funkce synchronizace. |
> | Akce | microsoft.web/sites/triggeredwebjobs/delete | Odstraní Web Apps aktivované webové úlohy. |
> | Akce | microsoft.web/sites/triggeredwebjobs/history/read | Získat Web Apps aktivované historii webových úloh |
> | Akce | microsoft.web/sites/triggeredwebjobs/read | Získejte Web Apps aktivované webové úlohy. |
> | Akce | microsoft.web/sites/triggeredwebjobs/run/action | Spusťte Web Apps aktivované webové úlohy. |
> | Akce | microsoft.web/sites/usages/read | Získat Web Apps využití. |
> | Akce | microsoft.web/sites/virtualnetworkconnections/delete | Odstraní připojení Web Apps Virtual Network. |
> | Akce | microsoft.web/sites/virtualnetworkconnections/gateways/read | Získá Web Apps Virtual Network připojení brány. |
> | Akce | microsoft.web/sites/virtualnetworkconnections/gateways/write | Aktualizuje brány Web Apps Virtual Network připojení. |
> | Akce | microsoft.web/sites/virtualnetworkconnections/read | Získá Web Apps Virtual Network připojení. |
> | Akce | microsoft.web/sites/virtualnetworkconnections/write | Aktualizace Web Apps Virtual Network připojení. |
> | Akce | microsoft.web/sites/webjobs/read | Získat Web Apps WebJobs. |
> | Akce | Microsoft.Web/sites/Write | Vytvořte novou webovou aplikaci nebo aktualizujte některou z existujících. |
> | Akce | Microsoft. Web/SKU/Read | Získat SKU. |
> | Akce | microsoft.web/sourcecontrols/read | Získat zdrojové ovládací prvky. |
> | Akce | microsoft.web/sourcecontrols/write | Aktualizuje ovládací prvky zdroje. |
> | Akce | microsoft.web/unregister/action | Zrušte registraci poskytovatele prostředků Microsoft. Web pro předplatné. |
> | Akce | Microsoft. Web/ověření/akce | Oproti. |
> | Akce | Microsoft. Web/verifyhostingenvironmentvnet/Action | Ověřte virtuální síť hostujícího prostředí. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft. monitor zátěže byl/komponenty/číst | Načte součásti pro daný prostředek. |
> | Akce | Microsoft. monitor zátěže byl/componentsSummary/Read | Získá souhrn komponent. |
> | Akce | Microsoft. monitor zátěže byl/monitorInstances/Read | Načte instance monitorování pro daný prostředek. |
> | Akce | Microsoft. monitor zátěže byl/monitorInstancesSummary/Read | Získá souhrn instancí monitorování. |
> | Akce | Microsoft. monitor zátěže byl/Monitors/Read | Získá monitorování pro daný prostředek. |
> | Akce | Microsoft. monitor zátěže byl/Monitors/Write | Konfigurace monitorování pro prostředek |
> | Akce | Microsoft. monitor zátěže byl/notificationSettings/Read | Získá nastavení oznámení pro daný prostředek. |
> | Akce | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurovat nastavení oznámení pro prostředek |
> | Akce | Microsoft. monitor zátěže byl/Operations/Read | Načte podporované operace. |

## <a name="next-steps"></a>Další kroky

- [Spárovat poskytovatele prostředků se službou](../azure-resource-manager/azure-services-resource-providers.md)
- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Předdefinované role pro prostředky Azure](built-in-roles.md)
