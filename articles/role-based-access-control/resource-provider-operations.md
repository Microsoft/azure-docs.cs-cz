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
ms.date: 07/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6ac3ef296509c6613a2a30821392c91648cc104
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868627"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager operace poskytovatele prostředků

Tento článek obsahuje seznam operací, které jsou k dispozici pro každého poskytovatele prostředků Azure Resource Manager. Tyto operace se dají použít ve [vlastních rolích](custom-roles.md) k zajištění podrobného [řízení přístupu na základě role (RBAC)](overview.md) k prostředkům v Azure. Řetězce operací mají následující formát:`{Company}.{ProviderName}/{resourceType}/{action}`

Operace poskytovatele prostředků se vždycky víjejí. Pokud chcete získat nejnovější operace, použijte seznam operací [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) nebo [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. AAD/domainServices/DELETE | Odstranit doménovou službu |
> | Action | Microsoft. AAD/domainServices/oucontainer/DELETE | Odstranit kontejner organizační jednotky |
> | Action | Microsoft. AAD/domainServices/oucontainer/Read | Čtení kontejnerů organizační jednotky |
> | Action | Microsoft. AAD/domainServices/oucontainer/Write | Zapsat kontejner organizační jednotky |
> | Action | Microsoft. AAD/domainServices/Read | Čtení doménových služeb |
> | Action | Microsoft.AAD/domainServices/replicaSets/delete | Odstranit lokalitu clusteru |
> | Action | Microsoft. AAD/domainServices/replicaSets/Read | Čtení lokality clusteru |
> | Action | Microsoft.AAD/domainServices/replicaSets/write | Zápis lokality clusteru |
> | Action | Microsoft. AAD/domainServices/Write | Zápis doménové služby |
> | Action | Microsoft. AAD/Locations/operationresults/Read |  |
> | Action | Microsoft. AAD/Operations/Ready |  |
> | Action | Microsoft. AAD/registrace/akce | Registrovat doménovou službu |
> | Action | Microsoft. AAD/zrušit registraci/akce | Zrušit registraci doménové služby |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | microsoft.aadiam/diagnosticsettings/delete | Odstranění nastavení diagnostiky |
> | Action | microsoft.aadiam/diagnosticsettings/read | Čtení nastavení diagnostiky |
> | Action | microsoft.aadiam/diagnosticsettings/write | Zápis nastavení diagnostiky |
> | Action | microsoft.aadiam/diagnosticsettingscategories/read | Čtení kategorií nastavení diagnostiky |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Addons/operations/read | Načte podporované operace RP. |
> | Action | Microsoft. addons/Register/Action | Zaregistrovat zadané předplatné pro Microsoft. addons |
> | Action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Zobrazí seznam aktuálních informací o plánu podpory pro zadané předplatné. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Odebere zadaný kanonický plán podpory. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/read | Získá zadaný kanonický stav plánu podpory. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/write | Přidá zadaný typ plánu kanonické podpory. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | Vytvořte novou doménovou strukturu pro tenanta. |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Načte všechny servery pro zadaný název služby. |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | Získá podrobnosti o výstrahách pro doménovou strukturu, jako je AlertId, datum vyvolání výstrahy, poslední zjištěná výstraha, popis výstrahy, Poslední aktualizace, úroveň výstrahy, stav výstrahy, odkazy na řešení potíží s výstrahami atd. |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | Získá konfiguraci služby pro doménovou strukturu. Příklad: název doménové struktury, funkční úroveň, role FSMO hlavního serveru pro pojmenovávání domén, role FSMO hlavního serveru schémat atd. |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | Odstraní službu a její servery spolu s daty o stavu. |
> | Action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Získá podrobnosti o doménách a lokalitách pro doménovou strukturu. Příklad: stav, aktivní výstrahy, vyřešené výstrahy, vlastnosti, jako je úroveň funkčnosti domény, doménová struktura, hlavní server infrastruktury, primární řadič domény, hlavní server RID atd.  |
> | Action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Získá nastavení předvolby pro uživatele pro doménovou strukturu.<br>Příklad: MetricCounterName, jako je ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Nastavení pro grafy uživatelského rozhraní atd. |
> | Action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Získá souhrn doménové struktury pro danou doménovou strukturu, jako je název doménové struktury, počet domén pod touto doménovou strukturou, počet lokalit a podrobností o lokalitách atd. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Získá seznam podporovaných metrik pro danou službu.<br>Například uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu za službu ADFS.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomainService.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do Azure AD pro službu ADSync. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | V případě služby toto rozhraní API získá informace o metrikách.<br>Pomocí tohoto rozhraní API můžete například získat informace týkající se: Uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny/s atd. pro službu ADFederation.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Toto rozhraní API získá seznam všech integrovaných ADDomainServices pro tenanta Premium. |
> | Action | Microsoft.ADHybridHealthService/addsservices/read | Získá podrobnosti o službě pro zadaný název služby. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Získá podrobnosti o replikaci pro všechny servery pro zadaný název služby. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Získá počet řadičů domény a jejich chyby replikace, pokud existují. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Načte Úplný seznam řadičů domény spolu s podrobnostmi o replikaci pro danou doménovou strukturu. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Přidejte instanci serveru do služby. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Během registrace serveru ADDomainService se toto rozhraní API volá, aby se získaly přihlašovací údaje pro nové servery. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Odstraní server pro danou službu a tenanta. |
> | Action | Microsoft.ADHybridHealthService/addsservices/write | Vytvoří nebo aktualizuje instanci ADDomainService pro tenanta. |
> | Action | Microsoft. ADHybridHealthService/Configuration/Action | Aktualizuje konfiguraci tenanta. |
> | Action | Microsoft. ADHybridHealthService/Configuration/Read | Přečte konfiguraci tenanta. |
> | Action | Microsoft.ADHybridHealthService/configuration/write | Vytvoří konfiguraci tenanta. |
> | Action | Microsoft. ADHybridHealthService/logs/obsah/číst | Načte obsah protokolů instalace a registrace agenta uložených v objektu BLOB. |
> | Action | Microsoft. ADHybridHealthService/logs/číst | Získá protokoly instalace a registrace agenta pro tenanta. |
> | Action | Microsoft.ADHybridHealthService/operations/read | Získá seznam operací podporovaných systémem. |
> | Action | Microsoft.ADHybridHealthService/register/action | Zaregistruje poskytovatele prostředků ADHybrid Health Service a povolí vytvoření prostředku ADHybrid Health Service. |
> | Action | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Získá seznam dostupných oblastí, které používá DevOps k podpoře incidentů zákazníků. |
> | Action | Microsoft. ADHybridHealthService/Reports/BadPassword/Read | Získá seznam špatných pokusů o zadání hesla pro všechny uživatele ve službě Active Directory služba FS (Federation Service). |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Získá identifikátor URI pro SAS objektu BLOB obsahující stav a konečný výsledek nově zařazené úlohy ve frontě pro frekvenci špatného uživatelského jména nebo hesla na základě ID uživatele a počtu identifikátorů hesel za den pro daného tenanta. |
> | Action | Microsoft. ADHybridHealthService/Reports/consentedtodevopstenants/Read | Získá seznam odsouhlasených klientů DevOps. Obvykle se používá pro zákaznickou podporu. |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | Načte hodnotu, která označuje, jestli je tenant DevOps nebo ne. |
> | Action | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Aktualizuje ID uživatele (objectID) pro vybraného tenanta operace vývoj. |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Získá vybrané nasazení pro daného tenanta. |
> | Action | Microsoft. ADHybridHealthService/Reports/tenantassigneddeployment/Read | Dané ID tenanta Získá umístění úložiště tenanta. |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Získá geografické umístění, ze kterého budou k datům přicházet. |
> | Action | Microsoft. ADHybridHealthService/Services/Action | Aktualizuje instanci služby v tenantovi. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrahy pro službu. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrahy pro službu. |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Vzhledem k názvu funkce ověří, jestli má služba všechno, co je potřeba k použití této funkce. |
> | Action | Microsoft.ADHybridHealthService/services/delete | Odstraní instanci služby v tenantovi. |
> | Action | Microsoft.ADHybridHealthService/services/exporterrors/read | Získá chyby exportu pro danou synchronizační službu. |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | Získá stav exportu pro danou službu. |
> | Action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Vrátí zpětnou vazbu k dané službě a serveru. |
> | Action | Microsoft. ADHybridHealthService/Services/metricmetadata/Read | Získá seznam podporovaných metrik pro danou službu.<br>Například uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny za sekundu za službu ADFS.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomainService.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do Azure AD pro službu ADSync. |
> | Action | Microsoft. ADHybridHealthService/Services/metriky/skupiny/průměr/čtení | Pro danou službu získá toto rozhraní API průměrnou metriku pro danou službu.<br>Pomocí tohoto rozhraní API můžete například získat informace týkající se: Uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny/s atd. pro službu ADFederation.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Action | Microsoft. ADHybridHealthService/Services/metriky/skupiny/číst | V případě služby toto rozhraní API získá informace o metrikách.<br>Pomocí tohoto rozhraní API můžete například získat informace týkající se: Uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny/s atd. pro službu ADFederation.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Action | Microsoft. ADHybridHealthService/Services/metriky/skupiny/Sum/Read | Rozhraní API pro danou službu získá agregované zobrazení metrik pro danou službu.<br>Pomocí tohoto rozhraní API můžete například získat informace týkající se: Uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny/s atd. pro službu ADFederation.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Action | Microsoft. ADHybridHealthService/Services/monitoringconfiguration/Write | Přidá nebo aktualizuje konfiguraci monitorování pro službu. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Získá konfigurace monitorování pro danou službu. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Přidejte nebo aktualizujte konfigurace monitorování pro službu. |
> | Action | Microsoft.ADHybridHealthService/services/premiumcheck/read | Toto rozhraní API získá seznam všech integrovaných služeb pro tenanta Premium. |
> | Action | Microsoft.ADHybridHealthService/services/read | Přečte instance služby v tenantovi. |
> | Action | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Načte všechny identifikátory URI pro sestavu rizikových IP adres za posledních 7 dnů. |
> | Action | Microsoft. ADHybridHealthService/Services/Reports/Details/Read | Získá sestavu prvních 50 uživatelů s chybami špatného hesla z posledních 7 dnů. |
> | Action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Generuje sestavu rizikových IP adres a vrátí identifikátor URI, na který odkazuje. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/Action | Vytvoří instanci serveru ve službě. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Přečte výstrahy pro server. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Během registrace serveru se toto rozhraní API volá, aby se získaly přihlašovací údaje pro nové servery. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Pro daný server toto rozhraní API získá seznam DataType, které jsou odesílány servery a nejnovější čas pro každé nahrání. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/delete | Odstraní instanci serveru ve službě. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/Read | Získá podrobnosti o chybě exportu synchronizace pro danou synchronizační službu. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/metriky/skupiny/číst | V případě služby toto rozhraní API získá informace o metrikách.<br>Pomocí tohoto rozhraní API můžete například získat informace týkající se: Uzamčení účtu extranetu, celkový počet neúspěšných požadavků, nezpracované žádosti o tokeny (proxy), žádosti o tokeny/s atd. pro službu ADFederation.<br>Ověřování protokolem NTLM/s – úspěšné vazby protokolu LDAP/s, čas vazby protokolu LDAP, aktivní vlákna protokolu LDAP, ověřování protokolem Kerberos/s, ATQ vlákna celkem atd. ADDomain služba.<br>Latence profilu spuštění, navázaná připojení TCP, soukromé bajty agenta Insights, export statistik do služby Azure AD pro synchronizační službu. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/metriky/číst | Získá seznam konektorů a názvy profilů spuštění pro daného člena služby a služby. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/read | Přečte instanci serveru ve službě. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Načte konfiguraci služby pro daného tenanta. |
> | Action | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/Read | Získá stav seznamu povolených funkcí pro daného tenanta. |
> | Action | Microsoft.ADHybridHealthService/services/write | Vytvoří instanci služby v tenantovi. |
> | Action | Microsoft.ADHybridHealthService/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků ADHybrid Health Service. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Advisor/Configurations/Ready | Získat konfigurace |
> | Action | Microsoft. Advisor/Configurations/Write | Vytvoří nebo aktualizuje konfiguraci. |
> | Action | Microsoft. Advisor/generateRecommendations/Action | Vygeneruje doporučení. |
> | Action | Microsoft. Advisor/generateRecommendations/Read | Získá stav doporučení pro generování doporučení. |
> | Action | Microsoft. Advisor/metadata/číst | Získat metadata |
> | Action | Microsoft. Advisor/Operations/Ready | Načte operace pro Microsoft Advisor. |
> | Action | Microsoft. Advisor/doporučení/k dispozici/akce | Nové doporučení je dostupné v Microsoft Advisoru. |
> | Action | Microsoft. Advisor/doporučení/číst | Čte doporučení |
> | Action | Microsoft. Advisor/doporučení/potlačení/odstranění | Odstraní potlačení. |
> | Action | Microsoft. Advisor/doporučení/potlačení/čtení | Získá potlačení. |
> | Action | Microsoft. Advisor/doporučení/potlačení/zápis | Vytvoří nebo aktualizuje potlačení. |
> | Action | Microsoft. Advisor/registr/Action | Zaregistruje předplatné pro Microsoft Advisor. |
> | Action | Microsoft. Advisor/potlačit/odstranit | Odstraní potlačení. |
> | Action | Microsoft. Advisor/potlačení/čtení | Získá potlačení. |
> | Action | Microsoft. Advisor/potlačení/zápis | Vytvoří nebo aktualizuje potlačení. |
> | Action | Microsoft. Advisor/zrušit registraci/akce | Zruší registraci předplatného služby Microsoft Advisor. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. AlertsManagement/actionRules/DELETE | Odstraní pravidlo akce v daném předplatném. |
> | Action | Microsoft. AlertsManagement/actionRules/Read | Získejte všechna pravidla akcí pro vstupní filtry. |
> | Action | Microsoft.AlertsManagement/actionRules/write | Vytvořit nebo aktualizovat pravidlo akce v daném předplatném |
> | Action | Microsoft. AlertsManagement/Alerts/changestate/Action | Změňte stav výstrahy. |
> | Action | Microsoft. AlertsManagement, výstrahy/Diagnostika/číst | Získat veškerou diagnostiku výstrahy |
> | Action | Microsoft. AlertsManagement/Alerts/History/číst | Získat historii výstrahy |
> | Action | Microsoft. AlertsManagement/výstrahy/čtení | Získá všechny výstrahy pro vstupní filtry. |
> | Action | Microsoft.AlertsManagement/alertsList/read | Získat všechny výstrahy pro vstupní filtry v rámci předplatných |
> | Action | Microsoft. AlertsManagement/alertsMetaData/Read | Pro vstupní parametr získáte metadata metadat výstrahy. |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | Získání souhrnu výstrah |
> | Action | Microsoft. AlertsManagement/alertsSummaryList/Read | Získat souhrn výstrah v rámci předplatných |
> | Action | Microsoft.AlertsManagement/Operations/read | Přečte poskytnuté operace. |
> | Action | Microsoft.AlertsManagement/register/action | Zaregistruje předplatné pro správu výstrah společnosti Microsoft. |
> | Action | Microsoft. AlertsManagement/smartDetectorAlertRules/DELETE | Odstranit pravidlo výstrahy inteligentního detektoru v daném předplatném |
> | Action | Microsoft. AlertsManagement/smartDetectorAlertRules/Read | Získat všechna pravidla upozornění inteligentního detektoru pro vstupní filtry |
> | Action | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Vytvoří nebo aktualizuje pravidlo výstrahy inteligentního detektoru v daném předplatném. |
> | Action | Microsoft. AlertsManagement/smartGroups/changestate/Action | Změna stavu inteligentní skupiny |
> | Action | Microsoft. AlertsManagement/smartGroups/History/Read | Získat historii inteligentní skupiny |
> | Action | Microsoft.AlertsManagement/smartGroups/read | Získání všech inteligentních skupin pro vstupní filtry |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Kontroluje, zda je zadaný název Analysis Server platný a nepoužívá se. |
> | Action | Microsoft. AnalysisServices/Locations/operationresults/Read | Načte informace o výsledku zadané operace. |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | Načte informace o stavu zadané operace. |
> | Action | Microsoft. AnalysisServices/Operations/Read | Načte informace o operacích. |
> | Action | Microsoft. AnalysisServices/Register/Action | Zaregistruje poskytovatele prostředků Analysis Services. |
> | Action | Microsoft.AnalysisServices/servers/delete | Odstraní Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Vypíše stav brány přidružené k serveru. |
> | Action | Microsoft. AnalysisServices/servery/číst | Načte informace o zadaném Analysis Server. |
> | Action | Microsoft. AnalysisServices/servery/pokračovat/akce | Obnoví Analysis Server. |
> | Action | Microsoft. AnalysisServices/servery/SKU/číst | Načíst dostupné informace o SKU pro server |
> | Action | Microsoft. AnalysisServices/servery/pozastavit/akce | Pozastaví Analysis Server. |
> | Action | Microsoft. AnalysisServices/servery/Write | Vytvoří nebo aktualizuje zadaný Analysis Server. |
> | Action | Microsoft. AnalysisServices/SKU/číst | Načte informace o SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ApiManagement/checkNameAvailability/Read | Zkontroluje, jestli je zadaný název služby k dispozici. |
> | Action | Microsoft.ApiManagement/operations/read | Načte všechny operace rozhraní API, které jsou k dispozici pro prostředek Microsoft. ApiManagement. |
> | Action | Microsoft. ApiManagement/Register/Action | Registrace předplatného pro poskytovatele prostředků Microsoft. ApiManagement |
> | Action | Microsoft. ApiManagement/Reports/Read | Získejte sestavy agregované podle časových období, geografické oblasti, vývojářů, produktů, rozhraní API, operací, předplatných a byRequest. |
> | Action | Microsoft. ApiManagement/Service/API/DELETE | Odstraní zadané rozhraní API instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/API/Diagnostics/DELETE | Odstraní zadanou diagnostiku z rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Diagnostics/Read | Vypíše veškerou diagnostiku rozhraní API. nebo získá podrobnosti diagnostiky pro rozhraní API zadané jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/API/Diagnostics/Write | Vytvoří novou diagnostiku pro rozhraní API nebo aktualizuje stávající. nebo aktualizuje podrobnosti diagnostiky pro rozhraní API zadané jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/Attachments/Attachments/DELETE | Odstraní zadaný komentář z problému. |
> | Action | Microsoft. ApiManagement/Service/rozhraní API/problémy/přílohy/číst | Vypíše všechny přílohy problému přidruženého k zadanému rozhraní API. nebo získá podrobnosti o příloze problému pro rozhraní API zadané jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/Attachments/Write | Vytvoří novou přílohu pro daný problém v rozhraní API nebo aktualizuje stávající. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/Comments/DELETE | Odstraní zadaný komentář z problému. |
> | Action | Microsoft. ApiManagement/Service/API/problémy/komentáře/čtení | Zobrazí seznam všech komentářů k problému souvisejícímu se zadaným rozhraním API. nebo získá podrobnosti poznámky k problému pro rozhraní API zadané jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/Comments/Write | Vytvoří nový komentář k problému v rozhraní API nebo aktualizuje stávající. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/DELETE | Odstraní zadaný problém z rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/Read | Zobrazí seznam všech problémů přidružených k zadanému rozhraní API. nebo získá podrobnosti problému pro rozhraní API zadané jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/API/probléms/Write | Vytvoří nový problém pro rozhraní API nebo aktualizuje stávající. nebo aktualizuje stávající problém pro rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/DELETE | Odstraní zadanou operaci v rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/policies/DELETE | Odstraní konfiguraci zásad na operaci rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/policies/Ready | Získejte seznam konfigurace zásad na úrovni operace rozhraní API. nebo si na úrovni operace rozhraní API získejte konfiguraci zásad. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/policies/Write | Vytvoří nebo aktualizuje konfiguraci zásad pro úroveň operace rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Policy/DELETE | Odstranit konfiguraci zásad na úrovni provozu |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Policy/Read | Získat konfiguraci zásad na úrovni provozu |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Policy/Write | Vytvořit konfiguraci zásad na úrovni provozu |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Read | Vypíše kolekci operací pro zadané rozhraní API. nebo získá podrobné informace o operaci rozhraní API zadané jeho identifikátorem. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odpojte značku od operace. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Tags/Read | Zobrazí seznam všech značek přidružených k operaci. nebo získat značku přidruženou k operaci. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Tags/Write | Přiřaďte k operaci značku. |
> | Action | Microsoft. ApiManagement/Service/API/Operations/Write | Vytvoří novou operaci v rozhraní API nebo aktualizuje stávající. nebo aktualizuje podrobnosti operace v rozhraní API určeném identifikátorem. |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | Vypíše kolekci operací přidružených k značkám. |
> | Action | Microsoft. ApiManagement/Service/API/policies/DELETE | Odstraní konfiguraci zásad v rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/policies/Read | Získejte konfiguraci zásad na úrovni rozhraní API. nebo získejte konfiguraci zásad na úrovni rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/policies/Write | Vytvoří nebo aktualizuje konfiguraci zásad pro rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Policy/DELETE | Odstranění konfigurace zásad na úrovni rozhraní API |
> | Action | Microsoft. ApiManagement/Service/API/Policy/Read | Získat konfiguraci zásad na úrovni rozhraní API |
> | Action | Microsoft. ApiManagement/Service/API/Policy/Write | Vytvoření konfigurace zásad na úrovni rozhraní API |
> | Action | Microsoft. ApiManagement/Service/API/Products/Read | Zobrazí seznam všech produktů, které jsou součástí rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Read | Vypíše všechna rozhraní API instance služby API Management. nebo získá podrobnosti rozhraní API určeného identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/API/releases/DELETE | Odebere všechny verze rozhraní API nebo odstraní určenou verzi v rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/releases/Read | Zobrazí seznam všech verzí rozhraní API.<br>Při vytváření Revize rozhraní API se vytvoří vydaná verze rozhraní API.<br>Verze se používají také k vrácení zpět k předchozím revizím.<br>Výsledky budou stránkovaná a můžou být omezené pomocí parametrů $top a $skip.<br>nebo vrátí podrobnosti verze rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Release/Write | Vytvoří novou verzi rozhraní API. nebo aktualizuje podrobnosti vydání rozhraní API určeného identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/rozhraní API/revize/odstranit | Odebere všechny revize rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/revize/číst | Zobrazí seznam všech revizí rozhraní API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/delete | Odstraní konfiguraci schématu v rozhraní API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | Získejte konfiguraci schématu na úrovni rozhraní API. nebo získáte konfiguraci schématu na úrovni rozhraní API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | Vytvoří nebo aktualizuje konfiguraci schématu pro rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/tagDescriptions/DELETE | Odstraní popis značky pro rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/tagDescriptions/Read | Zobrazí seznam všech popisů značek v oboru rozhraní API. Model podobný nástroji Swagger – tagDescription je definovaný na úrovni rozhraní API, ale značka může být přiřazená operacím nebo získání popisu značky v oboru rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/tagDescriptions/Write | Vytvoří nebo aktualizuje popis značky v oboru rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Tags/DELETE | Odpojte značku od rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Tags/Read | Zobrazí seznam všech značek přidružených k rozhraní API. nebo získat značku přidruženou k rozhraní API. |
> | Action | Microsoft. ApiManagement/Service/API/Tags/Write | Přiřaďte k rozhraní API značku. |
> | Action | Microsoft. ApiManagement/Service/API/Write | Vytvoří nové nebo aktualizuje existující zadané rozhraní API instance služby API Management. nebo aktualizuje zadané rozhraní API instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/apisByTags/Read | Obsahuje kolekci rozhraní API přidružených k značkám. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/delete | Odstraní konkrétní sadu verzí rozhraní API. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/read | Vypíše kolekci sad verzí rozhraní API v zadané instanci služby. nebo získá Podrobnosti sady verzí rozhraní API určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/apiVersionSets/verze/číst | Získat seznam entit verze |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/write | Vytvoří nebo aktualizuje sadu verzí rozhraní API. nebo aktualizuje podrobnosti rozhraní API VersionSet určeného identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/applynetworkconfigurationupdates/Action | Aktualizuje prostředky Microsoft. ApiManagement spuštěné v Virtual Network k výběru aktualizovaných nastavení sítě. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/delete | Odstraní konkrétní instanci autorizačního serveru. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/read | Vypíše kolekci autorizačních serverů definovaných v rámci instance služby. nebo získá podrobnosti autorizačního serveru určeného identifikátorem. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/write | Vytvoří nový autorizační Server nebo aktualizuje stávající autorizační Server. nebo aktualizuje podrobnosti autorizačního serveru určeného identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/back-end/DELETE | Odstraní zadaný back-end. |
> | Action | Microsoft. ApiManagement/Service/back-endy/číst | Vypíše kolekci back-endy v zadané instanci služby. nebo získá podrobnosti o back-endu zadaném jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/back-endy/znovu připojit/akce | Oznamuje proxy serveru APIM, aby po uplynutí zadaného časového limitu vytvořil nové připojení k back-endu. Pokud nebyl zadán žádný časový limit, bude použit časový limit 2 minut. |
> | Action | Microsoft. ApiManagement/Service/back-end/Write | Vytvoří nebo aktualizuje back-end. nebo aktualizuje stávající back-end. |
> | Action | Microsoft. ApiManagement/Service/Backup/Action | Služba Backup API Management do zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | Action | Microsoft.ApiManagement/service/caches/delete | Odstraní konkrétní mezipaměť. |
> | Action | Microsoft. ApiManagement/Service/caches/Read | Vypíše kolekci všech externích mezipamětí v zadané instanci služby. nebo získá podrobnosti mezipaměti určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/caches/Write | Vytvoří nebo aktualizuje externí mezipaměť, která se má použít v instanci služby API Management. nebo aktualizuje podrobnosti mezipaměti určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Certificates/DELETE | Odstraní konkrétní certifikát. |
> | Action | Microsoft. ApiManagement/Service/Certificates/Read | Vypíše kolekci všech certifikátů v zadané instanci služby. nebo získá podrobnosti certifikátu určeného jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Certificates/Write | Vytvoří nebo aktualizuje certifikát používaný k ověřování pomocí back-endu. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Odebere zadanou položku obsahu. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Vrátí seznam položek obsahu nebo vrátí podrobnosti o položce obsahu. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Vytvoří novou položku obsahu nebo aktualizuje zadanou položku obsahu. |
> | Action | Microsoft.ApiManagement/service/contentTypes/read | Vrátí seznam typů obsahu. |
> | Action | Microsoft.ApiManagement/service/delete | Odstranit instanci služby API Management |
> | Action | Microsoft. ApiManagement/Service/Diagnostics/DELETE | Odstraní zadanou diagnostiku. |
> | Action | Microsoft. ApiManagement/Service/Diagnostics/Read | Zobrazí všechny diagnostiky instance služby API Management. nebo získá podrobnosti diagnostiky určené jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Diagnostics/Write | Vytvoří novou diagnostiku nebo aktualizuje stávající. nebo aktualizuje podrobnosti diagnostiky určeného identifikátorem. |
> | Action | Microsoft. ApiManagement/služba/brány/akce | Načte konfiguraci brány. nebo aktualizuje prezenční signál brány. |
> | Action | Microsoft. ApiManagement/Service/gateways/DELETE | Odstraní konkrétní bránu. |
> | Action | Microsoft. ApiManagement/Service/gateways/Key/Action | Načte klíče brány. |
> | Action | Microsoft. ApiManagement/služba/brány/číst | Vypíše kolekci bran zaregistrovaných u instance služby. nebo získá podrobnosti o bráně určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/gateways/regeneratePrimaryKey/Action | Znovu vygeneruje klíč primární brány invalidationg všechny tokeny, které se s ním vytvořily. |
> | Action | Microsoft. ApiManagement/Service/gateways/regenerateSecondaryKey/Action | Znovu vygeneruje klíč sekundární brány invalidationg všechny tokeny, které se s ním vytvořily. |
> | Action | Microsoft. ApiManagement/Service/gateways/token/Action | Získá autorizační token sdíleného přístupu pro bránu. |
> | Action | Microsoft. ApiManagement/Service/gateways/Write | Vytvoří nebo aktualizuje bránu, která se má použít v instanci API managementu. nebo aktualizuje podrobnosti o bráně určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/getssotoken/Action | Získá token jednotného přihlašování, který se dá použít k přihlášení k portálu služby API Management starší verze jako správce. |
> | Action | Microsoft. ApiManagement/Service/Groups/DELETE | Odstraní konkrétní skupinu instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/Groups/Read | Vypíše kolekci skupin definovaných v rámci instance služby. nebo získá podrobnosti skupiny určené identifikátorem. |
> | Action | Microsoft.ApiManagement/service/groups/users/delete | Odebere stávajícího uživatele z existující skupiny. |
> | Action | Microsoft. ApiManagement/Service/Groups/Users/Read | Zobrazuje kolekci uživatelských entit přidružených ke skupině. |
> | Action | Microsoft. ApiManagement/Service/Groups/Users/Write | Přidat existujícího uživatele do existující skupiny |
> | Action | Microsoft. ApiManagement/Service/Groups/Write | Vytvoří nebo aktualizuje skupinu. nebo aktualizuje podrobnosti skupiny určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/identityProviders/DELETE | Odstraní zadanou konfiguraci zprostředkovatele identity. |
> | Action | Microsoft. ApiManagement/Service/identityProviders/Read | Vypíše kolekci zprostředkovatele identity nakonfigurovanou v zadané instanci služby. nebo získá podrobné informace o konfiguraci zprostředkovatele identity nakonfigurovaného v zadané instanci služby. |
> | Action | Microsoft. ApiManagement/Service/identityProviders/Write | Vytvoří nebo aktualizuje konfiguraci IdentityProvider. nebo aktualizuje existující konfiguraci IdentityProvider. |
> | Action | Microsoft. ApiManagement/Service/problémy/číst | Vypíše kolekci problémů v zadané instanci služby. nebo získá podrobnosti o API Management problému. |
> | Action | Microsoft. ApiManagement/Service/Locations/NetworkStatus/Read | Získá stav přístupu k síti prostředků, na kterých závisí služba v umístění. |
> | Action | Microsoft. ApiManagement/Service/protokolovacích/DELETE | Odstraní zadaný protokolovací nástroj. |
> | Action | Microsoft. ApiManagement/Service/protokolovací nástroje/číst | Vypíše kolekci protokolovacích nástrojů v zadané instanci služby. nebo získá podrobnosti protokolovacího nástroje určeného jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/protokolovacích/Write | Vytvoří nebo aktualizuje protokolovací nástroj. nebo aktualizuje existující protokolovací nástroj. |
> | Action | Microsoft. ApiManagement/Service/managedeployments/Action | Změna SKU/jednotek, přidání nebo odebrání regionálních nasazení API Management služby |
> | Action | Microsoft. ApiManagement/Service/NetworkStatus/Read | Získá stav přístupu k síti prostředků, na kterých je služba závislá. |
> | Action | Microsoft. ApiManagement/Service/Notifications/Action | Odešle oznámení zadanému uživateli. |
> | Action | Microsoft. ApiManagement/Service/Notifications/Read | Zobrazí seznam vlastností definovaných v rámci instance služby. nebo získá podrobnosti oznámení určeného identifikátorem. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Odebere e-mail ze seznamu oznámení. |
> | Action | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Read | Získá seznam e-mailů příjemce oznámení přihlášených k odběru oznámení. |
> | Action | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Write | Přidá e-mailovou adresu do seznamu příjemců oznámení. |
> | Action | Microsoft. ApiManagement/Service/Notifications/recipientUsers/DELETE | Odebere uživatele API Management ze seznamu oznámení. |
> | Action | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Read | Získá seznam uživatele příjemce oznámení, který se přihlásí k odběru oznámení. |
> | Action | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Write | Přidá uživatele API Management do seznamu příjemců oznámení. |
> | Action | Microsoft. ApiManagement/Service/Notifications/Write | Vytvoří nebo aktualizuje oznámení vydavatele API Management. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/delete | Odstraní konkrétního poskytovatele OpenID Connect instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/openidConnectProviders/Read | Seznam všech poskytovatelů OpenId Connect. nebo získá konkrétního poskytovatele OpenID Connect. |
> | Action | Microsoft. ApiManagement/Service/openidConnectProviders/Write | Vytvoří nebo aktualizuje poskytovatele OpenID Connect. nebo aktualizuje konkrétního poskytovatele OpenID Connect. |
> | Action | Microsoft. ApiManagement/Service/operationresults/Read | Získá aktuální stav dlouho běžící operace. |
> | Action | Microsoft. ApiManagement/Service/policies/DELETE | Odstraní konfiguraci globálních zásad služby API Management. |
> | Action | Microsoft. ApiManagement/Service/policies/Read | Zobrazí seznam všech definic globálních zásad služby API Management. nebo si přečtěte definici globální zásady služby API Management. |
> | Action | Microsoft. ApiManagement/Service/policies/Write | Vytvoří nebo aktualizuje konfiguraci globálních zásad služby API Management. |
> | Action | Microsoft. ApiManagement/Service/Policy/DELETE | Odstranění konfigurace zásad na úrovni tenanta |
> | Action | Microsoft. ApiManagement/Service/Policy/Read | Získat konfiguraci zásad na úrovni tenanta |
> | Action | Microsoft. ApiManagement/Service/Policy/Write | Vytvoření konfigurace zásad na úrovni tenanta |
> | Action | Microsoft. ApiManagement/Service/policySnippets/Read | Vypíše všechny fragmenty zásad. |
> | Action | Microsoft. ApiManagement/Service/portalsettings/Read | Získejte nastavení pro přihlášení k portálu nebo získejte nastavení pro registraci portálu nebo získejte nastavení pro delegování portálu. |
> | Action | Microsoft. ApiManagement/Service/portalsettings/Write | Aktualizujte nastavení přihlášení. nebo vytvořte nebo aktualizujte nastavení přihlášení. nebo aktualizovat nastavení registrace nebo aktualizovat nastavení registrace nebo aktualizovat nastavení delegování. nebo vytvořte nebo aktualizujte nastavení delegování. |
> | Action | Microsoft. ApiManagement/Service/Products/API/DELETE | Odstraní zadané rozhraní API ze zadaného produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/API/Read | Vypíše kolekci rozhraní API přidružených k produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/API/Write | Přidá rozhraní API k zadanému produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/DELETE | Odstranit produkt |
> | Action | Microsoft. ApiManagement/Service/Products/Groups/DELETE | Odstraní přidružení mezi určenou skupinou a produktem. |
> | Action | Microsoft. ApiManagement/Service/Products/Groups/Read | Zobrazuje kolekci skupin pro vývojáře přidružených k zadanému produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/Groups/Write | Přidá přidružení mezi zadanou skupinou vývojářů se zadaným produktem. |
> | Action | Microsoft. ApiManagement/Service/Products/policies/DELETE | Odstraní konfiguraci zásad v produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/policies/Read | Získejte konfiguraci zásad na úrovni produktu. nebo získejte konfiguraci zásad na úrovni produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/policies/Write | Vytvoří nebo aktualizuje konfiguraci zásad pro daný produkt. |
> | Action | Microsoft. ApiManagement/Service/Products/Policy/DELETE | Odstranění konfigurace zásad na úrovni produktu |
> | Action | Microsoft. ApiManagement/Service/Products/Policy/Read | Získat konfiguraci zásad na úrovni produktu |
> | Action | Microsoft. ApiManagement/Service/Products/Policy/Write | Vytvoření konfigurace zásad na úrovni produktu |
> | Action | Microsoft. ApiManagement/Service/Products/Read | Vypíše kolekci produktů v zadané instanci služby. nebo získá podrobnosti o produktu určeném jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Products/Subscriptions/Read | Zobrazí seznam předplatných určených pro daný produkt. |
> | Action | Microsoft. ApiManagement/Service/Products/Tags/DELETE | Odpojte značku od produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/Tags/Read | Zobrazí seznam všech značek přidružených k produktu. nebo získat značku přidruženou k produktu. |
> | Action | Microsoft. ApiManagement/Service/Products/Tags/Write | Přiřaďte k produktu značku. |
> | Action | Microsoft. ApiManagement/Service/Products/Write | Vytvoří nebo aktualizuje produkt. nebo aktualizuje existující podrobnosti o produktu. |
> | Action | Microsoft. ApiManagement/Service/productsByTags/Read | Vypíše kolekci produktů přidružených k značkám. |
> | Action | Microsoft. ApiManagement/Service/Properties/DELETE | Odstraní konkrétní vlastnost z instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/Properties/Read | Zobrazí seznam vlastností definovaných v rámci instance služby. nebo získá podrobnosti vlastnosti určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Properties/Write | Vytvoří nebo aktualizuje vlastnost. nebo aktualizuje konkrétní vlastnost. |
> | Action | Microsoft. ApiManagement/Service/kvóty/období/čtení | Hodnota čítače získání kvóty pro období |
> | Action | Microsoft. ApiManagement/Service/kvóty/období/zápis | Nastavit čítač kvót aktuální hodnota |
> | Action | Microsoft. ApiManagement/Service/kvóty/číst | Získat hodnoty pro kvótu |
> | Action | Microsoft. ApiManagement/Service/kvóty/Write | Nastavit čítač kvót aktuální hodnota |
> | Action | Microsoft. ApiManagement/Service/Read | Čtení metadat pro instanci služby API Management Service |
> | Action | Microsoft. ApiManagement/Service/Regions/Read | Zobrazí seznam všech oblastí Azure, ve kterých služba existuje. |
> | Action | Microsoft. ApiManagement/Service/Reports/Read | Získání sestavy agregované podle časových období nebo získání sestavy agregované podle geografické oblasti nebo získání sestavy agregované vývojáři<br>nebo získat sestavu agregovanou podle produktů.<br>nebo získat sestavu agregovanou pomocí rozhraní API nebo získat sestavu agregovanou podle operací nebo získat sestavu agregovanou podle předplatného.<br>nebo získávat požadavky na data vytváření sestav |
> | Action | Microsoft. ApiManagement/Service/Restore/Action | Obnovení služby API Management ze zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | Action | Microsoft. ApiManagement/Service/Subscriptions/DELETE | Odstraní zadané předplatné. |
> | Action | Microsoft. ApiManagement/Service/Subscriptions/Read | Zobrazí seznam všech předplatných instance služby API Management. nebo získá zadanou entitu předplatného. |
> | Action | Microsoft. ApiManagement/Service/Subscriptions/regeneratePrimaryKey/Action | Znovu vygeneruje primární klíč stávajícího předplatného instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/Subscriptions/regenerateSecondaryKey/Action | Obnoví sekundární klíč stávajícího předplatného instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/Subscriptions/Write | Vytvoří nebo aktualizuje odběr zadaného uživatele na zadaný produkt. nebo aktualizuje podrobnosti předplatného, které určuje jeho identifikátor. |
> | Action | Microsoft. ApiManagement/Service/tagResources/Read | Vypíše kolekci prostředků přidružených k značkám. |
> | Action | Microsoft. ApiManagement/Service/Tags/DELETE | Odstraní konkrétní značku instance služby API Management. |
> | Action | Microsoft. ApiManagement/Service/Tags/Read | Obsahuje kolekci značek definovaných v rámci instance služby. nebo získá podrobnosti značky určené identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Tags/Write | Vytvoří značku. nebo aktualizuje podrobnosti značky určené identifikátorem. |
> | Action | Microsoft.ApiManagement/service/templates/delete | Resetovat výchozí šablonu e-mailu API Management |
> | Action | Microsoft. ApiManagement/Service/Templates/Read | Získá všechny e-mailové šablony nebo získá API Management podrobnosti e-mailové šablony. |
> | Action | Microsoft. ApiManagement/Service/Templates/Write | Vytvořit nebo aktualizovat šablonu e-mailu API Management nebo aktualizace API Management e-mailové šablony |
> | Action | Microsoft. ApiManagement/Service/tenant/DELETE | Odebrat konfiguraci zásad pro tenanta |
> | Action | Microsoft. ApiManagement/Service/tenant/Deploy/Action | Spustí úlohu nasazení, která aplikuje změny ze zadané větve Git do konfigurace v databázi. |
> | Action | Microsoft. ApiManagement/Service/tenant/operationResults/Read | Získat seznam výsledků operace nebo získat výsledek konkrétní operace |
> | Action | Microsoft. ApiManagement/Service/tenant/Read | Získejte definici globální zásady služby API Management. nebo získat podrobnosti o přístupu klienta |
> | Action | Microsoft. ApiManagement/Service/tenant/regeneratePrimaryKey/Action | Znovu vygenerovat primární přístupový klíč |
> | Action | Microsoft. ApiManagement/Service/tenant/regenerateSecondaryKey/Action | Znovu vygenerovat sekundární přístupový klíč |
> | Action | Microsoft. ApiManagement/Service/tenant/Save/Action | Vytvoří potvrzení se snímkem konfigurace do zadané větve v úložišti. |
> | Action | Microsoft. ApiManagement/Service/tenant/syncState/Read | Získat stav poslední synchronizace Gitu |
> | Action | Microsoft. ApiManagement/Service/tenant/Validate/Action | Ověří změny ze zadané větve Git. |
> | Action | Microsoft. ApiManagement/Service/tenant/Write | Nastavte konfiguraci zásad pro klienta nebo aktualizujte informace o přístupu klienta. |
> | Action | Microsoft. ApiManagement/Service/updatecertificate/Action | Odeslat certifikát SSL pro službu API Management |
> | Action | Microsoft. ApiManagement/Service/updatehostname/Action | Instalace, aktualizace nebo odebrání názvů vlastních domén pro službu API Management |
> | Action | Microsoft. ApiManagement/Service/uživatelé/akce | Registrace nového uživatele |
> | Action | Microsoft. ApiManagement/Service/Users/potvrzení/odeslání/akce | Odešle potvrzení |
> | Action | Microsoft.ApiManagement/service/users/delete | Odstraní konkrétního uživatele. |
> | Action | Microsoft. ApiManagement/Service/Users/generateSsoUrl/Action | Načte adresu URL pro přesměrování obsahující ověřovací token pro podepsání daného uživatele na portálu pro vývojáře. |
> | Action | Microsoft. ApiManagement/Service/Users/Groups/Read | Zobrazí seznam všech skupin uživatelů. |
> | Action | Microsoft. ApiManagement/Service/Users/identity/číst | Seznam všech identit uživatelů |
> | Action | Microsoft. ApiManagement/Service/Users/Keys/číst | Získání klíčů přidružených k uživateli |
> | Action | Microsoft. ApiManagement/Service/Users/číst | Vypíše kolekci registrovaných uživatelů v zadané instanci služby. nebo získá podrobnosti uživatele zadaného jeho identifikátorem. |
> | Action | Microsoft. ApiManagement/Service/Users/Subscriptions/Read | Vypíše kolekci předplatných určeného uživatele. |
> | Action | Microsoft. ApiManagement/Service/Users/token/Action | Získá autorizační token sdíleného přístupu pro uživatele. |
> | Action | Microsoft. ApiManagement/Service/Users/Write | Vytvoří nebo aktualizuje uživatele. nebo aktualizuje podrobnosti uživatele zadaného jeho identifikátorem. |
> | Action | Microsoft.ApiManagement/service/write | Vytvoří novou instanci služby API Management. |
> | Action | Microsoft.ApiManagement/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Authorization/classicAdministrators/DELETE | Odebere správce z předplatného. |
> | Action | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Získá stav operace Správce předplatného. |
> | Action | Microsoft. Authorization/classicAdministrators/Read | Přečte Správce předplatného. |
> | Action | Microsoft. Authorization/classicAdministrators/Write | Umožňuje přidat nebo upravit správce k předplatnému. |
> | Action | Microsoft. Authorization/denyAssignments/DELETE | Odstraní přiřazení odepřít v zadaném oboru. |
> | Action | Microsoft. Authorization/denyAssignments/Read | Získat informace o přiřazení zamítnutí. |
> | Action | Microsoft. Authorization/denyAssignments/Write | Vytvoří přiřazení odepřít v zadaném oboru. |
> | Action | Microsoft. Authorization/elevateAccess/Action | Udělí volajícímu přístup správce přístupu v oboru tenanta. |
> | Action | Microsoft. Authorization/zámky/DELETE | Odstraní zámky v zadaném oboru. |
> | Action | Microsoft. Authorization/zámky/čtení | Načte zámky v zadaném oboru. |
> | Action | Microsoft. Authorization/zámky/Write | Přidá zámky v zadaném oboru. |
> | Action | Microsoft. Authorization/Operations/Read | Získá seznam operací. |
> | Action | Microsoft. Authorization/oprávnění/čtení | Zobrazí seznam všech oprávnění, která volající má v daném oboru. |
> | Action | Microsoft. Authorization/policyAssignments/DELETE | Odstraní přiřazení zásady v zadaném oboru. |
> | Action | Microsoft. Authorization/policyAssignments/Read | Získejte informace o přiřazení zásady. |
> | Action | Microsoft. Authorization/policyAssignments/Write | Vytvoří přiřazení zásady v zadaném oboru. |
> | Action | Microsoft. Authorization/policyDefinitions/DELETE | Odstraní definici zásady. |
> | Action | Microsoft. Authorization/policyDefinitions/Read | Získat informace o definici zásady. |
> | Action | Microsoft. Authorization/policyDefinitions/Write | Vytvořte vlastní definici zásad. |
> | Action | Microsoft. Authorization/policySetDefinitions/DELETE | Odstraní definici sady zásad. |
> | Action | Microsoft. Authorization/policySetDefinitions/Read | Získejte informace o definici sady zásad. |
> | Action | Microsoft. Authorization/policySetDefinitions/Write | Vytvoření vlastní definice sady zásad |
> | Action | Microsoft. Authorization/providerOperations/Read | Získá operace pro všechny poskytovatele prostředků, které se dají použít v definicích rolí. |
> | Action | Microsoft.Authorization/roleAssignments/delete | Odstraní přiřazení role v zadaném oboru. |
> | Action | Microsoft. Authorization/roleAssignments/Read | Získejte informace o přiřazení role. |
> | Action | Microsoft.Authorization/roleAssignments/write | Vytvoří přiřazení role v zadaném oboru. |
> | Action | Microsoft. Authorization/roleDefinitions/DELETE | Odstraňte zadanou definici vlastní role. |
> | Action | Microsoft. Authorization/roleDefinitions/Read | Získejte informace o definici role. |
> | Action | Microsoft. Authorization/roleDefinitions/Write | Vytvořte nebo aktualizujte definici vlastní role se zadanými oprávněními a přiřazovatelné obory. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Přečtěte si informace o registraci Azure Automation DSC. |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapíše požadavek na opětovné vygenerování Azure Automation klíčů DSC. |
> | Action | Microsoft. Automation/automationAccounts/Certificates/DELETE | Odstraní prostředek certifikátu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Certificates/GetCount/Action | Přečte počet certifikátů. |
> | Action | Microsoft. Automation/automationAccounts/Certificates/Read | Získá Azure Automation prostředek certifikátu. |
> | Action | Microsoft. Automation/automationAccounts/Certificates/Write | Vytvoří nebo aktualizuje prostředek certifikátu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Read | Přečte kompilaci Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Read | Přečte kompilaci Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilaci Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Write | Zapisuje kompilaci Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/Configurations/Content/Read | Přečte konfigurační obsah média. |
> | Action | Microsoft. Automation/automationAccounts/Configurations/DELETE | Odstraní obsah Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/Configurations/GetCount/Action | Přečte počet Azure Automation obsahu DSC. |
> | Action | Microsoft. Automation/automationAccounts/Configurations/Read | Získá obsah Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/Configurations/Write | Zapisuje obsah Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/Connections/DELETE | Odstraní prostředek připojení Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Connections/GetCount/Action | Přečte počet připojení. |
> | Action | Microsoft. Automation/automationAccounts/Connections/Read | Načte prostředek připojení Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Connections/Write | Vytvoří nebo aktualizuje prostředek připojení Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/connectionTypes/DELETE | Odstraní prostředek typu připojení Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/connectionTypes/Read | Získá prostředek typu připojení Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/connectionTypes/Write | Vytvoří prostředek typu připojení Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/přihlašovací údaje/odstranit | Odstraní prostředek přihlašovacích údajů Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/přihlašovací údaje/GetCount/Action | Načte počet přihlašovacích údajů. |
> | Action | Microsoft. Automation/automationAccounts/přihlašovací údaje/číst | Získá Azure Automation Asset přihlašovacích údajů. |
> | Action | Microsoft. Automation/automationAccounts/přihlašovací údaje/zapisovat | Vytvoří nebo aktualizuje prostředek přihlašovacích údajů Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/DELETE | Odstraní účet Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Odstraní prostředky Hybrid Runbook Worker. |
> | Action | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Přečte Hybrid Runbook Worker prostředky. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Načte výstup úlohy. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Read | Načte Azure Automation úlohu. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Obnoví Azure Automation úlohu. |
> | Action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Získá obsah Azure Automation sady Runbook v době provádění úlohy. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/stop/Action | Zastaví úlohu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Pozastaví úlohu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Jobs/Write | Vytvoří úlohu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/jobSchedules/DELETE | Odstraní plán úlohy Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/jobSchedules/Read | Získá Azure Automation plán úlohy. |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úlohy Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Získá pracovní prostor propojený s účtem Automation. |
> | Action | Microsoft. Automation/automationAccounts/klíče listkey/Action | Přečte klíče pro účet Automation. |
> | Action | Microsoft. Automation/automationAccounts/modules/Activities/Read | Získá aktivity Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Module/DELETE | Odstraní modul PowerShellu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/modules/GetCount/Action | Získá počet modulů PowerShellu v rámci účtu Automation. |
> | Action | Microsoft. Automation/automationAccounts/Module/Read | Načte Azure Automation modul PowerShellu. |
> | Action | Microsoft. Automation/automationAccounts/modules/Write | Vytvoří nebo aktualizuje modul prostředí Azure Automation PowerShell. |
> | Action | Microsoft. Automation/automationAccounts/nodeConfigurations/DELETE | Odstraní konfiguraci uzlu Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Přečte obsah konfigurace uzlu Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/nodeConfigurations/Read | Přečte konfiguraci uzlu Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Zapisuje konfiguraci uzlu Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/nodecounts/Read | Načte souhrn počtu uzlů pro zadaný typ. |
> | Action | Microsoft. Automation/automationAccounts/Nodes/DELETE | Odstraní Azure Automation uzly DSC. |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Read | Přečte Azure Automation uzly DSC. |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Reports/Reports/Read | Přečte obsah sestavy Azure Automation DSC. |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Reports/Read | Přečte Azure Automation sestavy DSC. |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Write | Vytvoří nebo aktualizuje Azure Automation uzlů DSC. |
> | Action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Získá Azure Automation TypeFields |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/delete | Odstraní balíček Azure Automation Python 2. |
> | Action | Microsoft. Automation/automationAccounts/python2Packages/Read | Získá Azure Automation balíček Python 2. |
> | Action | Microsoft. Automation/automationAccounts/python2Packages/Write | Vytvoří nebo aktualizuje balíček Azure Automation Python 2. |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/delete | Odstraní balíček Azure Automation Python 3. |
> | Action | Microsoft. Automation/automationAccounts/python3Packages/Read | Získá balíček Azure Automation Python 3. |
> | Action | Microsoft. Automation/automationAccounts/python3Packages/Write | Vytvoří nebo aktualizuje balíček Azure Automation Python 3. |
> | Action | Microsoft. Automation/automationAccounts/Read | Získá účet Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/Content/Read | Načte obsah sady Runbook Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/runbooks/delete | Odstraní Runbook Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Vytvoří obsah konceptu Azure Automation Runbooku. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/koncept/operationResults/číst | Získá výsledky operace konceptu sady Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/koncept/čtení | Načte Azure Automation koncept Runbooku. |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Načte testovací úlohu konceptu sady Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/koncept/testJob/Resume/Action | Obnoví testovací úlohu konceptu sady Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/koncept/testJob/stop/Action | Zastaví testovací úlohu konceptu sady Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/koncept/testJob/Suspend/Action | Pozastaví testovací úlohu konceptu sady Runbook Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Vytvoří testovací úlohu konceptu sady Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/koncept/undoEdit/Action | Umožňuje vrátit zpět úpravy konceptu sady Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/GetCount/Action | Získá počet Azure Automation sad Runbook. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/Publish/Action | Publikuje koncept Azure Automation Runbooku. |
> | Action | Microsoft. Automation/automationAccounts/Runbooky/číst | Získá Azure Automation Runbook. |
> | Action | Microsoft.Automation/automationAccounts/runbooks/write | Vytvoří nebo aktualizuje Runbook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Schedules/DELETE | Odstraní prostředek plánování Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Schedules/GetCount/Action | Získá počet plánů Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Schedules/Read | Získá prostředek plánování Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Schedules/Write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Odstraní Azure Automation konfiguraci aktualizace softwaru. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Získá Azure Automation konfiguraci aktualizace softwaru. |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Vytvoří nebo aktualizuje konfiguraci Azure Automation aktualizace softwaru. |
> | Action | Microsoft. Automation/automationAccounts/STATISTICS/Read | Získá statistiku Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Získat počítač nasazení aktualizace Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Získá úlohu opravy služby Azure Automation Update Management. |
> | Action | Microsoft. Automation/automationAccounts/použití/číst | Získá využití Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Variables/DELETE | Odstraní Azure Automation variabilní prostředek. |
> | Action | Microsoft. Automation/automationAccounts/Variables/číst | Přečte Azure Automation variabilní prostředek. |
> | Action | Microsoft. Automation/automationAccounts/Variables/Write | Vytvoří nebo aktualizuje prostředek proměnné Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/watchers/delete | Odstraní úlohu sledovacího procesu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/sledovací procesy/číst | Získá úlohu sledovacího procesu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/watchs/Start/Action | Spustit úlohu sledovacího procesu Azure Automation |
> | Action | Microsoft. Automation/automationAccounts/sledovací procesy/zastavení/akce | Zastavení úlohy sledovacího procesu Azure Automation |
> | Action | Microsoft. Automation/automationAccounts/sledovací procesy/proudy/číst | Získá datový proud úlohy sledovacího procesu Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Odstraní akce úlohy sledovacího procesu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/watchs/watcherActions/Read | Získá akce úlohy sledovacího procesu Azure Automation. |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Vytvořit akce úlohy sledovacího procesu Azure Automation |
> | Action | Microsoft. Automation/automationAccounts/watchs/Write | Vytvoří úlohu sledovacího procesu Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/webhooks/Action | Generuje identifikátor URI pro Azure Automation Webhook. |
> | Action | Microsoft.Automation/automationAccounts/webhooks/delete | Odstraní Webhook Azure Automation.  |
> | Action | Microsoft.Automation/automationAccounts/webhooks/read | Přečte Azure Automation Webhook. |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | Vytvoří nebo aktualizuje Webhook Azure Automation. |
> | Action | Microsoft. Automation/automationAccounts/Write | Vytvoří nebo aktualizuje účet Azure Automation. |
> | Action | Microsoft. Automation/Operations/Read | Získá dostupné operace pro prostředky Azure Automation. |
> | Action | Microsoft. Automation/Register/Action | Zaregistruje předplatné do Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Azureactivedirectory selhala/b2cDirectories/DELETE | Odstranit prostředek adresáře B2C |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | Zobrazit prostředek adresáře B2C |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | Vytvořit nebo aktualizovat prostředek adresáře B2C |
> | Action | Microsoft. Azureactivedirectory selhala/b2ctenants/Read | Zobrazí seznam všech tenantů B2C, ve kterých je uživatel členem. |
> | Action | Microsoft.AzureActiveDirectory/operations/read | Načte všechny operace rozhraní API, které jsou k dispozici pro poskytovatele prostředků Microsoft. Azureactivedirectory selhala. |
> | Action | Microsoft. Azureactivedirectory selhala/Register/Action | Registrace předplatného pro poskytovatele prostředků Microsoft. Azureactivedirectory selhala |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | Získá vlastnosti operace poskytovatele prostředků. |
> | Action | Microsoft.AzureStack/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. AzureStack. |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Odstraní předplatné zákazníka Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/read | Získá vlastnosti předplatného Azure Stack zákazníka. |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Vytvoří nebo aktualizuje předplatné Azure Stack zákazníka. |
> | Action | Microsoft.AzureStack/registrations/delete | Odstraní registraci Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | Získá nejnovější aktivační klíč Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | Načte rozšířené podrobnosti pro produkt Azure Stack Marketplace. |
> | Action | Microsoft.AzureStack/registrations/products/read | Získá vlastnosti produktu Azure Stack Marketplace. |
> | Action | Microsoft.AzureStack/registrations/read | Získá vlastnosti registrace Azure Stack. |
> | Action | Microsoft.AzureStack/registrations/write | Vytvoří nebo aktualizuje registraci Azure Stack. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Batch/batchAccounts/Applications/DELETE | Odstraní aplikaci. |
> | Action | Microsoft. Batch/batchAccounts/Applications/Read | Zobrazí seznam aplikací nebo získá vlastnosti aplikace. |
> | Action | Microsoft. Batch/batchAccounts/aplikace/verze/aktivace/akce | Aktivuje balíček aplikace. |
> | Action | Microsoft. Batch/batchAccounts/aplikace/verze/odstranit | Odstraní balíček aplikace. |
> | Action | Microsoft. Batch/batchAccounts/aplikace/verze/číst | Získá vlastnosti balíčku aplikace. |
> | Action | Microsoft. Batch/batchAccounts/aplikace/verze/zápis | Vytvoří nový balíček aplikace nebo aktualizuje existující balíček aplikace. |
> | Action | Microsoft. Batch/batchAccounts/Applications/Write | Vytvoří novou aplikaci nebo aktualizuje existující aplikaci. |
> | Action | Microsoft. Batch/batchAccounts/certificateOperationResults/Read | Získá výsledky dlouhotrvající operace s certifikátem na účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/Certificates/cancelDelete/Action | Zruší neúspěšné odstranění certifikátu na účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/Certificates/DELETE | Odstraní certifikát z účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/Certificates/Read | Zobrazí seznam certifikátů na účtu Batch nebo získá vlastnosti certifikátu. |
> | Action | Microsoft. Batch/batchAccounts/Certificates/Write | Vytvoří nový certifikát na účtu Batch nebo aktualizuje existující certifikát. |
> | Action | Microsoft. Batch/batchAccounts/DELETE | Odstraní účet Batch. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/Jobs/DELETE | Odstraní úlohu z účtu Batch. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/Jobs/Read | Vypisuje úlohy na účtu Batch nebo získá vlastnosti úlohy. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/Jobs/Write | Vytvoří novou úlohu na účtu Batch nebo aktualizuje existující úlohu. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/jobSchedules/DELETE | Odstraní plán úlohy z účtu Batch. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Read | Zobrazí plány úloh na účtu Batch nebo získá vlastnosti plánu úlohy. |
> | Akce dataaction | Microsoft. Batch/batchAccounts/jobSchedules/Write | Vytvoří nový plán úlohy na účtu Batch nebo aktualizuje existující plán úlohy. |
> | Action | Microsoft. Batch/batchAccounts/klíče listkey/Action | Seznam přístupových klíčů pro účet Batch |
> | Action | Microsoft.Batch/batchAccounts/operationResults/read | Získá výsledky dlouhotrvající operace účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/poolOperationResults/Read | Získá výsledky dlouhotrvající operace s fondem na účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/Pools/DELETE | Odstraní fond z účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/Pools/disableAutoscale/Action | Zakáže automatické škálování fondu účtů Batch. |
> | Action | Microsoft. Batch/batchAccounts/Pools/Read | Vypíše fondy na účtu Batch nebo získá vlastnosti fondu. |
> | Action | Microsoft. Batch/batchAccounts/Pools/stopResize/Action | Zastaví probíhající operaci změny velikosti ve fondu účtů Batch. |
> | Action | Microsoft. Batch/batchAccounts/Pools/Write | Vytvoří nový fond na účtu Batch nebo aktualizuje existující fond. |
> | Action | Microsoft. Batch/batchAccounts/Read | Zobrazí účty Batch nebo získá vlastnosti účtu Batch. |
> | Action | Microsoft. Batch/batchAccounts/regeneratekeys/Action | Znovu vygeneruje přístupové klíče pro účet Batch. |
> | Action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje přístupové klíče pro účet automatického úložiště nakonfigurovaný pro účet Batch. |
> | Action | Microsoft.Batch/batchAccounts/write | Vytvoří nový účet Batch nebo aktualizuje existující účet Batch. |
> | Action | Microsoft. Batch/Locations/accountOperationResults/Read | Získá výsledky dlouhotrvající operace účtu Batch. |
> | Action | Microsoft. Batch/Locations/checkNameAvailability/Action | Kontroluje, že název účtu je platný a nepoužívá se. |
> | Action | Microsoft. Batch/umístění/kvóty/číst | Načte kvóty dávky zadaného předplatného v zadané oblasti Azure. |
> | Action | Microsoft. Batch/Operations/Read | Zobrazí seznam operací dostupných v poskytovateli prostředků Microsoft. batch. |
> | Action | Microsoft. Batch/registrovat/Action | Zaregistruje předplatné pro poskytovatele prostředků služby Batch a povolí vytváření účtů Batch. |
> | Action | Microsoft. Batch/zrušit registraci/akce | Zruší registraci předplatného poskytovatele prostředků služby Batch, aby se zabránilo vytváření účtů Batch. |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. fakturace/billingAccounts/oddělení/čtení | Vypíše všechna oddělení v rámci oboru fakturačního účtu. |
> | Action | Microsoft. fakturace/billingAccounts/enrollmentAccounts/Read | Vypíše všechny účty pro zápis pod oborem fakturačního účtu. |
> | Action | Microsoft. fakturace/billingAccounts/Read | Vypíše všechny fakturační účty, ke kterým má uživatel přístup. |
> | Action | Microsoft. fakturace/billingPeriods/Read | Zobrazí seznam dostupných fakturačních období. |
> | Action | Microsoft. fakturace/oddělení/čtení | Vypíše všechna oddělení, ke kterým má uživatel přístup. |
> | Action | Microsoft. fakturace/faktury/číst | Zobrazí seznam dostupných faktur. |
> | Action | Microsoft. fakturace/registrace/akce | Zaregistruje předplatné u poskytovatele prostředků Microsoft. fakturace. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/mapApis/Delete | Odstranit operaci |
> | Action | Microsoft. BingMaps/mapApis/listSecrets/Action | Výpis tajných kódů |
> | Action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Přečíst autorizační token jednotného přihlašování pro prostředek |
> | Action | Microsoft. BingMaps/mapApis/Read | Operace čtení |
> | Action | Microsoft.BingMaps/mapApis/regenerateKey/action | Znovu vygeneruje klíč. |
> | Action | Microsoft. BingMaps/mapApis/Write | Operace zápisu |
> | Action | Microsoft. BingMaps/Operations/Read | Popis operace |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. blockchain/blockchainMembers/DELETE | Odstraní existujícího člena blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/listApiKeys/Action | Získá nebo zobrazí seznam existujících klíčů rozhraní API členů blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/Read | Získá nebo zobrazí seznam existujících členů blockchain. |
> | Akce dataaction | Microsoft. blockchain/blockchainMembers/transactionNodes/Connect/Action | Připojí se k uzlu transakce člena blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/transactionNodes/DELETE | Odstraní existující uzel transakcí členů blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/transactionNodes/listApiKeys/Action | Získá nebo zobrazí seznam existujících klíčů rozhraní API uzlu transakcí členů blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/transactionNodes/Read | Získá nebo zobrazí seznam existujících uzlů transakcí členů blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/transactionNodes/Write | Vytvoří nebo aktualizuje uzel transakcí členů blockchain. |
> | Action | Microsoft. blockchain/blockchainMembers/Write | Vytvoří nebo aktualizuje člen blockchain. |
> | Action | Microsoft. blockchain/Locations/blockchainMemberOperationResults/Read | Získá výsledky operace členů blockchain. |
> | Action | Microsoft. blockchain/Locations/checkNameAvailability/Action | Kontroluje, zda je název prostředku platný a není používán. |
> | Action | Microsoft. blockchain/Operations/Read | Vypíše všechny operace v poskytovateli prostředků Microsoft blockchain. |
> | Action | Microsoft. blockchain/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. detail/blueprintAssignments/assignmentOperations/Read | Přečtěte si všechny artefakty podrobného plánu |
> | Action | Microsoft. detail/blueprintAssignments/DELETE | Odstranit všechny artefakty podrobného plánu |
> | Action | Microsoft. detail/blueprintAssignments/Read | Přečtěte si všechny artefakty podrobného plánu |
> | Action | Microsoft. detail/blueprintAssignments/whoisblueprint/Action | Získá ID instančního objektu služby Azure modrotisky. |
> | Action | Microsoft. detail/blueprintAssignments/Write | Vytvořit nebo aktualizovat všechny artefakty podrobného plánu |
> | Action | Microsoft. Details/modrotisky/artefakty/DELETE | Odstranit všechny artefakty podrobného plánu |
> | Action | Microsoft. Details/modrotisky/artefakty/číst | Přečtěte si všechny artefakty podrobného plánu |
> | Action | Microsoft. Details/modrotisky/artefakty/Write | Vytvořit nebo aktualizovat všechny artefakty podrobného plánu |
> | Action | Microsoft. Details/modrotisky/DELETE | Odstranit všechny podrobné plány |
> | Action | Microsoft. Details/modrotisky/Read | Přečtěte si všechny podrobné plány |
> | Action | Microsoft. Details/modrotisky/verze/artefakty/čtení | Přečtěte si všechny artefakty podrobného plánu |
> | Action | Microsoft. Details/modrotisky/Versions/DELETE | Odstranit všechny podrobné plány |
> | Action | Microsoft. Details/modrotisky/verze/číst | Přečtěte si všechny podrobné plány |
> | Action | Microsoft. Details/modrotisky/Versions/Write | Vytvořit nebo aktualizovat jakékoli podrobné plány |
> | Action | Microsoft. Details/modrotisky/Write | Vytvořit nebo aktualizovat jakékoli podrobné plány |
> | Action | Microsoft. detail/Register/Action | Zaregistruje poskytovatele prostředků Azure modrotisky. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. BotService/botServices/Channels/DELETE | Odstranění služby bot |
> | Action | Microsoft. BotService/botServices/Channels/Read | Přečíst službu bot |
> | Action | Microsoft. BotService/botServices/Channels/Write | Zápis služby bot |
> | Action | Microsoft. BotService/botServices/Connections/DELETE | Odstranění služby bot |
> | Action | Microsoft. BotService/botServices/Connections/Read | Přečíst službu bot |
> | Action | Microsoft. BotService/botServices/Connections/Write | Zápis služby bot |
> | Action | Microsoft. BotService/botServices/DELETE | Odstranění služby bot |
> | Action | Microsoft. BotService/botServices/Read | Přečíst službu bot |
> | Action | Microsoft. BotService/botServices/Write | Zápis služby bot |
> | Action | Microsoft. BotService/Locations/operationresults/Read | Přečtěte si stav asynchronní operace. |
> | Action | Microsoft. BotService/Operations/Read | Přečtěte si operace pro všechny typy prostředků. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. cache/checknameavailability/Action | Zkontroluje, jestli je název k dispozici pro použití s novým Redis Cache. |
> | Action | Microsoft. cache/Locations/operationresults/Read | Získá výsledek dlouhotrvající operace, pro kterou se dřív vrátila hlavička Location do klienta. |
> | Action | Microsoft.Cache/operations/read | Obsahuje seznam operací, které podporuje poskytovatel Microsoft. cache. |
> | Action | Microsoft.Cache/redis/delete | Odstranit celý Redis Cache |
> | Action | Microsoft.Cache/redis/export/action | Exportovat Redis data do předem vydaných objektů BLOB úložiště v zadaném formátu |
> | Action | Microsoft.Cache/redis/firewallRules/delete | Odstranit pravidla brány firewall protokolu IP Redis Cache |
> | Action | Microsoft.Cache/redis/firewallRules/read | Získat pravidla brány firewall protokolu IP pro Redis Cache |
> | Action | Microsoft.Cache/redis/firewallRules/write | Upravit pravidla brány firewall protokolu IP Redis Cache |
> | Action | Microsoft.Cache/redis/forceReboot/action | Vynutí restartování instance mezipaměti, což může způsobit ztrátu dat. |
> | Action | Microsoft.Cache/redis/import/action | Import dat zadaného formátu z více objektů blob do Redis |
> | Action | Microsoft.Cache/redis/linkedservers/delete | Odstranit odkazovaný server z Redis Cache |
> | Action | Microsoft.Cache/redis/linkedservers/read | Získat propojené servery přidružené k mezipaměti Redis Cache. |
> | Action | Microsoft.Cache/redis/linkedservers/write | Přidání propojeného serveru do Redis Cache |
> | Action | Microsoft.Cache/redis/listKeys/action | Zobrazení hodnoty přístupových klíčů Redis Cache na portálu pro správu |
> | Action | Microsoft.Cache/redis/listUpgradeNotifications/read | Seznamte se s nejnovějšími oznámeními o upgradu pro tenanta mezipaměti. |
> | Action | Microsoft.Cache/redis/metricDefinitions/read | Získá dostupné metriky pro Redis Cache. |
> | Action | Microsoft.Cache/redis/patchSchedules/delete | Odstraní plán opravy Redis Cache. |
> | Action | Microsoft.Cache/redis/patchSchedules/read | Získá plán oprav Redis Cache. |
> | Action | Microsoft.Cache/redis/patchSchedules/write | Úprava plánu aktualizace Redis Cache |
> | Action | Microsoft.Cache/redis/read | Zobrazení nastavení a konfigurace Redis Cache na portálu pro správu |
> | Action | Microsoft.Cache/redis/regenerateKey/action | Změna hodnoty přístupových klíčů Redis Cache na portálu pro správu |
> | Action | Microsoft. cache/Redis/Start/Action | Spusťte instanci mezipaměti. |
> | Action | Microsoft. cache/Redis/stop/Action | Zastavte instanci mezipaměti. |
> | Action | Microsoft.Cache/redis/write | Úprava nastavení a konfigurace Redis Cache na portálu pro správu |
> | Action | Microsoft.Cache/register/action | Zaregistruje poskytovatele prostředků Microsoft. cache v rámci předplatného. |
> | Action | Microsoft.Cache/unregister/action | Zruší registraci poskytovatele prostředků Microsoft. cache u předplatného. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Capacity/appliedreservations/read | Čtení všech rezervací |
> | Action | Microsoft. Capacity/calculateexchange/Action | Vypočítá množství Exchange a cenu nového nákupu a vrátí chyby zásad. |
> | Action | Microsoft. Capacity/calculateprice/Action | Vypočítat cenu za rezervované |
> | Action | Microsoft. Capacity/Katalogs/Read | Přečíst katalog rezervací |
> | Action | Microsoft. Capacity/checkoffers/Action | Podívejte se na nabídky předplatného |
> | Action | Microsoft. Capacity/checkscopes/Action | Kontrolovat předplatné |
> | Action | Microsoft. Capacity/commercialreservationorders/Read | Získání objednávek rezervací vytvořených v jakémkoli Tenantovi |
> | Action | Microsoft. Capacity/Exchange/Action | Výměna všech rezervací |
> | Action | Microsoft.Capacity/operations/read | Čtení všech operací |
> | Action | Microsoft. Capacity/registr/Action | Zaregistruje poskytovatele prostředků kapacity a povolí vytváření prostředků kapacity. |
> | Action | Microsoft. Capacity/reservationorders/Action | Aktualizace všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/availablescopes/Action | Najít libovolný dostupný rozsah |
> | Action | Microsoft. Capacity/reservationorders/calculaterefund/Action | Vypočítá částku náhrady a cenu nového nákupu a vrátí chyby zásad. |
> | Action | Microsoft.Capacity/reservationorders/delete | Odstranit všechny rezervace |
> | Action | Microsoft. Capacity/reservationorders/Merge/Action | Sloučit všechny rezervace |
> | Action | Microsoft. Capacity/reservationorders/Read | Čtení všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/rezervací/Action | Aktualizace všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/rezervací/DELETE | Odstranit všechny rezervace |
> | Action | Microsoft. Capacity/reservationorders/rezervací/Read | Čtení všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/rezervací/revize/čtení | Čtení všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/rezervací/Write | Vytvoření jakékoli rezervace |
> | Action | Microsoft. Capacity/reservationorders/Return/Action | Vrácení všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/Split/Action | Rozdělení všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/swap/Action | Prohození všech rezervací |
> | Action | Microsoft. Capacity/reservationorders/Write | Vytvoření jakékoli rezervace |
> | Action | Microsoft. Capacity/klienti/registrovat/akce | Registrace libovolného tenanta |
> | Action | Microsoft. Capacity/zrušit registraci/akce | Zrušit registraci tenanta |
> | Action | Microsoft. Capacity/validatereservationorder/Action | Ověření všech rezervací |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. CDN/CheckNameAvailability/Action |  |
> | Action | Microsoft. CDN/CheckResourceUsage/Action |  |
> | Action | Microsoft. CDN/edgenodes/DELETE |  |
> | Action | Microsoft. CDN/edgenodes/Read |  |
> | Action | Microsoft. CDN/edgenodes/Write |  |
> | Action | Microsoft.Cdn/operationresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/Load/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/vyprázdnit/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/Action |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/stop/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft. CDN/operationresults/profileresults/GenerateSsoUri/Action |  |
> | Action | Microsoft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft. CDN/operationresults/Read |  |
> | Action | Microsoft. CDN/operationresults/Write |  |
> | Action | Microsoft.Cdn/operations/read |  |
> | Action | Microsoft. CDN/Profiles/CheckResourceUsage/Action |  |
> | Action | Microsoft. CDN/Profiles/DELETE |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/CheckResourceUsage/Action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/customdomains/DisableCustomHttps/Action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/customdomains/Read |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/customdomains/Write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/Load/Action |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/origines/DELETE |  |
> | Action | Microsoft. CDN/Profiles/koncových bodů/původu/čtení |  |
> | Action | Microsoft. CDN/Profiles/koncových bodů/origines/Write |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/disčist/Action |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/Read |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/Start/Action |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/stop/Action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Action | Microsoft. CDN/Profiles/Endpoints/Write |  |
> | Action | Microsoft. CDN/Profiles/GenerateSsoUri/Action |  |
> | Action | Microsoft. CDN/Profiles/GetSupportedOptimizationTypes/Action |  |
> | Action | Microsoft. CDN/profily/číst |  |
> | Action | Microsoft. CDN/Profiles/Write |  |
> | Action | Microsoft. CDN/registrovat/akce | Zaregistruje předplatné pro poskytovatele prostředků CDN a povolí vytváření profilů CDN. |
> | Action | Microsoft. CDN/ValidateProbe/Action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Odstraní existující certifikát. |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Certificates/Read | Získat seznam certifikátů |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Certificates/Write | Přidat nový certifikát nebo aktualizovat existující |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Delete | Odstraní existující AppServiceCertificate. |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Read | Získat seznam CertificateOrders |
> | Action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Vystavení existujícího objednávky certifikátu |
> | Action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Obnovit existující objednávky certifikátu |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Znovu poslat e-mail s certifikátem |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Znovu odeslat e-maily žádosti na jinou e-mailovou adresu |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Načtěte zapečetění webu pro vydanou App Service Certificate. |
> | Action | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Načíst seznam akcí certifikátu |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Načíst historii e-mailů certifikátu |
> | Action | Microsoft. CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Ověřit vlastnictví domény |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Write | Přidat nový objednávky certifikátu nebo aktualizovat existující |
> | Action | Microsoft.CertificateRegistration/operations/Read | Vypsat všechny operace z registrace certifikátu App Service |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zřídit instanční objekt pro instanční objekt aplikace |
> | Action | Microsoft. CertificateRegistration/Register/Action | Registrace poskytovatele prostředků certifikátů společnosti Microsoft pro předplatné |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Ověřit objekt nákupu certifikátu bez odeslání |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ClassicCompute/možnosti/číst | Zobrazuje možnosti |
> | Action | Microsoft. ClassicCompute/checkDomainNameAvailability/Action | Ověří dostupnost daného názvu domény. |
> | Action | Microsoft. ClassicCompute/checkDomainNameAvailability/Read | Získá dostupnost daného názvu domény. |
> | Action | Microsoft. ClassicCompute/domainNames/Active/Write | Nastaví název aktivní domény. |
> | Action | Microsoft. ClassicCompute/domainNames/availabilitySets/Read | Zobrazit skupinu dostupnosti pro daný prostředek. |
> | Action | Microsoft. ClassicCompute/domainNames/Capabilities/číst | Zobrazuje možnosti názvu domény. |
> | Action | Microsoft.ClassicCompute/domainNames/delete | Odeberte názvy domén pro prostředky. |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/Read | Zobrazuje sloty nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/role/číst | Získat roli pro slot nasazení názvu domény |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/role/roleinstances/Read | Získat instanci role pro roli ve slotu nasazení název domény |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/State/Read | Získejte stav slotu pro nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/State/Write | Přidejte stav slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/upgradedomain/Read | Získat doménu upgradu pro slot nasazení v názvu domény |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/upgradedomain/Write | Aktualizovat upgradovací doménu pro slot nasazení v názvu domény |
> | Action | Microsoft. ClassicCompute/domainNames/deploymentslots/Write | Vytvoří nebo aktualizuje nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/Extensions/DELETE | Odeberte přípony názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/Extensions/operationStatuses/Read | Přečte stav operace pro rozšíření názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/Extensions/Read | Vrátí přípony názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/Extensions/Write | Přidejte přípony názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/DELETE | Odebere nové interní vyrovnávání zatížení. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Přečte stav operace pro názvy domén interních nástrojů pro vyrovnávání zatížení. |
> | Action | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Read | Načte interní nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Write | Vytvoří nový interní vyrovnávání zatížení. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Přečte stav operace pro sady koncových bodů s vyrovnáváním zatížení názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Read | Získejte sady koncových bodů s vyrovnáváním zatížení. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Přidejte sadu koncových bodů s vyrovnáváním zatížení. |
> | Action | Microsoft. ClassicCompute/domainNames/operationstatuses/Read | Získá stav operace pro název domény. |
> | Action | Microsoft. ClassicCompute/domainNames/operationStatuses/Read | Přečte stav operace pro rozšíření názvů domén. |
> | Action | Microsoft.ClassicCompute/domainNames/read | Vrátí názvy domén pro prostředky. |
> | Action | Microsoft. ClassicCompute/domainNames/serviceCertificates/DELETE | Odstraňte použité certifikáty služby. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Přečte stav operace pro certifikáty služby názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/serviceCertificates/Read | Vrátí používané certifikáty služeb. |
> | Action | Microsoft. ClassicCompute/domainNames/serviceCertificates/Write | Umožňuje přidat nebo upravit používané certifikáty služeb. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/abortMigration/Action | Přeruší migraci slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/commitMigration/Action | Potvrdí migraci slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/DELETE | Odstraní zadaný slot nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/operationStatuses/Read | Přečte stav operace pro sloty názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/prepareMigration/Action | Připraví migraci slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/číst | Zobrazuje sloty nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/DELETE | Odeberte odkaz na rozšíření role slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/operationStatuses/Read | Přečte stav operace pro odkazy na rozšíření rolí slotů názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/Read | Vrátí odkaz na rozšíření role slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/extensionReferences/Write | Přidejte nebo upravte odkaz na rozšíření role slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/metricdefinitions/Read | Získejte definici metriky role pro název domény. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/metriky/číst | Získá metriku role pro název domény. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/operationstatuses/Read | Získejte stav operace pro roli slotu názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Získat nastavení diagnostiky. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Získá definice metrik. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/číst | Získejte roli pro slot nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/downloadremotedesktopconnectionfile/Action | Stáhne soubor připojení ke vzdálené ploše pro instanci role v roli slotu názvů domén. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Získá stav operace pro instanci role v roli slotu názvů domén. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/Read | Získejte instanci role. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/znovu sestavit/akce | Znovu sestaví instanci role. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/obnovení obrazu/akce | Obnoví instanci role z image. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/roleInstances/restart/Action | Restartuje instance rolí. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/SKU/číst | Získá SKU role pro slot nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/role/zápis | Přidejte roli pro slot nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/Start/Action | Spustí slot nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/State/Start/Write | Změní stav slotu nasazení na zastaveno. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/State/stop/Write | Změní stav slotu nasazení na spuštěno. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/stop/Action | Pozastaví slot nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/upgradeDomain/Write | Projděte si upgrade domény. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/validateMigration/Action | Ověří migraci slotu nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/sloty/Write | Vytvoří nebo aktualizuje nasazení. |
> | Action | Microsoft. ClassicCompute/domainNames/swap/Action | Zamění pracovní slot na produkční slot. |
> | Action | Microsoft.ClassicCompute/domainNames/write | Přidejte nebo upravte názvy domén pro prostředky. |
> | Action | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Přesuňte všechny klasické prostředky do jiného předplatného. |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Obsahuje seznam rodin hostovaných operačních systémů, které jsou k dispozici v Microsoft Azure, a také uvádí dostupné verze operačních systémů pro každou rodinu. |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | Uvádí verze hostovaného operačního systému, které jsou v Microsoft Azure aktuálně k dispozici. |
> | Action | Microsoft. ClassicCompute/Operations/Read | Získá seznam operací. |
> | Action | Microsoft.ClassicCompute/operationStatuses/read | Přečte stav operace pro daný prostředek. |
> | Action | Microsoft. ClassicCompute/kvóty/čtení | Získejte kvótu pro předplatné. |
> | Action | Microsoft. ClassicCompute/Register/Action | Registrovat do klasického výpočetního prostředí |
> | Action | Microsoft. ClassicCompute/ResourceType/SKU/Read | Získá seznam SKU pro podporované typy prostředků. |
> | Action | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Ověří dostupnost předplatného pro klasickou operaci přesunutí. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě přidruženou k virtuálnímu počítači. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupiny zabezpečení sítě přidružené k virtuálním počítačům. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě přidruženou k virtuálnímu počítači. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě přidruženou k virtuálnímu počítači. |
> | Action | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Získá možné asynchronní operace. |
> | Action | Microsoft. ClassicCompute/virtualMachines/attachDisk/Action | Připojí datový disk k virtuálnímu počítači. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Capture/Action | Zachyťte virtuální počítač. |
> | Action | Microsoft. ClassicCompute/virtualMachines/DELETE | Odebere virtuální počítače. |
> | Action | Microsoft. ClassicCompute/virtualMachines/detachDisk/Action | Odpojí datový disk od virtuálního počítače. |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Získat nastavení diagnostiky virtuálního počítače |
> | Action | Microsoft. ClassicCompute/virtualMachines/disks/Read | Načte seznam datových disků. |
> | Action | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/Action | Stáhne soubor RDP pro virtuální počítač. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Přečte stav operace pro rozšíření virtuálních počítačů. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Extensions/Read | Získá rozšíření virtuálního počítače. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Vloží rozšíření virtuálního počítače. |
> | Action | Microsoft. ClassicCompute/virtualMachines/metricdefinitions/Read | Získat definici metriky virtuálního počítače |
> | Action | Microsoft. ClassicCompute/virtualMachines/metriky/číst | Získá metriky. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě přidruženou k síťovému rozhraní. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupiny zabezpečení sítě přidružené k virtuálním počítačům. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě přidruženou k síťovému rozhraní. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě přidruženou k síťovému rozhraní. |
> | Action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Přečte stav operace pro virtuální počítače. |
> | Action | Microsoft. ClassicCompute/virtualMachines/performMaintenance/Action | Provede údržbu na virtuálním počítači. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Read | Získat nastavení diagnostiky. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/metricDefinitions/Read | Získá definice metrik. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Read | Načte seznam virtuálních počítačů. |
> | Action | Microsoft. ClassicCompute/virtualMachines/znovu nasadit/akce | Znovu nasadí virtuální počítač. |
> | Action | Microsoft. ClassicCompute/virtualMachines/restart/Action | Restartuje virtuální počítače. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Shutdown/Action | Vypněte virtuální počítač. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Start/Action | Spusťte virtuální počítač. |
> | Action | Microsoft. ClassicCompute/virtualMachines/stop/Action | Zastaví virtuální počítač. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Write | Přidání nebo úprava virtuálních počítačů. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/Read | Získejte stav operace vzájemného propojení Express Route. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/peering/DELETE | Odstranit partnerský vztah mezi propojeními Express Route |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/peering/operationstatuses/Read | Získejte stav operace vzájemného propojení Express Route. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/peering/Read | Získá partnerský vztah mezi spojeními Express Route. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/peering/Write | Přidejte partnerský vztah vzájemného propojení Express Route. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/Read | Získejte vzájemná propojení Express Route. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Přidejte vzájemná propojení Express Route. |
> | Action | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Načte seznam podporovaných zařízení. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupinu zabezpečení sítě. |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky skupiny zabezpečení sítě. |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky skupiny zabezpečení sítě. Tato operace je doplněna poskytovatelem prostředků Insights. |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/logDefinitions/Read | Načte události pro skupinu zabezpečení sítě. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/read | Načte skupinu zabezpečení sítě. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Odstraní pravidlo zabezpečení. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Přečte stav operace pro pravidla zabezpečení skupiny zabezpečení sítě. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Získá pravidlo zabezpečení. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Přidá nebo aktualizuje pravidlo zabezpečení. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | Přidá novou skupinu zabezpečení sítě. |
> | Action | Microsoft. ClassicNetwork/Operations/Read | Získejte klasické síťové operace. |
> | Action | Microsoft. ClassicNetwork/kvóty/čtení | Získejte kvótu pro předplatné. |
> | Action | Microsoft. ClassicNetwork/Register/Action | Registrovat do klasické sítě |
> | Action | Microsoft.ClassicNetwork/reservedIps/delete | Odstraní vyhrazenou IP adresu. |
> | Action | Microsoft.ClassicNetwork/reservedIps/join/action | Připojit se k rezervované IP adrese |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | Propojení rezervované IP adresy |
> | Action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Přečte stav operace pro rezervované IP adresy. |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | Načte rezervované IP adresy. |
> | Action | Microsoft.ClassicNetwork/reservedIps/write | Přidat novou rezervovanou IP adresu |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/Action | Přeruší migraci Virtual Network. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Capabilities/číst | Zobrazuje možnosti |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/Action | Kontroluje dostupnost dané IP adresy ve virtuální síti. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/Action | Potvrdí migraci Virtual Network. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/DELETE | Odstraní virtuální síť. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Zruší odvolání klientského certifikátu. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Přečtěte si odvolané klientské certifikáty. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Odvolá klientský certifikát. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Odstraní certifikát klienta brány virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/Download/Action | Stáhne certifikát podle kryptografického otisku. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Vypíše balíček certifikátů brány virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Vyhledejte klientské kořenové certifikáty. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Nahraje nový kořenový certifikát klienta. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/Connections/Connect/Action | Připojí lokalitu k připojení brány lokality. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/Connections/Connect/Action | Odpojí lokalitu s připojením k bráně lokality. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/brány/připojení/číst | Načte seznam připojení. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/Connections/test/Action | Testuje připojení lokality k bráně lokality. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Odstraní bránu virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/Action | Stáhne konfigurační skript zařízení. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/Action | Stáhne diagnostiku brány. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/Action | Načte klíč služby okruhu. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/listPackage/Action | Vypíše balíček brány virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Přečte stav operace pro brány virtuálních sítí. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/brány/balíčky/číst | Načte balíček brány virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Načte brány virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/startDiagnostics/Action | Spustí diagnostiku brány virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/Action | Zastaví diagnostiku brány virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/Write | Přidá bránu virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/join/action | Připojí se k virtuální síti. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Přečte stav operace pro virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Navázání partnerského vztahu virtuální sítě s jinou virtuální sítí. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/Action | Připraví migraci Virtual Network. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | Získat virtuální síť. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Odstraní proxy server partnerského vztahu vzdálené virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Načte proxy server partnerského vztahu vzdálené virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Přidá nebo aktualizuje proxy server partnerského vztahu vzdálené virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě přidruženou k podsíti. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupinu zabezpečení sítě přidruženou k podsíti virtuální sítě. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě přidruženou k podsíti. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě spojenou s podsítí. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/Action | Ověří migraci Virtual Network |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Načte partnerský vztah virtuální sítě. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Write | Přidejte novou virtuální síť. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ClassicStorage/možnosti/číst | Zobrazuje možnosti |
> | Action | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Kontroluje dostupnost účtu úložiště. |
> | Action | Microsoft. ClassicStorage/checkStorageAccountAvailability/Read | Získejte dostupnost účtu úložiště. |
> | Action | Microsoft. ClassicStorage/disky/číst | Vrátí disk účtu úložiště. |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | Získá stav operace image. |
> | Action | Microsoft. ClassicStorage/images/Read | Vrátí obrázek. |
> | Action | Microsoft. ClassicStorage/Operations/Read | Získá klasické operace úložiště. |
> | Action | Microsoft.ClassicStorage/osImages/read | Vrátí bitovou kopii operačního systému. |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | Načte image platformy operačního systému. |
> | Action | Microsoft.ClassicStorage/publicImages/read | Získá veřejnou image virtuálního počítače. |
> | Action | Microsoft. ClassicStorage/kvóty/čtení | Získejte kvótu pro předplatné. |
> | Action | Microsoft. ClassicStorage/Register/Action | Registrovat do klasického úložiště |
> | Action | Microsoft. ClassicStorage/storageAccounts/abortMigration/Action | Přeruší migraci účtu úložiště. |
> | Action | Microsoft. ClassicStorage/storageAccounts/commitMigration/Action | Potvrdí migraci účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/delete | Odstraňte účet úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/delete | Odstraní daný disk účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Přečte stav operace pro daný prostředek. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | Vrátí disk účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | Přidá disk účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | Odstraní danou image účtu úložiště. Zastaralé. Použijte Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Vrátí stav operace image účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí image účtu úložiště. Zastaralé. Použijte Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Vypíše přístupové klíče pro účty úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Přečte stav operace pro daný prostředek. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Odstraní danou image operačního systému účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | Vrátí image operačního systému účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/write | Přidá určenou bitovou kopii operačního systému účtu úložiště. |
> | Action | Microsoft. ClassicStorage/storageAccounts/prepareMigration/Action | Připraví migraci účtu úložiště. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Získat nastavení diagnostiky. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Získá definice metrik. |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | Vrátí účet úložiště s daným účtem. |
> | Action | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Obnoví existující přístupové klíče pro účet úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Získat nastavení diagnostiky. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Přidat nebo upravit nastavení diagnostiky. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/Read | Získá definice metrik. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/metriky/číst | Získá metriky. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/read | Získejte dostupné služby. |
> | Action | Microsoft. ClassicStorage/storageAccounts/validateMigration/Action | Ověří migraci účtu úložiště. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Odstraní zadanou image virtuálního počítače. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Načte daný stav operace image virtuálního počítače. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Vrátí image virtuálního počítače. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Přidá zadanou image virtuálního počítače. |
> | Action | Microsoft.ClassicStorage/storageAccounts/write | Přidá nový účet úložiště. |
> | Action | Microsoft.ClassicStorage/vmImages/read | Zobrazí seznam imagí virtuálních počítačů. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/analyzovat/Action | Tato operace extrahuje bohatou sadu vizuálních funkcí založenou na obsahu obrázku.  |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/areaofinterest/Action | Tato operace vrátí ohraničující rámeček kolem nejdůležitější oblasti obrázku. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/popis/akce | Tato operace vygeneruje popis obrázku v jazyce pro lidské čtení s úplnými větami.<br> Popis je založen na kolekci značek obsahu, které jsou také vráceny v rámci operace.<br>Pro každý obrázek lze vygenerovat více než jeden popis.<br> Popisy jsou seřazené podle jejich skóre spolehlivosti.<br>Všechny popisy jsou v angličtině. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Detect/Action | Tato operace provádí detekci objektu na zadané imagi.  |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/generatethumbnail/Action | Tato operace vygeneruje miniaturní obrázek s uživatelem zadanou šířkou a výškou.<br> Ve výchozím nastavení služba analyzuje obrázek, identifikuje oblast zájmu a vygeneruje souřadnice inteligentního oříznutí na základě návratnosti investic.<br> Inteligentní oříznutí pomáhá při určení poměru stran, který se liší od hodnoty vstupního obrázku. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Tato operace rozpoznává obsah v rámci bitové kopie použitím modelu specifického pro doménu.<br> Seznam modelů specifických pro doménu, které rozhraní API pro počítačové zpracování obrazu podporuje, se dají načíst pomocí žádosti o získání/Models.<br> V současné době rozhraní API poskytuje následující modely specifické pro doménu: celebrit, orientačních bodů. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/Models/Read | Tato operace vrátí seznam modelů specifických pro doménu, které jsou podporovány rozhraní API pro počítačové zpracování obrazu.  Rozhraní API v současné době podporuje následující modely specifické pro doménu: Nástroj pro rozpoznávání celebrit, nástroj pro rozpoznávání bodů. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/OCR/Action | Optické rozpoznávání znaků (OCR) detekuje text v obrázku a extrahuje rozpoznané znaky do datového proudu znaků, který je použitelný pro počítač.    |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/recognizetext/Action | Pomocí tohoto rozhraní získáte výsledek operace Rozpoznávání textu. Když použijete rozhraní Rozpoznávání textu, odpověď obsahuje pole s názvem "Operation-Location". Pole "umístění operace" obsahuje adresu URL, kterou je nutné použít pro operaci získání Rozpoznávání textuho výsledku operace get. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/tag/Action | Tato operace vygeneruje seznam slov nebo značek, které jsou relevantní pro obsah dodané image.<br>Rozhraní API pro počítačové zpracování obrazu může vracet značky založené na objektech, živém, krajin nebo akcích, které se našly na obrázcích.<br>Na rozdíl od kategorií nejsou značky uspořádány podle hierarchického klasifikačního systému, ale odpovídají obsahu obrázku.<br>Značky mohou obsahovat tipy pro předcházení nejednoznačnosti nebo zadání kontextu, například značka "cello" může být doprovázena nápovědou "hudební instrument".<br>Všechny značky jsou v angličtině. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/ComputerVision/textoperations/Read | Toto rozhraní se používá k získání výsledku pro rozpoznání textové operace. Adresa URL tohoto rozhraní by měla být načtena z Rozpoznávání textuho rozhraní z pole <b>"Operation Location"</b> . |
> | Action | Microsoft.CognitiveServices/accounts/delete | Odstraní účty rozhraní API. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/Detect/Action | Rozpoznávat lidské obličeje v obrázku, vracet obdélníky obličeje a volitelně pomocí faceIds, orientačních bodů a atributů. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/facelists/DELETE | Odstraní zadaný seznam obličeje. Související obrázky obličeje v seznamu obličeje budou také odstraněny. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Odstraní obličej ze seznamu obličeje zadanými faceListId a persisitedFaceId. Odstraní se i související image obličeje. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Přidejte obličej do určeného seznamu obličeje, až 1 000 obličeje. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/facelists/Read | Načte faceListId, název, userData a obličeje v seznamu obličeje v seznamu obličej. Seznam obličeje obsahuje faceListId, název a userData. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/facelists/Write | Vytvoří prázdný seznam obličeje pomocí uživatelem zadaného faceListId, názvu a volitelného uživatelského jména. Až 64 seznamů obličeje jsou povolené informace o aktualizaci seznamu obličeje, včetně názvu a userData. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/findsimilars/Action | PřefaceId na podobné plošky z pole faceId, na seznam obličeje nebo na rozsáhlý seznam obličeje. faceId |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/Group/Action | Vydělte kandidáty kandidátů do skupin na základě podobnosti obličeje. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/Face/identifikují/Action | Identifikace 1: n pro vyhledání nejbližších shod konkrétního uživatele dotazu ze skupiny osob nebo velké skupiny osob. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Odstraní zadaný velký seznam obličeje. Související obrázky na obličeji ve velkém seznamu obličeje budou také odstraněny. |
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
> | Action | Microsoft.CognitiveServices/accounts/listKeys/action | Zobrazit seznam klíčů |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Načte předpověď publikovaných koncových bodů pro daný dotaz. |
> | Action | Microsoft.CognitiveServices/accounts/read | Přečte účty rozhraní API. |
> | Action | Microsoft.CognitiveServices/accounts/regenerateKey/action | Znovu vygenerovat klíč |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | Přečte dostupné SKU pro existující prostředek. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Rozhraní API vrátí seznam známých entit a obecně pojmenovaných entit (\"osoba\", \"umístění\", \"organizace\" atd.) v daném dokumentu. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Rozhraní API vrátí seznam řetězců označujících klíčová témata ve vstupním textu. |
> | Akce dataaction | Microsoft. Cognitiveservices Account/Accounts/TextAnalytics/jazyky/akce | Rozhraní API vrátí detekovaný jazyk a číselné hodnocení v rozsahu 0 až 1. Hodnocení blížící se 1 značí 100% jistotu správné identifikace jazyka. Celkem se podporuje 120 jazyků. |
> | Akce dataaction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Rozhraní API vrátí číselné hodnocení v rozsahu 0 až 1.<br>Skóre blížící se 1 značí pozitivní mínění a skóre blížící se 0 značí negativní mínění.<br>Skóre 0,5 označuje nedostatek mínění (např.<br>příkaz factoid). |
> | Action | Microsoft.CognitiveServices/accounts/usages/read | Získá využití kvóty pro existující prostředek. |
> | Action | Microsoft.CognitiveServices/accounts/write | Zapisuje účty rozhraní API. |
> | Action | Microsoft.CognitiveServices/checkDomainAvailability/action | Přečte dostupné SKU pro předplatné. |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Přečte dostupné SKU pro předplatné. |
> | Action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Oznámení od společnosti Microsoft. Network odstraňování VirtualNetworks nebo podsítí. |
> | Action | Microsoft.CognitiveServices/Operations/read | Vypsat všechny dostupné operace |
> | Action | Microsoft.CognitiveServices/register/action | Zaregistruje předplatné pro Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Commerce/RateCard/Read | Vrátí data nabídky, metadata prostředku/měřiče a sazby pro dané předplatné. |
> | Action | Microsoft.Commerce/UsageAggregates/read | Načte spotřebu Microsoft Azure předplatným. Výsledek obsahuje agregovaná data o využití, informace o předplatném a zdroji, v určitém časovém rozsahu. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. COMPUTE/availabilitySets/DELETE | Odstraní skupinu dostupnosti. |
> | Action | Microsoft. COMPUTE/availabilitySets/Read | Získá vlastnosti skupiny dostupnosti. |
> | Action | Microsoft. COMPUTE/availabilitySets/povolených velikostí/Read | Vypíše dostupné velikosti pro vytvoření nebo aktualizaci virtuálního počítače ve skupině dostupnosti. |
> | Action | Microsoft. COMPUTE/availabilitySets/Write | Vytvoří novou skupinu dostupnosti nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/disks/beginGetAccess/Action | Získat identifikátor URI SAS disku pro přístup k objektu BLOB |
> | Action | Microsoft. COMPUTE/disks/DELETE | Odstraní disk. |
> | Action | Microsoft. COMPUTE/disks/endGetAccess/Action | Odvolání identifikátoru URI SAS disku |
> | Action | Microsoft. COMPUTE/disky/číst | Získá vlastnosti disku. |
> | Action | Microsoft. COMPUTE/disks/Write | Vytvoří nový disk nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/Galerie/odstranění | Odstraní galerii. |
> | Action | Microsoft.Compute/galleries/images/delete | Odstraní obrázek Galerie. |
> | Action | Microsoft.Compute/galleries/images/read | Získá vlastnosti obrázku galerie. |
> | Action | Microsoft.Compute/galleries/images/versions/delete | Odstraní verzi image galerie. |
> | Action | Microsoft.Compute/galleries/images/versions/read | Získá vlastnosti verze image galerie. |
> | Action | Microsoft. COMPUTE/Galerie/images/verze/zápis | Vytvoří novou verzi Image Galerie nebo aktualizuje stávající. |
> | Action | Microsoft.Compute/galleries/images/write | Vytvoří novou image galerie nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/Galerie/čtení | Získá vlastnosti galerie. |
> | Action | Microsoft. COMPUTE/Galerie/zápis | Vytvoří novou galerii nebo aktualizuje stávající. |
> | Action | Microsoft.Compute/hostGroups/delete | Odstraní skupinu hostitelů. |
> | Action | Microsoft.Compute/hostGroups/read | Získá vlastnosti skupiny hostitelů. |
> | Action | Microsoft.Compute/hostGroups/write | Vytvoří novou skupinu hostitelů nebo aktualizuje stávající skupinu hostitelů. |
> | Action | Microsoft. COMPUTE/hostitelé/odstranit | Odstraní hostitele. |
> | Action | Microsoft. COMPUTE/hostitelé/číst | Získání vlastností hostitele |
> | Action | Microsoft. COMPUTE/hostitelé/zapisovat | Vytvoří nového hostitele nebo aktualizuje stávajícího hostitele. |
> | Action | Microsoft. COMPUTE/images/DELETE | Odstraní obrázek. |
> | Action | Microsoft. COMPUTE/images/Read | Získá vlastnosti obrázku. |
> | Action | Microsoft. COMPUTE/images/Write | Vytvoří novou bitovou kopii nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/Locations/capsOperations/Read | Získá stav operace asynchronního zakončení. |
> | Action | Microsoft.Compute/locations/diskOperations/read | Získá stav operace asynchronního disku. |
> | Action | Microsoft. COMPUTE/Locations/logAnalytics/getRequestRateByInterval/Action | Vytvořte protokoly, abyste zobrazili celkový počet požadavků podle časového intervalu na podporu pro diagnostiku omezování. |
> | Action | Microsoft. COMPUTE/Locations/logAnalytics/getThrottledRequests/Action | Vytvořte protokoly, aby se zobrazily agregované požadavky s omezením seskupené podle hodnoty resourceName, OperationName nebo aplikovaných zásad omezení. |
> | Action | Microsoft. COMPUTE/umístění/operace/čtení | Získá stav asynchronní operace. |
> | Action | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/nabídky/číst | Získat vlastnosti nabídky image platformy |
> | Action | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/nabídky/SKU/čtení | Získá vlastnosti SKU image platformy. |
> | Action | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/nabídky/SKU/verze/čtení | Získá vlastnosti verze image platformy. |
> | Action | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/typy/čtení | Získá vlastnosti VMExtension typu. |
> | Action | Microsoft. COMPUTE/umístění/vydavatelé/artifacttypes/typy/verze/číst | Získá vlastnosti verze VMExtension. |
> | Action | Microsoft. COMPUTE/umístění/vydavatelé/číst | Získat vlastnosti vydavatele |
> | Action | Microsoft. COMPUTE/Locations/runCommands/Read | Zobrazí seznam dostupných příkazů spuštění v umístění. |
> | Action | Microsoft. COMPUTE/umístění/využití/čtení | Získá omezení služby a aktuální množství využití výpočetních prostředků předplatného v daném umístění. |
> | Action | Microsoft. COMPUTE/Locations/povolených velikostí/Read | Zobrazí seznam dostupných velikostí virtuálních počítačů v daném umístění. |
> | Action | Microsoft. COMPUTE/Operations/Read | Zobrazí seznam operací, které jsou k dispozici na poskytovateli prostředků Microsoft. Compute. |
> | Action | Microsoft. COMPUTE/proximityPlacementGroups/DELETE | Odstraní skupinu umístění blízkosti. |
> | Action | Microsoft. COMPUTE/proximityPlacementGroups/Read | Získá vlastnosti skupiny umístění blízkosti. |
> | Action | Microsoft. COMPUTE/proximityPlacementGroups/Write | Vytvoří novou skupinu umístění pro proximity nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/registr/Action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. Compute. |
> | Action | Microsoft.Compute/restorePointCollections/delete | Odstraní kolekci bodů obnovení a obsažené body obnovení. |
> | Action | Microsoft.Compute/restorePointCollections/read | Získá vlastnosti kolekce bodů obnovení. |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/delete | Odstraní bod obnovení. |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/read | Získá vlastnosti bodu obnovení. |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Získat vlastnosti bodu obnovení spolu s identifikátory URI SAS objektů BLOB |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | Vytvoří nový bod obnovení. |
> | Action | Microsoft.Compute/restorePointCollections/write | Vytvoří novou kolekci bodů obnovení nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/sharedVMImages/DELETE | Odstraní SharedVMImage |
> | Action | Microsoft. COMPUTE/sharedVMImages/Read | Získá vlastnosti SharedVMImage. |
> | Action | Microsoft. COMPUTE/sharedVMImages/verze/DELETE | Odstranit SharedVMImageVersion |
> | Action | Microsoft. COMPUTE/sharedVMImages/verze/číst | Získá vlastnosti SharedVMImageVersion. |
> | Action | Microsoft. COMPUTE/sharedVMImages/verze/replika/akce | Replikace SharedVMImageVersion do cílových oblastí |
> | Action | Microsoft. COMPUTE/sharedVMImages/verze/Write | Vytvoří novou SharedVMImageVersion nebo aktualizuje stávající. |
> | Action | Microsoft.Compute/sharedVMImages/write | Vytvoří nový SharedVMImage nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/SKU/číst | Získá seznam skladových položek Microsoft. COMPUTE dostupných pro vaše předplatné. |
> | Action | Microsoft. COMPUTE/snímky/beginGetAccess/Action | Získat identifikátor URI SAS snímku pro přístup k objektu BLOB |
> | Action | Microsoft. COMPUTE/snímky/odstranit | Odstranění snímku |
> | Action | Microsoft. COMPUTE/snímky/endGetAccess/Action | Odvolání identifikátoru URI SAS snímku |
> | Action | Microsoft. COMPUTE/snímky/číst | Získá vlastnosti snímku. |
> | Action | Microsoft. COMPUTE/snímky/zápis | Vytvoří nový snímek nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/zrušit registraci/akce | Zruší registraci předplatného u poskytovatele prostředků Microsoft. Compute. |
> | Action | Microsoft. COMPUTE/virtualMachines/Capture/Action | Zachycuje virtuální počítač kopírováním virtuálních pevných disků a vygeneruje šablonu, která se dá použít k vytvoření podobných virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/Action | Převede disky na bázi objektů BLOB virtuálního počítače na spravované disky. |
> | Action | Microsoft. COMPUTE/virtualMachines/unallocate/Action | Vyžádá virtuální počítač a uvolní výpočetní prostředky. |
> | Action | Microsoft. COMPUTE/virtualMachines/DELETE | Odstraní virtuální počítač. |
> | Action | Microsoft. COMPUTE/virtualMachines/Extensions/DELETE | Odstraní rozšíření virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachines/Extensions/Read | Získá vlastnosti rozšíření virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachines/Extensions/Write | Vytvoří nové rozšíření virtuálního počítače nebo aktualizuje stávající. |
> | Action | Microsoft. COMPUTE/virtualMachines/generalize/akce | Nastaví stav virtuálního počítače jako zobecněný a připraví virtuální počítač pro zachytávání. |
> | Action | Microsoft. COMPUTE/virtualMachines/instanceView/Read | Získá podrobný běhový stav virtuálního počítače a jeho prostředků. |
> | Akce dataaction | Microsoft. COMPUTE/virtualMachines/Login/Action | Přihlaste se k virtuálnímu počítači jako běžný uživatel. |
> | Akce dataaction | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/Action | Přihlášení k virtuálnímu počítači pomocí oprávnění správce Windows nebo kořenového uživatele platformy Linux |
> | Action | Microsoft. COMPUTE/virtualMachines/performMaintenance/Action | Provede na virtuálním počítači operaci údržby. |
> | Action | Microsoft. COMPUTE/virtualMachines/stavu PowerOff/Action | Umožňuje vypnout virtuální počítač. Všimněte si, že se virtuální počítač bude nadále účtovat. |
> | Action | Microsoft. COMPUTE/virtualMachines/Read | Získá vlastnosti virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachines/znovu nasadit/akce | Znovu nasadí virtuální počítač. |
> | Action | Microsoft. COMPUTE/virtualMachines/ReImage/Action | Obnoví virtuální počítač, který používá rozdílový disk, z image. |
> | Action | Microsoft. COMPUTE/virtualMachines/restart/Action | Restartuje virtuální počítač. |
> | Action | Microsoft. COMPUTE/virtualMachines/SpustitPříkaz/Action | Spustí na virtuálním počítači předdefinovaný skript. |
> | Action | Microsoft. COMPUTE/virtualMachines/Start/Action | Spustí virtuální počítač. |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | Zobrazí seznam dostupných velikostí, na které se dá virtuální počítač aktualizovat. |
> | Action | Microsoft. COMPUTE/virtualMachines/Write | Vytvoří nový virtuální počítač nebo aktualizuje stávající virtuální počítač. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/unallocate/Action | Vypíná a uvolňuje výpočetní prostředky pro instance sady škálování virtuálních počítačů.  |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/DELETE | Odstraní sadu škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Odstraní instance sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/DELETE | Odstraní rozšíření sady škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Získá vlastnosti rozšíření sady škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Vytvoří nové rozšíření sady škálování virtuálního počítače nebo aktualizuje stávající. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ruční procházení domén aktualizace platformy pro sadu škálování virtuálního počítače Service Fabric, aby se dokončila zablokovaná nedokončená aktualizace |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/instanceView/Read | Získá zobrazení instance sady škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/Action | Aktualizuje instance ručně na nejnovější model sady škálování virtuálních počítačů. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Získá vlastnosti všech síťových rozhraní sady škálování virtuálního počítače. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Spustí postupný upgrade, aby se přesunuly všechny instance sady škálování virtuálních počítačů na nejnovější dostupnou verzi operačního systému image platformy. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/osUpgradeHistory/Read | Načte historii upgradů operačního systému pro sadu škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/Action | Provede plánovanou údržbu instancí sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/stavu PowerOff/Action | Umožňuje vypnout instance sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/publicIPAddresses/Read | Získá vlastnosti všech veřejných IP adres sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Read | Získá vlastnosti sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/znovu nasadit/akce | Znovu nasaďte instance sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/ReImage/Action | Obnoví instance sady škálování virtuálních počítačů z image. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/reimageAll/Action | Obnoví z image všechny disky (disk s operačním systémem a datové disky) pro instance sady škálování virtuálních počítačů.  |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/restart/Action | Restartuje instance sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Zruší postupný upgrade sady škálování virtuálních počítačů. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Získat nejnovější stav upgradu pro sadu škálování virtuálního počítače |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Scale/Action | Ověřte, jestli se může existující sada škálování virtuálního počítače škálovat nebo škálovat na zadaný počet instancí. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/SKU/Read | Vypíše platné SKU pro existující sadu škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Start/Action | Spustí instance sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/unallocate/Action | Vypíná a uvolňuje výpočetní prostředky pro virtuální počítač v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/DELETE | Odstraní konkrétní virtuální počítač v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/instanceView/Read | Načte zobrazení instance virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Získá vlastnosti veřejné IP adresy vytvořené pomocí sady škálování virtuálních počítačů. Sada škálování virtuálního počítače může vytvořit maximálně jednu veřejnou IP adresu na IPConfiguration (privátní IP adresa). |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Získá vlastnosti jedné nebo všech konfigurací IP síťového rozhraní vytvořeného pomocí sady škálování virtuálního počítače. Konfigurace protokolu IP reprezentují privátní IP adresy. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Read | Získá vlastnosti jednoho nebo všech síťových rozhraní virtuálního počítače vytvořeného pomocí sady škálování virtuálních počítačů. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Provede plánovanou údržbu instance virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/stavu PowerOff/Action | Umožňuje vypnout instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Read | Načte vlastnosti virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/znovu nasadit/akce | Znovu nasadí instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/ReImage/Action | Obnoví instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/reimageAll/Action | Obnoví z image všechny disky (disk s operačním systémem a datové disky) pro instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/Action | Restartuje instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/SpustitPříkaz/Action | Spustí předdefinovaný skript v instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Start/Action | Spustí instanci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Aktualizuje vlastnosti virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/povolených velikostí/Read | Vypíše dostupné velikosti pro vytvoření nebo aktualizaci virtuálního počítače v sadě škálování virtuálního počítače. |
> | Action | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Vytvoří novou sadu škálování virtuálního počítače nebo aktualizuje stávající. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. spotřeb/bilance/číst | Vypíše Souhrn využití pro fakturační období pro skupinu pro správu. |
> | Action | Microsoft. spotřeb/rozpočty/odstranit | Odstraňte rozpočty podle předplatného nebo skupiny pro správu. |
> | Action | Microsoft. spotřeb/rozpočty/číst | Vypíše rozpočty podle předplatného nebo skupiny pro správu. |
> | Action | Microsoft. spotřeb/rozpočty/zápis | Vytvoří a aktualizuje rozpočty pomocí předplatného nebo skupiny pro správu. |
> | Action | Microsoft. spotřeba/poplatky/čtení | Poplatky za seznam |
> | Action | Microsoft. spotřeba/kredity/čtení | Zobrazit kredity |
> | Action | Microsoft. spotřeb/Events/číst | Události seznamu |
> | Action | Microsoft. spotřeb/prognózy/číst | Vypsat předpovědi |
> | Action | Microsoft. spotřeba/dávky/čtení | Vypsat spousty |
> | Action | Microsoft. spotřeb/Marketplace/číst | Vypíše podrobnosti o využití prostředků Marketplace v oboru pro předplatná EA a WebDirect. |
> | Action | Microsoft. spotřeb/operationresults/Read | Seznam operationresults |
> | Action | Microsoft. spotřeb/Operations/Read | Vypíše všechny podporované operace od poskytovatele prostředků Microsoft. spotřeb. |
> | Action | Microsoft.Consumption/operationstatus/read | Seznam stav operationstatus |
> | Action | Microsoft. spotřeb/pricesheets/Read | Vypíše Pricesheets data pro předplatné nebo skupinu pro správu. |
> | Action | Microsoft. spotřeba/registrace/akce | Registrovat do RP spotřeby |
> | Action | Microsoft. spotřeb/reservationDetails/Read | Seznam podrobností o využití pro rezervované instance podle pořadí rezervací nebo skupin pro správu. Data podrobností jsou na úrovni jednotlivých instancí a dnů. |
> | Action | Microsoft. spotřeb/reservationRecommendations/Read | Seznamte se s jedním nebo sdíleným doporučením pro rezervované instance pro předplatné. |
> | Action | Microsoft. spotřeb/reservationSummaries/Read | Vypíše Souhrn využití rezervovaných instancí podle pořadí rezervací nebo skupin pro správu. Souhrnná data jsou buď na úrovni měsíčního, nebo na denní úrovni. |
> | Action | Microsoft. spotřeb/reservationTransactions/Read | Vypíše historii transakcí pro rezervované instance podle skupin pro správu. |
> | Action | Microsoft. spotřeb/Tags/číst | Vypíše značky pro EA a Subscriptions. |
> | Action | Microsoft. spotřeba/klienti/čtení | Seznam tenantů |
> | Action | Microsoft. spotřeba/klienti/registrace/akce | Zaregistrujte akci pro obor Microsoft. spotřebujte u tenanta. |
> | Action | Microsoft. spotřeb/terms/Read | Zobrazí seznam podmínek předplatného nebo skupiny pro správu. |
> | Action | Microsoft. spotřeb/usageDetails/Read | Vypíšete podrobnosti o využití oboru pro předplatné EA a WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec do konkrétního kontejneru. |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Získá protokoly pro konkrétní kontejner. |
> | Action | Microsoft.ContainerInstance/containerGroups/delete | Odstraňte konkrétní skupinu kontejnerů. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro skupinu kontejnerů. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro skupinu kontejnerů. |
> | Action | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro skupinu kontejnerů. |
> | Action | Microsoft.ContainerInstance/containerGroups/read | Získá všechny skupiny kontejnerů. |
> | Action | Microsoft.ContainerInstance/containerGroups/restart/action | Restartuje konkrétní skupinu kontejnerů. |
> | Action | Microsoft.ContainerInstance/containerGroups/start/action | Spustí konkrétní skupinu kontejnerů. |
> | Action | Microsoft.ContainerInstance/containerGroups/stop/action | Zastaví konkrétní skupinu kontejnerů. Výpočetní prostředky budou uvolněny a fakturace se zastaví. |
> | Action | Microsoft.ContainerInstance/containerGroups/write | Vytvoří nebo aktualizuje konkrétní skupinu kontejnerů. |
> | Action | Microsoft.ContainerInstance/locations/cachedImages/read | Načte image v mezipaměti pro odběr v oblasti. |
> | Action | Microsoft. ContainerInstance/umístění/možnosti/číst | Získejte možnosti pro oblast. |
> | Action | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoft. ContainerInstance, že se odstraňuje virtuální síť nebo podsíť. |
> | Action | Microsoft.ContainerInstance/locations/usages/read | Získejte využití konkrétní oblasti. |
> | Action | Microsoft.ContainerInstance/register/action | Zaregistruje předplatné pro poskytovatele prostředků instance kontejneru a povolí vytváření skupin kontejnerů. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontroluje, zda je název registru kontejneru k dispozici pro použití. |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoftu. ContainerRegistry, že se odstraňuje virtuální síť nebo podsíť. |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | Načte výsledek asynchronní operace. |
> | Action | Microsoft.ContainerRegistry/operations/read | Zobrazí seznam všech dostupných Azure Container Registrych operací REST API. |
> | Action | Microsoft.ContainerRegistry/register/action | Zaregistruje předplatné pro poskytovatele prostředků registru kontejneru a povolí vytváření registrů kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/artifacts/delete | Odstraňte artefakt v registru kontejnerů. |
> | Action | Microsoft. ContainerRegistry/Registry/Builds/Cancel/Action | Zruší existující sestavení. |
> | Action | Microsoft. ContainerRegistry/Registry/Build/getLogLink/Action | Načte odkaz pro stažení protokolů sestavení. |
> | Action | Microsoft. ContainerRegistry/Registry/Build/Read | Získá vlastnosti zadaného sestavení nebo Vypíše všechna sestavení pro zadaný registr kontejnerů. |
> | Action | Microsoft. ContainerRegistry/Registry/Build/Write | Aktualizuje sestavení pro registr kontejneru se zadanými parametry. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/delete | Odstraní úlohu sestavení z registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Obsahuje seznam vlastností správy zdrojového kódu pro úlohu sestavení. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/read | Získá vlastnosti zadané úlohy sestavení nebo zobrazí seznam všech úloh sestavení pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Odstraní krok sestavení z úlohy sestavení. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Vypíše argumenty sestavení pro krok sestavení včetně tajných argumentů. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Získá vlastnosti zadaného kroku sestavení nebo zobrazí seznam všech kroků sestavení pro zadanou úlohu sestavení. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Vytvoří nebo aktualizuje krok sestavení pro úlohu sestavení se zadanými parametry. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/write | Vytvoří nebo aktualizuje úlohu sestavení pro registr kontejneru se zadanými parametry. |
> | Action | Microsoft.ContainerRegistry/registries/delete | Odstraní registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Odstraní filtr Event gridu z registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Získá vlastnosti zadaného filtru Event gridu nebo vypíše všechny filtry služby Event Grid pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event gridu pro registr kontejneru se zadanými parametry. |
> | Action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Získá umístění pro odeslání pro uživatele, aby mohl odeslat zdroj. |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | Importujte image do registru kontejneru se zadanými parametry. |
> | Action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Získá umístění adresy URL pro nahrání zdroje pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | Zobrazí seznam přihlašovacích údajů pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | Zobrazí seznam zásad pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | Vypíše využití kvót pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/metadata/read | Načte metadata konkrétního úložiště pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/metadata/write | Aktualizuje metadata úložiště pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | Načte stav asynchronní operace v registru. |
> | Action | Microsoft.ContainerRegistry/registries/pull/read | Vyžádání nebo získání imagí z registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/push/write | Vložení nebo zápis imagí do registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/quarantineRead/read | Vyžádání nebo získání imagí v karanténě z registru kontejnerů |
> | Action | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Zápis/Změna stavu karantény imagí v karanténě |
> | Action | Microsoft.ContainerRegistry/registries/queueBuild/action | Vytvoří nové sestavení založené na parametrech žádosti a přidá je do fronty sestavení. |
> | Action | Microsoft.ContainerRegistry/registries/read | Získá vlastnosti zadaného registru kontejneru nebo zobrazí seznam všech registrů kontejnerů v rámci zadané skupiny prostředků nebo předplatného. |
> | Action | Microsoft. ContainerRegistry/Registry/regenerateCredential/Action | Znovu vygeneruje jedno z přihlašovacích údajů pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/replications/delete | Odstraní replikaci z registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Získá stav asynchronní operace replikace. |
> | Action | Microsoft.ContainerRegistry/registries/replications/read | Získá vlastnosti zadané replikace nebo vypíše všechny replikace určeného registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/replications/write | Vytvoří nebo aktualizuje replikaci pro registr kontejneru se zadanými parametry. |
> | Action | Microsoft. ContainerRegistry/Registry/spuštění/zrušení/akce | Zruší existující běh. |
> | Action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Získá adresu URL protokolu SAS protokolu pro běh. |
> | Action | Microsoft.ContainerRegistry/registries/runs/read | Získá vlastnosti spuštění proti registru kontejneru nebo spuštění seznamu. |
> | Action | Microsoft.ContainerRegistry/registries/runs/write | Aktualizuje běh. |
> | Action | Microsoft.ContainerRegistry/registries/scheduleRun/action | Naplánování spuštění proti registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/sign/write | Metadata vztahu důvěryhodnosti nabízených nebo vyžádaného obsahu pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/delete | Odstraní úlohu pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Zobrazí seznam všech podrobností úlohy pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/read | Načte úlohu pro registr kontejneru nebo seznam všech úkolů. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/write | Vytvoří nebo aktualizuje úlohu pro registr kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizuje zásady pro zadaný registr kontejnerů. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/delete | Odstraní Webhook z registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Získá konfiguraci identifikátoru URI služby a vlastních hlaviček pro Webhook. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Vypíše nedávné události pro zadaný Webhook. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Získá stav asynchronní operace Webhooku. |
> | Action | Microsoft. ContainerRegistry/Registry/webhooks/test/Action | Aktivuje událost testu, která se pošle Webhooku. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/read | Získá vlastnosti zadaného Webhooku nebo vypíše všechny Webhooky zadaného registru kontejneru. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/write | Vytvoří nebo aktualizuje Webhook pro registr kontejneru se zadanými parametry. |
> | Action | Microsoft.ContainerRegistry/registries/write | Vytvoří nebo aktualizuje registr kontejneru se zadanými parametry. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ContainerService/containerServices/delete | Odstraní službu kontejneru. |
> | Action | Microsoft.ContainerService/containerServices/read | Získat službu kontejneru |
> | Action | Microsoft.ContainerService/containerServices/write | Vytvoří novou službu kontejneru nebo aktualizuje stávající. |
> | Action | Microsoft.ContainerService/locations/operationresults/read | Získá stav výsledku asynchronní operace. |
> | Action | Microsoft.ContainerService/locations/operations/read | Získá stav asynchronní operace. |
> | Action | Microsoft.ContainerService/locations/orchestrators/read | Vypisuje podporované orchestrace. |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Získání profilu přístupu spravovaného clusteru podle názvu role pomocí přihlašovacích údajů pro seznam |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | Získání profilu přístupu spravovaného clusteru podle názvu role |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/delete | Odstraní fond agentů. |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/read | Načte fond agentů. |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/write | Vytvoří nový fond agentů nebo aktualizuje stávající. |
> | Action | Microsoft.ContainerService/managedClusters/delete | Odstraní spravovaný cluster. |
> | Action | Microsoft. ContainerService/managedClusters/detektory/číst | Získat detektor spravovaného clusteru |
> | Action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Výpis přihlašovacích údajů clusterAdmin spravovaného clusteru |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Výpis přihlašovacích údajů clusterUser spravovaného clusteru |
> | Action | Microsoft.ContainerService/managedClusters/read | Získání spravovaného clusteru |
> | Action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Resetovat profil AAD spravovaného clusteru |
> | Action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Resetování hlavního profilu služby spravovaného clusteru |
> | Action | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Získá profil upgradu clusteru. |
> | Action | Microsoft.ContainerService/managedClusters/write | Vytvoří nový spravovaný cluster nebo aktualizuje stávající. |
> | Action | Microsoft.ContainerService/openShiftClusters/delete | Odstraní otevřený cluster Shift. |
> | Action | Microsoft.ContainerService/openShiftClusters/read | Získat otevřený cluster Shift |
> | Action | Microsoft.ContainerService/openShiftClusters/write | Vytvoří nový cluster Open Shift nebo aktualizuje stávající. |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/delete | Odstranění spravovaného clusteru s otevřeným Shift |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/read | Získání spravovaného clusteru s otevřeným Shift |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/write | Vytvoří nový spravovaný cluster s otevřeným Shift nebo aktualizuje stávající. |
> | Action | Microsoft.ContainerService/operations/read | Zobrazí seznam operací dostupných v poskytovateli prostředků Microsoft. ContainerService. |
> | Action | Microsoft.ContainerService/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. ContainerService. |
> | Action | Microsoft.ContainerService/unregister/action | Zruší registraci předplatného u poskytovatele prostředků Microsoft. ContainerService. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ContentModerator/applications/delete | Odstranit operaci |
> | Action | Microsoft. ContentModerator/aplikace/listSecrets/Action | Výpis tajných kódů |
> | Action | Microsoft. ContentModerator/aplikace/listSingleSignOnToken/Action | Číst tokeny jednotného přihlašování |
> | Action | Microsoft.ContentModerator/applications/read | Operace čtení |
> | Action | Microsoft.ContentModerator/applications/write | Operace zápisu |
> | Action | Microsoft.ContentModerator/applications/write | Operace zápisu |
> | Action | Microsoft.ContentModerator/listCommunicationPreference/action | Vypsat předvolby komunikace |
> | Action | Microsoft.ContentModerator/operations/read | operace čtení |
> | Action | Microsoft.ContentModerator/updateCommunicationPreference/action | Aktualizace předvolby komunikace |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.CostManagement/cloudConnectors/delete | Odstraní zadaný cloudConnector. |
> | Action | Microsoft.CostManagement/cloudConnectors/read | Vypíše seznam cloudConnectors pro ověřeného uživatele. |
> | Action | Microsoft.CostManagement/cloudConnectors/write | Vytvoří nebo aktualizuje zadaný cloudConnector. |
> | Action | Microsoft. CostManagement/Dimensions/Read | Vypíše všechny podporované dimenze podle oboru. |
> | Action | Microsoft. CostManagement/EXPORTS/Action | Spusťte zadaný export. |
> | Action | Microsoft.CostManagement/exports/delete | Odstraní zadaný export. |
> | Action | Microsoft.CostManagement/exports/read | Vypíše export podle oboru. |
> | Action | Microsoft. CostManagement/EXPORTS/Run/Action | Spusťte exporty. |
> | Action | Microsoft.CostManagement/exports/write | Vytvoří nebo aktualizuje zadaný export. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Vypíše externalSubscriptionsy v rámci externalBillingAccount pro ověřeného uživatele. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/read | Vypíše seznam externalBillingAccounts pro ověřeného uživatele. |
> | Action | Microsoft. CostManagement/externalSubscriptions/Read | Vypíše seznam externalSubscriptions pro ověřeného uživatele. |
> | Action | Microsoft. CostManagement/externalSubscriptions/Write | Aktualizovat přidruženou skupinu pro správu externalSubscription |
> | Action | Microsoft. CostManagement/dotaz/akce | Dotazování dat o využití podle oboru |
> | Action | Microsoft.CostManagement/query/read | Dotazování dat o využití podle oboru |
> | Action | Microsoft.CostManagement/register/action | Zaregistrujte akci pro obor Microsoft. CostManagement v rámci předplatného. |
> | Action | Microsoft. CostManagement/Reports/Action | Plánování sestav o využití dat podle oboru |
> | Action | Microsoft.CostManagement/reports/read | Plánování sestav o využití dat podle oboru |
> | Action | Microsoft.CostManagement/tenants/register/action | Zaregistrujte akci pro obor Microsoft. CostManagement v tenantovi. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/action | Vytvořit nebo aktualizovat jakákoli metadata Azure Customer Insights Adobe |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/read | Přečtěte si všechna metadata Azure Customer Insights Adobe |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Odstraní všechny zásady sdíleného přístupového podpisu Azure Customer Insights. |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Načte všechny zásady sdíleného přístupového podpisu Azure Customer Insights. |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Znovu vygenerovat primární klíč zásad sdíleného přístupového podpisu Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Znovu vygenerovat sekundární klíč zásad sdíleného přístupového podpisu Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Vytvoří nebo aktualizuje všechny zásady sdíleného přístupového podpisu Azure Customer Insights. |
> | Action | Microsoft. CustomerInsights/Centers/Connectors/Activate/Action | Aktivovat libovolný konektor Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/Centers/Connectors/Activate/Action | Aktivovat libovolný konektor Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/delete | Odstranit libovolný konektor Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Získat stav modulu runtime konektoru služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktivovat všechna mapování konektoru Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Odstranit všechna mapování konektoru Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Přečtěte si výsledek operace mapování konektoru služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Přečíst jakékoli mapování konektoru Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Vytvoří nebo aktualizuje jakékoli mapování služby Azure Customer Insights Connector. |
> | Action | Microsoft.CustomerInsights/hubs/connectors/operations/read | Přečtěte si výsledek operace konektoru služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/read | Číst libovolný konektor Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Vytvořit nebo aktualizovat libovolný informace o ověřování konektoru služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/update/action | Aktualizovat libovolný konektor Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/write | Vytvořit nebo aktualizovat libovolný konektor Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/action | Vytvoření nebo aktualizace metadat Azure Customer Insights CRM |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/read | Čtení libovolných metadat Azure Customer Insights CRM |
> | Action | Microsoft.CustomerInsights/hubs/delete | Odstranění jakéhokoli centra Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/delete | Odstranění jakékoli služby Azure Customer Insights Gdpr |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/read | Přečtěte si všechny služby Azure Customer Insights Gdpr |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/write | Vytvořit nebo aktualizovat libovolný Gdpr Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Získání fakturačních kreditů služby Azure Customer Insights hub |
> | Action | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Získat historii fakturace centra Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/images/delete | Odstranění libovolné image Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/images/read | Číst libovolný obrázek Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/images/write | Vytvoří nebo aktualizuje libovolný obrázek Azure Customer Insights. |
> | Action | Microsoft.CustomerInsights/hubs/interactions/delete | Odstranění jakýchkoli interakcí služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/operations/read | Přečtěte si výsledek operace interakce služby Azure Customer Insights. |
> | Action | Microsoft.CustomerInsights/hubs/interactions/read | Čtení jakékoliv interakce služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Navrhněte odkazy na vztahy pro jakékoli interakce Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/write | Vytvoření nebo aktualizace jakékoli interakce služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/delete | Odstranit libovolný klíčový ukazatel výkonu pro Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/operations/read | Přečtěte si výsledek operace klíčových ukazatelů výkonu Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/read | Číst libovolný klíčový ukazatel výkonu Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Znovu zpracovat všechny klíčové ukazatele výkonu pro Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/write | Vytvoří nebo aktualizuje libovolný klíčový ukazatel výkonu Azure Customer Insights. |
> | Action | Microsoft.CustomerInsights/hubs/links/delete | Odstranění jakýchkoli odkazů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/links/operations/read | Načíst jakýkoli výsledek operace Azure Customer Insights Links |
> | Action | Microsoft.CustomerInsights/hubs/links/read | Čtení libovolných odkazů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/links/write | Vytvořit nebo aktualizovat jakékoli odkazy na zákazníky Azure |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/action | Vytvořit nebo aktualizovat jakákoli metadata služby Azure Customer Insights MSE |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/read | Číst všechna metadata Azure Customer Insights MSE |
> | Action | Microsoft.CustomerInsights/hubs/operationresults/read | Získat výsledky operace Azure Customer Insights hub |
> | Action | Microsoft.CustomerInsights/hubs/predictions/delete | Odstranění jakékoli služby Azure Customer Insights předpovědi |
> | Action | Microsoft.CustomerInsights/hubs/predictions/operations/read | Přečtěte si výsledek operace Azure Customer Insights předpovědi |
> | Action | Microsoft.CustomerInsights/hubs/predictions/read | Přečtěte si všechny služby Azure Customer Insights předpovědi |
> | Action | Microsoft.CustomerInsights/hubs/predictions/write | Vytvoření nebo aktualizace jakýchkoli zákaznických předpovědi Azure |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Odstraňte všechny zásady prediktivního porovnávání z Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Přečtěte si výsledek operace zásad prediktivního porovnávání služby Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Přečtěte si všechny zásady prediktivního porovnávání z Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Vytvořit nebo aktualizovat všechny zásady prediktivního porovnávání v Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/delete | Odstranit jakýkoliv profil Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/operations/read | Načíst jakýkoliv výsledek operace profilu Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/read | Číst libovolný profil Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/write | Zápis libovolného profilu Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/Centers/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. CustomerInsights/Centers/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. CustomerInsights/Centers/Providers/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro prostředek. |
> | Action | Microsoft. CustomerInsights/Centers/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro prostředek. |
> | Action | Microsoft.CustomerInsights/hubs/read | Čtení libovolného centra Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Odstranit všechny odkazy na relace Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Přečtěte si výsledek operace propojení relací Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Přečíst všechny odkazy na relace Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Vytvořit nebo aktualizovat jakékoli odkazy na relaci Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/delete | Odstranění všech relací Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/operations/read | Přečtěte si výsledek operace v relaci Azure Customer Insights. |
> | Action | Microsoft.CustomerInsights/hubs/relationships/read | Čtení libovolných vztahů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/write | Vytvoření nebo aktualizace jakýchkoli vztahů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Odstranit všechna přiřazení služby Azure Customer Insights RBAC |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Přečtěte si výsledek operace přiřazení služby RBAC pro Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/read | Číst jakékoli přiřazení služby Azure Customer Insights RBAC |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/write | Vytvořit nebo aktualizovat jakékoli přiřazení služby Azure Customer Insights RBAC |
> | Action | Microsoft.CustomerInsights/hubs/roles/read | Přečtěte si všechny role Azure Customer Insights RBAC |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Vytvoření nebo aktualizace libovolných metadat služby Azure Customer Insights SalesForce |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Čtení libovolných metadat služby Azure Customer Insights SalesForce |
> | Action | Microsoft.CustomerInsights/hubs/segments/delete | Odstranění libovolných segmentů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Správa všech dynamických segmentů Azure Customer Insight |
> | Action | Microsoft.CustomerInsights/hubs/segments/read | Čtení libovolných segmentů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/segments/static/action | Správa všech statických segmentů zákaznických přehledů Azure |
> | Action | Microsoft.CustomerInsights/hubs/segments/write | Vytvoření nebo aktualizace libovolných segmentů Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Odstranit všechny služby Azure Customer Insights SqlConnectionString |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Přečtěte si všechny služby Azure Customer Insights SqlConnectionString |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Vytvoří nebo aktualizuje všechny služby Azure Customer Insights SqlConnectionString. |
> | Action | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Vygenerovat schéma typu návrhu z ukázkových dat |
> | Action | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Správa nastavení centra Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/delete | Odstranění libovolného zobrazení aplikace Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/read | Čtení libovolného zobrazení aplikace Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/write | Vytvoří nebo aktualizuje jakékoli zobrazení Azure Customer Insights App. |
> | Action | Microsoft.CustomerInsights/hubs/widgettypes/read | Přečtěte si libovolné typy widgetů pro Azure Customer Insights App |
> | Action | Microsoft.CustomerInsights/hubs/write | Vytvoření nebo aktualizace libovolného centra Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/operations/read | Číst metadata rozhraní API pro Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/register/action | Zaregistruje předplatné pro poskytovatele prostředků Customer Insights a povolí vytváření prostředků Customer Insights. |
> | Action | Microsoft.CustomerInsights/unregister/action | Zruší registraci předplatného pro Customer Insights poskytovatele prostředků. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. DataBox/Jobs/bookShipmentPickUp/Action | Umožňuje rezervovat vyzvednutí pro návratové dodávky. |
> | Action | Microsoft. DataBox/Jobs/Cancel/Action | Zruší probíhající objednávku. |
> | Action | Microsoft.DataBox/jobs/delete | Odstranit objednávky |
> | Action | Microsoft. DataBox/Jobs/listCredentials/Action | Zobrazí nešifrované přihlašovací údaje související s objednávkou. |
> | Action | Microsoft. DataBox/Jobs/číst | Vypsat nebo získat objednávky |
> | Action | Microsoft.DataBox/jobs/write | Vytvořit nebo aktualizovat objednávky |
> | Action | Microsoft. DataBox/Locations/availableSkus/Action | Tato metoda vrátí seznam dostupných skladových položek. |
> | Action | Microsoft. DataBox/Locations/availableSkus/Read | Vypsat nebo získat dostupné SKU |
> | Action | Microsoft.DataBox/locations/operationResults/read | Vypsat nebo získat výsledky operace |
> | Action | Microsoft. DataBox/Locations/validateAddress/Action | Ověří dodací adresu a v případě potřeby poskytne alternativní adresy. |
> | Action | Microsoft. DataBox/Locations/validateInputs/Action |  |
> | Action | Microsoft.DataBox/register/action | Registrovat poskytovatele Microsoft. Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Zobrazí nebo získá výstrahy. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Zobrazí nebo získá výstrahy. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Odstraní plány šířky pásma. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Zobrazí nebo získá plány šířky pásma. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Zobrazí nebo získá plány šířky pásma. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Vytvoří nebo aktualizuje plány šířky pásma. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Odstraní zařízení Data Box Edge. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Stáhnout aktualizace v zařízení |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Načte rozšířené informace o prostředku. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalovat aktualizace na zařízení |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Zobrazí nebo získá úlohy. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Zobrazí nebo získá nastavení sítě zařízení. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Zobrazí nebo získá stav operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Odstraní objednávky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Zobrazí nebo získá objednávky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Zobrazí nebo získá objednávky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Vytvoří nebo aktualizuje objednávky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Zobrazí nebo získá Data Box Edge zařízení. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Zobrazí nebo získá Data Box Edge zařízení. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Zobrazí nebo získá Data Box Edge zařízení. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Odstraní role. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Zobrazí nebo získá role. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Zobrazí nebo získá role. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Vytvoří nebo aktualizuje role. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Vyhledat aktualizace |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizovat nastavení zabezpečení |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Odstraní sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Zobrazí nebo získá sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Zobrazí nebo získá sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Obnovte metadata sdílení s daty z cloudu. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Vytvoří nebo aktualizuje sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Odstraní přihlašovací údaje účtu úložiště. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Zobrazí nebo získá přihlašovací údaje účtu úložiště. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Zobrazí nebo získá přihlašovací údaje účtu úložiště. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Vytvoří nebo aktualizuje přihlašovací údaje účtu úložiště. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Odstraní triggery. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Zobrazí nebo získá aktivační události. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Zobrazí nebo získá aktivační události. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Vytvoří nebo aktualizuje triggery. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Zobrazí nebo získá souhrn aktualizace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Nahrát certifikát pro registraci zařízení |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Odstraní uživatele sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Zobrazí nebo získá výsledek operace. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Zobrazí nebo získá uživatele sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Zobrazí nebo získá uživatele sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Vytvoří nebo aktualizuje uživatele sdílené složky. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Vytvoří nebo aktualizuje Data Box Edge zařízení. |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Vytvoří nebo aktualizuje Data Box Edge zařízení. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. datacihly/registrovat/akce | Zaregistrujte se do datacihl. |
> | Action | Microsoft. datacihly/pracovní prostory/odstranit | Odebere pracovní prostor datacihly. |
> | Action | Microsoft. datacihly/pracovní prostory/poskytovatelé/Microsoft. Insights/diagnosticSettings/Read | Nastaví dostupná nastavení diagnostiky pro pracovní prostor datacihly. |
> | Action | Microsoft. datacihly/pracovní prostory/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Přidat nebo upravit nastavení diagnostiky. |
> | Action | Microsoft. datacihly/pracovní prostory/poskytovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné definice protokolů pro pracovní prostor datacihly. |
> | Action | Microsoft. datacihly/pracovní prostory/čtení | Načte seznam pracovních prostorů datacihlů. |
> | Action | Microsoft. datacihly/pracovní prostory/zápis | Vytvoří pracovní prostor datacihly. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DataCatalog/catalogs/delete | Odstraní prostředek catalogs pro poskytovatele prostředků Data Catalog. |
> | Action | Microsoft.DataCatalog/catalogs/read | Číst prostředky katalogu pro Data Catalog poskytovatele prostředků |
> | Action | Microsoft.DataCatalog/catalogs/write | Zapsat prostředek katalogů pro Data Catalog poskytovatele prostředků |
> | Action | Microsoft.DataCatalog/checkNameAvailability/read | Ověřte dostupnost názvu katalogu pro Data Catalog poskytovatele prostředků. |
> | Action | Microsoft.DataCatalog/datacatalogs/delete | Odstraní prostředek datacatalog pro poskytovatele prostředků Data Catalog. |
> | Action | Microsoft.DataCatalog/datacatalogs/read | Načte prostředek datacatalog pro poskytovatele prostředků Data Catalog. |
> | Action | Microsoft.DataCatalog/datacatalogs/write | Zápis prostředku datacatalog pro poskytovatele prostředků Data Catalog. |
> | Action | Microsoft.DataCatalog/operations/read | Přečte všechny dostupné operace v Data Catalog poskytovatel prostředků. |
> | Action | Microsoft.DataCatalog/register/action | Registrace předplatného pro Data Catalog poskytovatele prostředků |
> | Action | Microsoft.DataCatalog/unregister/action | Zrušení registrace předplatného pro Data Catalog poskytovatele prostředků |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Kontroluje, zda je název Data Factory k dispozici pro použití. |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | Přečte okna aktivity v Data Factory se zadanými parametry. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Přečte okna aktivity pro aktivitu kanálu se zadanými parametry. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Přečte okna aktivity pro kanál se zadanými parametry. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/delete | Odstraní všechny kanály. |
> | Action | Microsoft. DataFactory/DataFactory/datapipelines/Pause/Action | Pozastaví všechny kanály. |
> | Action | Microsoft. DataFactory/DataFactory/datapipelines/Read | Přečte libovolný kanál. |
> | Action | Microsoft. DataFactory/DataFactory/Process/Resume/Action | Obnoví všechny kanály. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje všechny kanály. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/write | Vytvoří nebo aktualizuje libovolný kanál. |
> | Action | Microsoft. DataFactory/DataFactory/activitywindows/Read | Přečte okna aktivity pro datovou sadu se zadanými parametry. |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | Odstraní libovolnou datovou sadu. |
> | Action | Microsoft. DataFactory/DataFactory/DataSets/číst | Přečte libovolnou datovou sadu. |
> | Action | Microsoft. DataFactory/DataFactory/sliceruns/Read | Přečte spuštění datového řezu pro danou datovou sadu s daným počátečním časem. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/read | Načte datové řezy v daném období. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizujte stav datového řezu. |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | Vytvoří nebo aktualizuje libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/datafactories/delete | Odstraní Data Factory. |
> | Action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Přečte informace o připojení pro každou bránu. |
> | Action | Microsoft.DataFactory/datafactories/gateways/delete | Odstraní libovolnou bránu. |
> | Action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Zobrazuje seznam ověřovacích klíčů pro každou bránu. |
> | Action | Microsoft.DataFactory/datafactories/gateways/read | Přečte libovolnou bránu. |
> | Action | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Znovu vygeneruje ověřovací klíče pro každou bránu. |
> | Action | Microsoft.DataFactory/datafactories/gateways/write | Vytvoří nebo aktualizuje libovolnou bránu. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/delete | Odstraní veškerou propojenou službu. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/read | Přečte jakoukoli propojenou službu. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/write | Vytvoří nebo aktualizuje jakoukoli propojenou službu. |
> | Action | Microsoft.DataFactory/datafactories/read | Přečte Data Factory. |
> | Action | Microsoft.DataFactory/datafactories/runs/loginfo/read | Přečte identifikátor URI SAS pro kontejner objektů BLOB obsahující protokoly. |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | Odstraní libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/datafactories/tables/read | Přečte libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/datafactories/tables/write | Vytvoří nebo aktualizuje libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/datafactories/write | Vytvoří nebo aktualizuje Data Factory. |
> | Action | Microsoft. DataFactory/Factory/cancelpipelinerun/Action | Zruší běh kanálu určený IDENTIFIKÁTORem spuštění. |
> | Action | Microsoft. DataFactory/Factory/createdataflowdebugsession/Action | Vytvoří relaci ladění toku dat. |
> | Action | Microsoft.DataFactory/factories/dataflows/delete | Odstraní tok dat. |
> | Action | Microsoft.DataFactory/factories/dataflows/read | Přečte tok dat. |
> | Action | Microsoft.DataFactory/factories/dataflows/write | Vytvořit nebo aktualizovat tok dat |
> | Action | Microsoft.DataFactory/factories/datasets/delete | Odstraní libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/factories/datasets/read | Přečte libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/factories/datasets/write | Vytvoří nebo aktualizuje libovolnou datovou sadu. |
> | Action | Microsoft.DataFactory/factories/delete | Odstraní Data Factory. |
> | Action | Microsoft. DataFactory/Factory/deletedataflowdebugsession/Action | Odstraní relaci ladění toku dat. |
> | Action | Microsoft. DataFactory/Factory/getDataPlaneAccess/Action | Získá přístup k službě ADF (dataplaning Service). |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Přečte přístup k službě ADF (dataplaning Service). |
> | Action | Microsoft. DataFactory/Factory/getFeatureValue/Action | Získat hodnotu funkce řízení expozice pro konkrétní umístění. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/read | Přečte hodnotu funkce řízení expozice pro konkrétní umístění. |
> | Action | Microsoft. DataFactory/Factory/getGitHubAccessToken/Action | Získá přístupový token GitHubu. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | Odstraní všechny Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Přečte Integration Runtime informace o připojení. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Získá metadata SSIS Integration Runtime pro zadaný Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Přečte stav Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Vytvoří odkaz na propojenou Integration Runtime v zadaném sdíleném Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Zobrazí seznam ověřovacích klíčů pro všechny Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Načte data monitorování pro všechny Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Odstraní uzel pro zadaný Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Vrátí IP adresu pro zadaný uzel Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Přečte uzel pro zadaný Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje uzel Integration Runtime v místním prostředí. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | Přečte všechny Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Aktualizuje SSIS metadata Integration Runtime pro zadaný Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Znovu vygeneruje ověřovací klíče pro zadaný Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Odebere propojené Integration Runtime odkazy ze zadaného Integration Runtime. |
> | Action | Microsoft. DataFactory/Factory/integrationruntimes/Start/Action | Spustí všechny Integration Runtime. |
> | Action | Microsoft. DataFactory/Factory/integrationruntimes/stop/Action | Zastaví všechny Integration Runtime. |
> | Action | Microsoft. DataFactory/Factory/integrationruntimes/synccredentials/Action | Synchronizuje pověření pro zadaný Integration Runtime. |
> | Action | Microsoft. DataFactory/Factory/integrationruntimes/upgrade/Action | Upgraduje zadaný Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | Vytvoří nebo aktualizuje jakékoli Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/linkedServices/delete | Odstraní propojenou službu. |
> | Action | Microsoft.DataFactory/factories/linkedServices/read | Načte propojenou službu. |
> | Action | Microsoft.DataFactory/factories/linkedServices/write | Vytvoří nebo aktualizuje propojenou službu. |
> | Action | Microsoft. DataFactory/Factory/pipelineruns/activityruns/Read | Přečte spuštění aktivit pro zadané ID spuštění kanálu. |
> | Action | Microsoft. DataFactory/Factory/pipelineruns/Cancel/Action | Zruší běh kanálu určený IDENTIFIKÁTORem spuštění. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Dotazuje spuštění aktivity pro zadané ID spuštění kanálu. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Přečte výsledek spuštění aktivity dotazu pro zadané ID spuštění kanálu. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/read | Přečte spuštění kanálu. |
> | Action | Microsoft. DataFactory/factorys/Pipelines/createrun/Action | Vytvoří běh pro kanál. |
> | Action | Microsoft.DataFactory/factories/pipelines/delete | Odstraní kanál. |
> | Action | Microsoft. DataFactory/factorys/Pipelines/pipelineruns/activityruns/průběh/čtení | Získá průběh spuštění aktivity. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Přečte běh kanálu. |
> | Action | Microsoft.DataFactory/factories/pipelines/read | Přečte kanál. |
> | Action | Microsoft.DataFactory/factories/pipelines/write | Vytvořit nebo aktualizovat kanál |
> | Action | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Provede dotaz na spuštění kanálu ladění. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/action | Provede dotaz na spuštění kanálu. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/read | Přečte výsledek spuštění kanálu dotazů. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/action | Provede dotaz na spuštění triggeru. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/read | Přečte výsledek spuštění triggeru. |
> | Action | Microsoft.DataFactory/factories/read | Přečte Data Factory. |
> | Action | Microsoft. DataFactory/Factory/startdataflowdebugsession/Action | Spustí relaci ladění toku dat. |
> | Action | Microsoft.DataFactory/factories/triggerruns/read | Přečte spuštění triggeru. |
> | Action | Microsoft.DataFactory/factories/triggers/delete | Odstraní všechny triggery. |
> | Action | Microsoft.DataFactory/factories/triggers/read | Přečte všechny triggery. |
> | Action | Microsoft. DataFactory/Factory/Triggers/Start/Action | Spustí jakoukoli aktivační událost. |
> | Action | Microsoft. DataFactory/Factory/Triggers/stop/Action | Zastaví všechny triggery. |
> | Action | Microsoft.DataFactory/factories/triggers/triggerruns/read | Přečte spuštění triggeru. |
> | Action | Microsoft.DataFactory/factories/triggers/write | Vytvoří nebo aktualizuje libovolné triggery. |
> | Action | Microsoft.DataFactory/factories/write | Vytvořit nebo aktualizovat Data Factory |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | Nakonfiguruje úložiště pro objekt pro vytváření. |
> | Action | Microsoft. DataFactory/Locations/getFeatureValue/Action | Získat hodnotu funkce řízení expozice pro konkrétní umístění. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/read | Přečte hodnotu funkce řízení expozice pro konkrétní umístění. |
> | Action | Microsoft.DataFactory/operations/read | Přečte všechny operace poskytovatele Microsoft Data Factory. |
> | Action | Microsoft.DataFactory/register/action | Zaregistruje předplatné pro poskytovatele prostředků Data Factory. |
> | Action | Microsoft.DataFactory/unregister/action | Zruší registraci předplatného pro Data Factory poskytovatele prostředků. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. DataLakeAnalytics/Accounts/computePolicies/DELETE | Odstraňte výpočetní zásadu. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/computePolicies/Read | Získejte informace o výpočetních zásadách. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/computePolicies/Write | Vytvořte nebo aktualizujte výpočetní zásadu. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Odpojte účet DataLakeStore z účtu DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Získejte informace o propojeném účtu DataLakeStore účtu DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Vytvoří nebo aktualizuje propojený účet DataLakeStore účtu DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/delete | Odstraňte účet DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Odstraní pravidlo brány firewall. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/firewallRules/Read | Získejte informace o pravidlu brány firewall. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Vytvořte nebo aktualizujte pravidlo brány firewall. |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Získejte výsledek operace DataLakeAnalytics účtu. |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | Získejte informace o existujícím účtu DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Containers/listSasTokens/Action | Vypíše tokeny SAS pro kontejnery úložiště propojeného účtu úložiště účtu DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Containers/Read | Získá kontejnery propojeného účtu úložiště účtu DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Odpojte účet úložiště od účtu DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Read | Získejte informace o propojeném účtu úložiště účtu DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Vytvoří nebo aktualizuje propojený účet úložiště účtu DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/TakeOwnership/Action | Udělte oprávnění ke zrušení úloh odeslaných jinými uživateli. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/transferAnalyticsUnits/Action | Přenos SystemMaxAnalyticsUnits mezi účty DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Odstraní pravidlo virtuální sítě. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Získat informace o pravidle virtuální sítě. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Vytvoří nebo aktualizuje pravidlo virtuální sítě. |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | Vytvořte nebo aktualizujte účet DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/umístění/funkce/číst | Získejte informace o funkcích předplatného, které se týkají používání DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/Locations/checkNameAvailability/Action | Ověřte dostupnost názvu účtu DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | Získejte výsledek operace DataLakeAnalytics účtu. |
> | Action | Microsoft.DataLakeAnalytics/locations/usages/read | Získat informace o využití kvóty pro předplatné týkající se použití DataLakeAnalytics |
> | Action | Microsoft.DataLakeAnalytics/operations/read | Získejte dostupné operace DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/Register/Action | Zaregistrujte předplatné do DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | Odstraňte účet DataLakeStore. |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Povolte Trezor klíčů pro účet DataLakeStore. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Odstraní filtr EventGrid. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Získejte filtr EventGrid. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Vytvoří nebo aktualizuje filtr EventGrid. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/delete | Odstraní pravidlo brány firewall. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | Získejte informace o pravidlu brány firewall. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/write | Vytvořte nebo aktualizujte pravidlo brány firewall. |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | Získejte výsledek operace DataLakeStore účtu. |
> | Action | Microsoft.DataLakeStore/accounts/read | Získejte informace o existujícím účtu DataLakeStore. |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | Udělte uživateli oprávnění na Data Lake Store, pokud je udělený pomocí Microsoft. Authorization/roleAssignments/Write. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Odstraňte důvěryhodného zprostředkovatele identity. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Získejte informace o důvěryhodném zprostředkovateli identity. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Vytvořte nebo aktualizujte důvěryhodného zprostředkovatele identity. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Odstraní pravidlo virtuální sítě. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Získat informace o pravidle virtuální sítě. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Vytvoří nebo aktualizuje pravidlo virtuální sítě. |
> | Action | Microsoft.DataLakeStore/accounts/write | Vytvořte nebo aktualizujte účet DataLakeStore. |
> | Action | Microsoft. DataLakeStore/umístění/funkce/číst | Získejte informace o funkcích předplatného, které se týkají používání DataLakeStore. |
> | Action | Microsoft. DataLakeStore/Locations/checkNameAvailability/Action | Ověřte dostupnost názvu účtu DataLakeStore. |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | Získejte výsledek operace DataLakeStore účtu. |
> | Action | Microsoft.DataLakeStore/locations/usages/read | Získat informace o využití kvóty pro předplatné týkající se použití DataLakeStore |
> | Action | Microsoft.DataLakeStore/operations/read | Získejte dostupné operace DataLakeStore. |
> | Action | Microsoft.DataLakeStore/register/action | Zaregistrujte předplatné do DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. datamigration/Locations/operationResults/Read | Získání stavu dlouhotrvající operace související s přijatou odpovědí 202 |
> | Action | Microsoft.DataMigration/locations/operationStatuses/read | Získání stavu dlouhotrvající operace související s přijatou odpovědí 202 |
> | Action | Microsoft. datamigrace/registrace/akce | Zaregistruje předplatné u poskytovatele Azure Database Migration Service. |
> | Action | Microsoft. datamigration/Services/addWorker/Action | Přidá pracovníka DMS do dispozici není žádná pracovníků služby. |
> | Action | Microsoft. datamigration/Services/CheckStatus/Action | Ověřte, zda je služba nasazená a spuštěná. |
> | Action | Microsoft. datamigration/Services/configureWorker/Action | Nakonfiguruje pracovní proces DMS na dispozici není žádná pracovní procesy služby. |
> | Action | Migrace a služby Microsoft. DataServices/DELETE | Odstraní prostředek a všechny jeho podřízené položky. |
> | Action | Microsoft. datamigration/Services/Projects/accessArtifacts/Action | Vygeneruje adresu URL, která se dá použít k získání nebo vložení artefaktů projektu. |
> | Action | Microsoft.DataMigration/services/projects/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Action | Microsoft.DataMigration/services/projects/files/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Action | Microsoft.DataMigration/services/projects/files/read | Přečtěte si informace o prostředcích |
> | Action | Migrace Microsoft. DataServices/služby/projekty/soubory/čtení/akce | Získat adresu URL, která se dá použít ke čtení obsahu souboru |
> | Action | Migrace Microsoft. DataServices/služby/projekty/soubory/čtení a akce | Získat adresu URL, kterou lze použít ke čtení nebo zápisu obsahu souboru |
> | Action | Microsoft.DataMigration/services/projects/files/write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Action | Migrace Microsoft. DataServices/služby/projekty/čtení | Přečtěte si informace o prostředcích |
> | Action | Microsoft. datamigrace/služby/projekty/úkoly/zrušit/akce | Zrušit úlohu, pokud je aktuálně spuštěna |
> | Action | Microsoft.DataMigration/services/projects/tasks/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Action | Microsoft.DataMigration/services/projects/tasks/read | Přečtěte si informace o prostředcích |
> | Action | Microsoft.DataMigration/services/projects/tasks/write | Spouštění úloh Azure Database Migration Service úlohami |
> | Action | Microsoft.DataMigration/services/projects/write | Spouštění úloh Azure Database Migration Service úlohami |
> | Action | Migrace Microsoft. DataServices/služby/čtení | Přečtěte si informace o prostředcích |
> | Action | Microsoft. datamigration/Services/removeWorker/Action | Odebere pracovní proces DMS na dispozici není žádná pracovní procesy služby. |
> | Action | Microsoft. datamigration/Services/serviceTasks/Cancel/Action | Zrušit úlohu, pokud je aktuálně spuštěna |
> | Action | Microsoft.DataMigration/services/serviceTasks/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Action | Microsoft. datamigration/Services/serviceTasks/Read | Přečtěte si informace o prostředcích |
> | Action | Microsoft. datamigration/Services/serviceTasks/Write | Spouštění úloh Azure Database Migration Service úlohami |
> | Action | Microsoft.DataMigration/services/slots/delete | Odstraní prostředek a všechny jeho podřízené položky. |
> | Action | Migrace Microsoft. DataServices/služby/sloty/čtení | Přečtěte si informace o prostředcích |
> | Action | Migrace Microsoft. DataServices/služby/sloty/zápis | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Action | Microsoft. datamigration/Services/Start/Action | Spusťte službu DMS, aby mohla znovu zpracovat migrace. |
> | Action | Migrace Microsoft. DataServices/služby/zastavení/akce | Zastavením služby DMS minimalizujete své náklady. |
> | Action | Microsoft. datamigration/Services/updateAgentConfig/Action | Aktualizuje konfiguraci agenta DMS pomocí zadaných hodnot. |
> | Action | Migrace Microsoft. DataServices/služby/Write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Action | Migrace Microsoft. datasku/SKU/Read | Získejte seznam SKU podporovaných prostředky DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. DBforMariaDB/checkNameAvailability/Action | Ověřte, jestli je zadaný název serveru k dispozici pro zřizování po celém světě pro dané předplatné. |
> | Action | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Vrátí výsledky operace MariaDB serveru. |
> | Action | Microsoft. DBforMariaDB/Locations/operationResults/Read | Vrátí výsledky operace serveru MariaDB založené na zdroji dat. |
> | Action | Microsoft. DBforMariaDB/Locations/operationResults/Read | Vrátí výsledky operace MariaDB serveru. |
> | Action | Microsoft.DBforMariaDB/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Action | Microsoft.DBforMariaDB/operations/read | Vrátí seznam operací MariaDB. |
> | Action | Microsoft. DBforMariaDB/performanceTiers/Read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Action | Microsoft. DBforMariaDB/Register/Action | Zaregistrovat poskytovatele prostředků MariaDB |
> | Action | Microsoft. DBforMariaDB/servery/Administrators/DELETE | Odstraní existujícího správce serveru MariaDB. |
> | Action | Microsoft. DBforMariaDB/servery/správci/číst | Načte seznam správců serveru MariaDB. |
> | Action | Microsoft. DBforMariaDB/servery/správci/zapisovat | Vytvoří nebo aktualizuje správce serveru MariaDB se zadanými parametry. |
> | Action | Microsoft. DBforMariaDB/servery/poradci/createRecommendedActionSession/Action | Vytvořit novou relaci akce doporučení |
> | Action | Microsoft. DBforMariaDB/servery/poradci/číst | Vrátí seznam poradců. |
> | Action | Microsoft. DBforMariaDB/servery/poradci/číst | Vrácení poradce |
> | Action | Microsoft. DBforMariaDB/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Action | Microsoft. DBforMariaDB/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Action | Microsoft. DBforMariaDB/servery/poradci/recommendedActions/číst | Vrácení doporučené akce |
> | Action | Microsoft.DBforMariaDB/servers/configurations/read | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Action | Microsoft.DBforMariaDB/servers/configurations/write | Aktualizuje hodnotu zadané konfigurace. |
> | Action | Microsoft.DBforMariaDB/servers/databases/delete | Odstraní existující databázi MariaDB. |
> | Action | Microsoft.DBforMariaDB/servers/databases/read | Vrátí seznam databází MariaDB nebo získá vlastnosti zadané databáze. |
> | Action | Microsoft.DBforMariaDB/servers/databases/write | Vytvoří databázi MariaDB se zadanými parametry nebo aktualizuje vlastnosti zadané databáze. |
> | Action | Microsoft.DBforMariaDB/servers/delete | Odstraní existující server. |
> | Action | Microsoft. DBforMariaDB/servery/firewallRules/DELETE | Odstraní existující pravidlo brány firewall. |
> | Action | Microsoft. DBforMariaDB/servery/firewallRules/Read | Vrátí seznam pravidel brány firewall pro server nebo získá vlastnosti zadaného pravidla brány firewall. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizuje stávající pravidlo. |
> | Action | Microsoft.DBforMariaDB/servers/logFiles/read | Vrátí seznam protokolů MariaDB. |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení disagnostic prostředku. |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. DBforMariaDB/servery/zprostředkovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro MariaDB servery. |
> | Action | Microsoft. DBforMariaDB/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Vrátí text pro seznam dotazů. |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Vrátí text dotazu. |
> | Action | Microsoft.DBforMariaDB/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Action | Microsoft.DBforMariaDB/servers/recoverableServers/read | Vrátit informace o obnovitelném serveru MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/replicas/read | Získání replik pro čtení serveru MariaDB |
> | Action | Microsoft. DBforMariaDB/servery/restartovat/akce | Restartuje konkrétní server. |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Načte podrobnosti o zásadách detekce hrozeb serveru nakonfigurovaných na daném serveru. |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Vrátí seznam statistik dotazu pro nejčastější dotazy. |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Vrátí statistiku dotazu. |
> | Action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Aktualizuje konfigurace pro zadaný server. |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Odstraní existující pravidlo Virtual Network. |
> | Action | Microsoft. DBforMariaDB/servery/virtualNetworkRules/Read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Action | Microsoft. DBforMariaDB/servery/virtualNetworkRules/Write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Vrátí statistiku čekání pro instanci. |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Vrácení statistiky čekání |
> | Action | Microsoft.DBforMariaDB/servers/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. DBforMySQL/checkNameAvailability/Action | Ověřte, jestli je zadaný název serveru k dispozici pro zřizování po celém světě pro dané předplatné. |
> | Action | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Vrátit výsledky operace serveru MySQL |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Vrátit výsledky operace serveru MySQL založené na zdroji |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Vrátit výsledky operace serveru MySQL |
> | Action | Microsoft.DBforMySQL/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Action | Microsoft.DBforMySQL/operations/read | Vrátí seznam operací MySQL. |
> | Action | Microsoft.DBforMySQL/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Action | Microsoft.DBforMySQL/register/action | Registrovat poskytovatele prostředků MySQL |
> | Action | Microsoft. DBforMySQL/servery/Administrators/DELETE | Odstraní existujícího správce serveru MySQL. |
> | Action | Microsoft. DBforMySQL/servery/správci/číst | Načte seznam správců serveru MySQL. |
> | Action | Microsoft. DBforMySQL/servery/správci/zapisovat | Vytvoří nebo aktualizuje správce serveru MySQL pomocí zadaných parametrů. |
> | Action | Microsoft. DBforMySQL/servery/poradci/createRecommendedActionSession/Action | Vytvořit novou relaci akce doporučení |
> | Action | Microsoft. DBforMySQL/servery/poradci/číst | Vrátí seznam poradců. |
> | Action | Microsoft. DBforMySQL/servery/poradci/číst | Vrácení poradce |
> | Action | Microsoft. DBforMySQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Action | Microsoft. DBforMySQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Action | Microsoft. DBforMySQL/servery/poradci/recommendedActions/číst | Vrácení doporučené akce |
> | Action | Microsoft. DBforMySQL/servery/konfigurace/číst | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Action | Microsoft.DBforMySQL/servers/configurations/write | Aktualizuje hodnotu zadané konfigurace. |
> | Action | Microsoft.DBforMySQL/servers/databases/delete | Odstraní existující databázi MySQL. |
> | Action | Microsoft.DBforMySQL/servers/databases/read | Vrátí seznam databází MySQL nebo získá vlastnosti zadané databáze. |
> | Action | Microsoft.DBforMySQL/servers/databases/write | Vytvoří databázi MySQL se zadanými parametry nebo aktualizuje vlastnosti zadané databáze. |
> | Action | Microsoft.DBforMySQL/servers/delete | Odstraní existující server. |
> | Action | Microsoft. DBforMySQL/servery/firewallRules/DELETE | Odstraní existující pravidlo brány firewall. |
> | Action | Microsoft. DBforMySQL/servery/firewallRules/Read | Vrátí seznam pravidel brány firewall pro server nebo získá vlastnosti zadaného pravidla brány firewall. |
> | Action | Microsoft. DBforMySQL/servery/firewallRules/Write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizuje stávající pravidlo. |
> | Action | Microsoft.DBforMySQL/servers/logFiles/read | Vrátí seznam protokolů PostgreSQL. |
> | Action | Microsoft. DBforMySQL/servery/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení disagnostic prostředku. |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. DBforMySQL/servery/zprostředkovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro servery MySQL. |
> | Action | Microsoft. DBforMySQL/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Vrátí text pro seznam dotazů. |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Vrátí text dotazu. |
> | Action | Microsoft.DBforMySQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Action | Microsoft.DBforMySQL/servers/recoverableServers/read | Vrátí informace o obnovitelném serveru MySQL. |
> | Action | Microsoft. DBforMySQL/servery/repliky/číst | Získání replik pro čtení serveru MySQL |
> | Action | Microsoft. DBforMySQL/servery/restartovat/akce | Restartuje konkrétní server. |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Načte podrobnosti o zásadách detekce hrozeb serveru nakonfigurovaných na daném serveru. |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Vrátí seznam statistik dotazu pro nejčastější dotazy. |
> | Action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Vrátí statistiku dotazu. |
> | Action | Microsoft. DBforMySQL/servery/updateConfigurations/Action | Aktualizuje konfigurace pro zadaný server. |
> | Action | Microsoft. DBforMySQL/servery/virtualNetworkRules/DELETE | Odstraní existující pravidlo Virtual Network. |
> | Action | Microsoft. DBforMySQL/servery/virtualNetworkRules/Read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Action | Microsoft. DBforMySQL/servery/virtualNetworkRules/Write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Action | Microsoft. DBforMySQL/servery/waitStatistics/Read | Vrátí statistiku čekání pro instanci. |
> | Action | Microsoft. DBforMySQL/servery/waitStatistics/Read | Vrácení statistiky čekání |
> | Action | Microsoft.DBforMySQL/servers/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Ověřte, jestli je zadaný název serveru k dispozici pro zřizování po celém světě pro dané předplatné. |
> | Action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Vrátí výsledky operace PostgreSQL serveru. |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Vrátí výsledky operace serveru PostgreSQL založené na zdroji dat. |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Vrátí výsledky operace PostgreSQL serveru. |
> | Action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Získá výsledek operace připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Vrátí seznam výsledků operace detekce hrozeb serveru. |
> | Action | Microsoft.DBforPostgreSQL/operations/read | Vrátí seznam operací PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Action | Microsoft.DBforPostgreSQL/register/action | Zaregistrovat poskytovatele prostředků PostgreSQL |
> | Action | Microsoft. DBforPostgreSQL/servery/Administrators/DELETE | Odstraní existujícího správce serveru PostgreSQL. |
> | Action | Microsoft. DBforPostgreSQL/servery/správci/číst | Načte seznam správců serveru PostgreSQL. |
> | Action | Microsoft. DBforPostgreSQL/servery/správci/zapisovat | Vytvoří nebo aktualizuje správce serveru PostgreSQL se zadanými parametry. |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/read | Vrátí seznam poradců. |
> | Action | Microsoft. DBforPostgreSQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí. |
> | Action | Microsoft. DBforPostgreSQL/servery/poradci/recommendedActionSessions/Action | Vytvořit doporučení |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/read | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/write | Aktualizuje hodnotu zadané konfigurace. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/delete | Odstraní existující databázi PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/read | Vrátí seznam databází PostgreSQL nebo získá vlastnosti zadané databáze. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/write | Vytvoří databázi PostgreSQL se zadanými parametry nebo aktualizuje vlastnosti zadané databáze. |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | Odstraní existující server. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Odstraní existující pravidlo brány firewall. |
> | Action | Microsoft. DBforPostgreSQL/servery/firewallRules/Read | Vrátí seznam pravidel brány firewall pro server nebo získá vlastnosti zadaného pravidla brány firewall. |
> | Action | Microsoft. DBforPostgreSQL/servery/firewallRules/Write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizuje stávající pravidlo. |
> | Action | Microsoft. DBforPostgreSQL/servery/logFiles/Read | Vrátí seznam protokolů PostgreSQL. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnectionProxies/DELETE | Odstraní existující proxy připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnectionProxies/Read | Vrátí seznam proxy serverů připojení privátního koncového bodu nebo získá vlastnosti zadaného proxy serveru připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnectionProxies/ověřit/akci | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnectionProxies/Write | Vytvoří proxy server připojení privátního koncového bodu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný proxy privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnections/DELETE | Odstraní existující připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnections/Read | Vrátí seznam připojení privátního koncového bodu nebo získá vlastnosti pro zadané připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/servery/privateEndpointConnections/Write | Schválí nebo odmítne existující připojení privátního koncového bodu. |
> | Action | Microsoft. DBforPostgreSQL/servery/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení disagnostic prostředku. |
> | Action | Microsoft. DBforPostgreSQL/servery/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. DBforPostgreSQL/servery/zprostředkovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro Postgres servery. |
> | Action | Microsoft. DBforPostgreSQL/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Vrátí text dotazu. |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Vrátí text pro seznam dotazů. |
> | Action | Microsoft.DBforPostgreSQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Action | Microsoft. DBforPostgreSQL/servery/recoverableServers/Read | Vrátit informace o obnovitelném serveru PostgreSQL |
> | Action | Microsoft. DBforPostgreSQL/servery/repliky/číst | Získání replik pro čtení serveru PostgreSQL |
> | Action | Microsoft. DBforPostgreSQL/servery/restartovat/akce | Restartuje konkrétní server. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Načte podrobnosti o zásadách detekce hrozeb serveru nakonfigurovaných na daném serveru. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Vrátí seznam statistik dotazu pro nejčastější dotazy. |
> | Action | Microsoft. DBforPostgreSQL/servery/updateConfigurations/Action | Aktualizuje konfigurace pro zadaný server. |
> | Action | Microsoft. DBforPostgreSQL/servery/virtualNetworkRules/DELETE | Odstraní existující pravidlo Virtual Network. |
> | Action | Microsoft. DBforPostgreSQL/servery/virtualNetworkRules/Read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Action | Microsoft. DBforPostgreSQL/servery/virtualNetworkRules/Write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Action | Microsoft. DBforPostgreSQL/servery/waitStatistics/Read | Vrátí statistiku čekání pro instanci. |
> | Action | Microsoft.DBforPostgreSQL/servers/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Aktualizuje hodnotu zadané konfigurace. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/delete | Odstraní existující server. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Odstraní existující pravidlo brány firewall. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Vrátí seznam pravidel brány firewall pro server nebo získá vlastnosti zadaného pravidla brány firewall. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizuje stávající pravidlo. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení disagnostic prostředku. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro Postgres servery. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Action | Microsoft.DBforPostgreSQL/serversv2/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/Action | Aktualizuje konfigurace pro zadaný server. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Devices/checkNameAvailability/Action | Ověřte, jestli je k dispozici IotHub název. |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ověřte, jestli je název služby zřizování k dispozici. |
> | Action | Microsoft. Devices/ElasticPools/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. Devices/ElasticPools/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/DELETE | Odstraní certifikát. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Generovat ověřovací kód |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Read | Získá certifikát. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Verify/Action | Ověřit prostředek certifikátu |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/DELETE | Odstranit prostředek tenanta IotHub |
> | Action | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Odstranit skupinu uživatelů EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Získat skupiny uživatelů EventHub |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Vytvořit skupinu uživatelů EventHub |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportovat zařízení |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/GetStats/Read | Získá prostředek statistiky tenanta IotHub. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Importovat zařízení |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/klíče listkey/Action | Získá klíč tenanta IotHub. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/Read | Získat podrobnosti o úlohách odeslaných v daném IotHub |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/klíče listkey/Action | Načte klíče tenanta IotHub. |
> | Action | Microsoft. Devices/ElasticPools/IotHubTenants/logDefinitions/Read | Získá dostupné definice protokolu pro službu IotHub. |
> | Action | Microsoft. Devices/ElasticPools/IotHubTenants/metricDefinitions/Read | Získá dostupné metriky pro službu IotHub. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Získat metriky kvóty |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Read | Získá prostředek klienta IotHub. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Routing/Routes/$testall za akci | Testování zprávy proti všem existujícím trasám |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Routing/Routes/$testnew za akci | Testování zprávy proti poskytnutému testovacímu postupu |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Získá stav všech koncových bodů směrování pro IotHub. |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Write | Vytvořit nebo aktualizovat prostředek klienta IotHub |
> | Action | Microsoft. Devices/ElasticPools/metricDefinitions/Read | Získá dostupné metriky pro službu IotHub. |
> | Action | Microsoft. Devices/iotHubs/Certificates/DELETE | Odstraní certifikát. |
> | Action | Microsoft. Devices/iotHubs/Certificates/generateVerificationCode/Action | Generovat ověřovací kód |
> | Action | Microsoft. Devices/iotHubs/Certificates/Read | Získá certifikát. |
> | Action | Microsoft. Devices/iotHubs/Certificates/Verify/Action | Ověřit prostředek certifikátu |
> | Action | Microsoft. Devices/iotHubs/Certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Action | Microsoft. Devices/iotHubs/DELETE | Odstranit prostředek IotHub |
> | Action | Microsoft. Devices/IotHubs/diagnosticSettings/Read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Odstraní filtr Event Grid. |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | Získá filtr Event Grid. |
> | Action | Microsoft. Devices/iotHubs/eventGridFilters/Write | Vytvořit nový nebo aktualizovat existující filtr Event Grid |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Odstranit skupinu uživatelů EventHub |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Získat skupiny uživatelů EventHub |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Vytvořit skupinu uživatelů EventHub |
> | Action | Microsoft. Devices/iotHubs/exportDevices/Action | Exportovat zařízení |
> | Action | Microsoft. Devices/iotHubs/importDevices/Action | Importovat zařízení |
> | Action | Microsoft. Devices/iotHubs/iotHubKeys/klíče listkey/Action | Získat IotHub klíč pro daný název |
> | Action | Microsoft. Devices/iotHubs/iotHubStats/Read | Získat statistiku IotHub |
> | Action | Microsoft. Devices/iotHubs/Jobs/Read | Získat podrobnosti o úlohách odeslaných v daném IotHub |
> | Action | Microsoft. Devices/iotHubs/klíče listkey/Action | Získat všechny IotHub klíče |
> | Action | Microsoft. Devices/IotHubs/logDefinitions/Read | Získá dostupné definice protokolu pro službu IotHub. |
> | Action | Microsoft. Devices/IotHubs/metricDefinitions/Read | Získá dostupné metriky pro službu IotHub. |
> | Action | Microsoft. Devices/iotHubs/operationresults/Read | Výsledek operace Get (zastaralé rozhraní API) |
> | Action | Microsoft. Devices/iotHubs/quotaMetrics/Read | Získat metriky kvóty |
> | Action | Microsoft. Devices/iotHubs/Read | Získá prostředky IotHub |
> | Action | Microsoft. Devices/iotHubs/Routing/$testall za akci | Testování zprávy proti všem existujícím trasám |
> | Action | Microsoft. Devices/iotHubs/Routing/$testnew za akci | Testování zprávy proti poskytnutému testovacímu postupu |
> | Action | Microsoft. Devices/iotHubs/routingEndpointsHealth/Read | Získá stav všech koncových bodů směrování pro IotHub. |
> | Action | Microsoft. Devices/iotHubs/SKU/Read | Získat platné SKU IotHub |
> | Action | Microsoft. Devices/iotHubs/Write | Vytvořit nebo aktualizovat prostředek IotHub |
> | Action | Microsoft. Devices/Locations/operationresults/Read | Získat výsledek operace na základě umístění |
> | Action | Microsoft. Devices/operationresults/Read | Získat výsledek operace |
> | Action | Microsoft.Devices/operations/Read | Získat všechny operace ResourceProvider |
> | Action | Microsoft.Devices/provisioningServices/certificates/Delete | Odstraní certifikát. |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generovat ověřovací kód |
> | Action | Microsoft.Devices/provisioningServices/certificates/Read | Získá certifikát. |
> | Action | Microsoft. Devices/provisioningServices/Certificates/Verify/Action | Ověřit prostředek certifikátu |
> | Action | Microsoft. Devices/provisioningServices/Certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Action | Microsoft.Devices/provisioningServices/Delete | Odstranit prostředek IotDps |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. Devices/provisioningServices/Keys/klíče listkey/Action | Získat klíče IotDps pro název klíče |
> | Action | Microsoft. Devices/provisioningServices/klíče listkey/Action | Získat všechny IotDps klíče |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | Získá dostupné definice protokolů pro službu zřizování. |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | Získá dostupné metriky pro službu zřizování. |
> | Action | Microsoft.Devices/provisioningServices/operationresults/Read | Získat výsledek operace DPS |
> | Action | Microsoft.Devices/provisioningServices/Read | Získat prostředek IotDps |
> | Action | Microsoft. Devices/provisioningServices/SKU/Read | Získat platné SKU IotDps |
> | Action | Microsoft. Devices/provisioningServices/Write | Vytvořit prostředek IotDps |
> | Action | Microsoft. Devices/Register/Action | Zaregistrujte předplatné pro poskytovatele prostředků IotHub a povolíte vytváření prostředků IotHub. |
> | Action | Microsoft. Devices/Register/Action | Zaregistrujte předplatné pro poskytovatele prostředků IotHub a povolíte vytváření prostředků IotHub. |
> | Action | Microsoft. zařízení/používání/čtení | Získat podrobnosti o použití předplatného pro tohoto poskytovatele. |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DevSpaces/controllers/delete | Odstranění služby Azure Dev Spaces Controller a služeb pro datatarify |
> | Action | Microsoft. DevSpaces/Controllers/listConnectionDetails/Action | Vypíše podrobnosti o připojení pro infrastrukturu řadiče Azure Dev Spaces. |
> | Action | Microsoft. DevSpaces/Controllers/Read | Číst Azure Dev Spaces vlastnosti kontroleru |
> | Action | Microsoft. DevSpaces/Controllers/Write | Vytvořit nebo aktualizovat vlastnosti kontroleru Azure Dev Spaces |
> | Action | Microsoft. DevSpaces/Locations/checkContainerHostMapping/Action | Zkontroluje existující mapování kontroléru pro hostitele kontejneru. |
> | Action | Microsoft. DevSpaces/Locations/operationresults/Read | Stav čtení asynchronní operace |
> | Action | Microsoft. DevSpaces/Register/Action | Registrace poskytovatele prostředků pro vývoj Microsoft dev Spaces pomocí předplatného |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | Odstranit centra testovacího prostředí. |
> | Action | Microsoft.DevTestLab/labCenters/read | Číst laboratorní centra. |
> | Action | Microsoft.DevTestLab/labCenters/write | Přidat nebo upravit centra testovacího prostředí. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/armTemplates/Read | Přečtěte si šablony Azure Resource Manageru. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/artefakty/GenerateArmTemplate/Action | Vygeneruje šablonu ARM pro daný artefakt, nahraje požadované soubory na účet úložiště a ověří vygenerovaný artefakt. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/artefakty/čtení | Čtení artefaktů. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/delete | Odstraňte zdroje artefaktů. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/Read | Čtení zdrojů artefaktů. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | Přidejte nebo upravte zdroje artefaktů. |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Vydeklarujte náhodný virtuální počítač v testovacím prostředí. |
> | Action | Microsoft.DevTestLab/labs/costs/read | Náklady na čtení. |
> | Action | Microsoft.DevTestLab/labs/costs/write | Přidat nebo upravit náklady. |
> | Action | Microsoft. DevTestLab/Labs/CreateEnvironment/Action | Vytvářejte virtuální počítače v testovacím prostředí. |
> | Action | Microsoft.DevTestLab/labs/customImages/delete | Odstraňte vlastní image. |
> | Action | Microsoft.DevTestLab/labs/customImages/read | Čtení vlastních imagí. |
> | Action | Microsoft.DevTestLab/labs/customImages/write | Přidat nebo upravit vlastní image. |
> | Action | Microsoft.DevTestLab/labs/delete | Odstraňte Labs. |
> | Action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Ujistěte se, že aktuální uživatel má v testovacím prostředí platný profil. |
> | Action | Microsoft. DevTestLab/Labs/ExportResourceUsage/Action | Exportuje využití prostředků testovacího prostředí do účtu úložiště. |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | Odstraňte vzorce. |
> | Action | Microsoft.DevTestLab/labs/formulas/read | Čtení vzorců. |
> | Action | Microsoft.DevTestLab/labs/formulas/write | Přidat nebo upravit vzorce. |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | Čtení imagí galerie. |
> | Action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Vygeneruje identifikátor URI pro nahrání vlastních imagí disků do testovacího prostředí. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/delete | Odstraňte nečinné vypínání. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/read | Čtení nečinných vypínání. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/write | Přidat nebo upravit nečinné vypnutí. |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importujte virtuální počítač do jiného testovacího prostředí. |
> | Action | Microsoft. DevTestLab/Labs/ListVhds/Action | Vypíše diskové image dostupné pro vytvoření vlastní image. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/delete | Odstraňte notificationchannels. |
> | Action | Microsoft. DevTestLab/Labs/notificationChannels/Notify/Action | Odešle oznámení zadanému kanálu. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | Přečtěte si notificationchannels. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | Přidat nebo upravit notificationchannels |
> | Action | Microsoft. DevTestLab/Labs/policySets/EvaluatePolicies/Action | Vyhodnotí zásady testovacího prostředí. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/delete | Odstraňte zásady. |
> | Action | Microsoft. DevTestLab/Labs/policySets/policies/Read | Číst zásady. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/write | Přidat nebo upravit zásady. |
> | Action | Microsoft.DevTestLab/labs/policySets/read | Číst sady zásad. |
> | Action | Microsoft.DevTestLab/labs/read | Přečtěte si Labs. |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | Odstraňte plány. |
> | Action | Microsoft. DevTestLab/Labs/Schedules/Execute/Action | Spusťte plán. |
> | Action | Microsoft. DevTestLab/Labs/plány/ListApplicable/Action | Zobrazí seznam všech použitelných plánů. |
> | Action | Microsoft.DevTestLab/labs/schedules/read | Načtou se plány. |
> | Action | Microsoft.DevTestLab/labs/schedules/write | Přidat nebo upravit plány. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | Odstraňte spouštěče služeb. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | Přečtěte si spouštěče služeb. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | Přidat nebo upravit spouštěče služeb. |
> | Action | Microsoft. DevTestLab/Labs/sharedGalleries/DELETE | Odstraňte sdílené galerie. |
> | Action | Microsoft. DevTestLab/Labs/sharedGalleries/Read | Číst sdílené galerie. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Odstraňte sdílené image. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Čtení sdílených imagí. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Přidání nebo úprava sdílených imagí. |
> | Action | Microsoft. DevTestLab/Labs/sharedGalleries/Write | Přidat nebo upravit sdílené galerie. |
> | Action | Microsoft.DevTestLab/labs/users/delete | Odstraňte profily uživatelů. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | Připojte a vytvořte zapůjčení disku k virtuálnímu počítači. |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | Odstraňte disky. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odpojit a zrušit zapůjčení disku připojeného k virtuálnímu počítači. |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | Čtení disků. |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | Přidejte nebo upravte disky. |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | Odstraňte prostředí. |
> | Action | Microsoft. DevTestLab/Labs/uživatelé/prostředí/číst | Čtení prostředí. |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | Přidejte nebo upravte prostředí. |
> | Action | Microsoft.DevTestLab/labs/users/read | Čtení profilů uživatelů. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | Odstraňte tajné kódy. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | Čtení tajných kódů. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | Přidání nebo úprava tajných klíčů. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Odstraňte prostředky infrastruktury služby. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Zobrazí seznam použitelných plánů spuštění/zastavení, pokud existují. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Přečtěte si Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Odstraňte plány. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Spusťte plán. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Načtou se plány. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Přidat nebo upravit plány. |
> | Action | Microsoft. DevTestLab/Labs/Users/serviceFabrics/Start/Action | Spusťte Service Fabric. |
> | Action | Microsoft. DevTestLab/Labs/Users/serviceFabrics/stop/Action | Zastavení služby Service Fabric |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Přidat nebo upravit prostředky infrastruktury služby |
> | Action | Microsoft.DevTestLab/labs/users/write | Přidejte nebo upravte profily uživatelů. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Připojte k virtuálnímu počítači nový nebo existující datový disk. |
> | Action | Microsoft. DevTestLab/Labs/virtualMachines/ApplyArtifacts/Action | Použít artefakty na virtuálním počítači. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Převezme vlastnictví existujícího virtuálního počítače. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | Odstranění virtuálních počítačů. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odpojí zadaný disk od virtuálního počítače. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Načte řetězec, který představuje obsah souboru RDP pro virtuální počítač. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Zobrazí seznam použitelných plánů spuštění/zastavení, pokud existují. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | Čtení virtuálních počítačů. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Opětovné nasazení virtuálního počítače |
> | Action | Microsoft. DevTestLab/Labs/virtualMachines/změnit velikost/akce | Změna velikosti virtuálního počítače |
> | Action | Microsoft. DevTestLab/Labs/virtualMachines/restart/Action | Restartujte virtuální počítač. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Odstraňte plány. |
> | Action | Microsoft. DevTestLab/Labs/virtualMachines/Schedules/Execute/Action | Spusťte plán. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Načtou se plány. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Přidat nebo upravit plány. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Spusťte virtuální počítač. |
> | Action | Microsoft. DevTestLab/Labs/virtualMachines/stop/Action | Zastavení virtuálního počítače |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Přenese všechny datové disky připojené k virtuálnímu počítači vlastněné aktuálním uživatelem. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Vydávat vlastnictví existujícího virtuálního počítače |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | Přidání nebo úprava virtuálních počítačů. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | Odstraňte virtuální sítě. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | Číst virtuální sítě. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | Přidat nebo upravit virtuální sítě. |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | Odstraňte fondy virtuálních počítačů. |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | Čtení fondů virtuálních počítačů. |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | Přidejte nebo upravte fondy virtuálních počítačů. |
> | Action | Microsoft.DevTestLab/labs/write | Přidat nebo upravit cvičení. |
> | Action | Microsoft.DevTestLab/locations/operations/read | Operace čtení. |
> | Action | Microsoft.DevTestLab/register/action | Zaregistruje předplatné. |
> | Action | Microsoft.DevTestLab/schedules/delete | Odstraňte plány. |
> | Action | Microsoft. DevTestLab/Schedules/Execute/Action | Spusťte plán. |
> | Action | Microsoft.DevTestLab/schedules/read | Načtou se plány. |
> | Action | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje ID cílového prostředku plánu. |
> | Action | Microsoft.DevTestLab/schedules/write | Přidat nebo upravit plány. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | Ověří dostupnost názvu. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Odstraní kolekci. Platí jenom pro typy rozhraní API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Přečtěte si kolekci nebo seznam všech kolekcí. Platí jenom pro typy rozhraní API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' MongoDB '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Čtení propustnosti kolekce. Platí jenom pro typy rozhraní API: ' MongoDB '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Aktualizujte propustnost kolekce. Platí jenom pro typy rozhraní API: ' MongoDB '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Vytvoří nebo aktualizuje kolekci. Platí jenom pro typy rozhraní API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Odstraní kontejner. Platí pouze pro typy rozhraní API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Stav čtení asynchronní operace. Platí pouze pro typy rozhraní API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Přečtěte si kontejner nebo seznam všech kontejnerů. Platí pouze pro typy rozhraní API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Stav čtení asynchronní operace. Platí pouze pro typy rozhraní API: ' SQL '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Čtení propustnosti kontejneru. Platí pouze pro typy rozhraní API: ' SQL '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Aktualizace propustnosti kontejneru. Platí pouze pro typy rozhraní API: ' SQL '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Vytvoří nebo aktualizuje kontejner. Platí pouze pro typy rozhraní API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Odstraní databázi. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Odstraní graf. Platí jenom pro typy rozhraní API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Přečtěte si graf nebo seznam všech grafů. Platí jenom pro typy rozhraní API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' gremlin'. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Načte propustnost grafu. Platí jenom pro typy rozhraní API: ' gremlin'. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Aktualizujte propustnost grafu. Platí jenom pro typy rozhraní API: ' gremlin'. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Vytvoří nebo aktualizuje graf. Platí jenom pro typy rozhraní API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Stav čtení asynchronní operace. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Přečtěte si databázi nebo seznam všech databází. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Stav čtení asynchronní operace. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Přečtěte si propustnost databáze. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Aktualizujte propustnost databáze. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Vytvořte databázi. Platí pouze pro typy rozhraní API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/disks/DELETE | Odstraní prostor. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/Spaces/operationResults/Read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/Spaces/Read | Přečtěte si místo nebo uveďte všechny prostory. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' Cassandra '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Přečtěte si propustnost prostoru klíčů. Platí jenom pro typy rozhraní API: ' Cassandra '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Aktualizujte propustnost prostoru klíčů. Platí jenom pro typy rozhraní API: ' Cassandra '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft. DocumentDB/databaseAccounts/rozhraní API/adresní prostory/tabulky/odstranění | Odstraní tabulku. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/rozhraní API/adresní prostory/tabulky/operationResults/Read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/disks/Tables/četl | Přečtěte si tabulku nebo seznam všech tabulek. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Stav čtení asynchronní operace. Platí jenom pro typy rozhraní API: ' Cassandra '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Přečtěte si propustnost tabulky. Platí jenom pro typy rozhraní API: ' Cassandra '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Aktualizujte propustnost tabulky. Platí jenom pro typy rozhraní API: ' Cassandra '. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/disks/Tables/Write | Vytvoří nebo aktualizuje tabulku. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Vytvořte místo pro. Platí jenom pro typy rozhraní API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Odstraní tabulku. Dá se použít jenom pro typy rozhraní API: Table. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Stav čtení asynchronní operace. Dá se použít jenom pro typy rozhraní API: Table. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/Tables/Read | Přečtěte si tabulku nebo seznam všech tabulek. Dá se použít jenom pro typy rozhraní API: Table. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Stav čtení asynchronní operace. Dá se použít jenom pro typy rozhraní API: Table. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Přečtěte si propustnost tabulky. Dá se použít jenom pro typy rozhraní API: Table. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Aktualizujte propustnost tabulky. Dá se použít jenom pro typy rozhraní API: Table. Platí jenom pro typy nastavení: propustnost. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Vytvoří nebo aktualizuje tabulku. Dá se použít jenom pro typy rozhraní API: Table. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Backup/Action | Odeslat žádost o konfiguraci zálohování |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Změna skupiny prostředků databázového účtu |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Přečte definice metriky kolekce. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Přečte metriky kolekce. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Číst metriky úrovně klíče oddílu účtu databáze |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Číst metriky na úrovni oddílu účtu databáze |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Čtení oddílů databázového účtu v kolekci |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Číst použití na úrovni oddílu účtu databáze |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Přečte využití kolekce. |
> | Action | Microsoft. DocumentDB/databaseAccounts/databázes/metricDefinitions/Read | Přečte definice metrik databáze. |
> | Action | Microsoft. DocumentDB/databaseAccounts/databáze/metriky/čtení | Přečte metriky databáze. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Přečte využití databáze. |
> | Action | Microsoft.DocumentDB/databaseAccounts/delete | Odstraní účty databáze. |
> | Action | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/Action | Změňte priority převzetí služeb při selhání v oblastech databázového účtu. Tento postup se používá k provedení ruční operace převzetí služeb při selhání. |
> | Action | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Získat zásady zálohování databázového účtu |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Získání připojovacích řetězců pro databázový účet |
> | Action | Microsoft. DocumentDB/databaseAccounts/klíče listkey/Action | Seznam klíčů databázového účtu |
> | Action | Microsoft. DocumentDB/databaseAccounts/metricDefinitions/Read | Přečte definice metrik databázového účtu. |
> | Action | Microsoft. DocumentDB/databaseAccounts/metriky/číst | Přečte metriky databázového účtu. |
> | Action | Microsoft. DocumentDB/databaseAccounts/offlineRegion/Action | Offline oblast databázového účtu.  |
> | Action | Microsoft. DocumentDB/databaseAccounts/onlineRegion/Action | Online oblast databázového účtu. |
> | Action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Stav čtení asynchronní operace |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/metriky/číst | Čtení metrik latence |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/read | Přečíst percentily latencí replikace |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/sourceRegion/targetRegion/metriky/číst | Čtení metrik latence pro konkrétní zdrojovou a cílovou oblast |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/targetRegion/metriky/číst | Čtení metrik latence pro konkrétní cílovou oblast |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Odstraní proxy účet privátního koncového bodu připojení k databázovému účtu. |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Čtení proxy serveru připojení privátního koncového bodu k databázovému účtu |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Ověření proxy serveru připojení privátního koncového bodu k databázovému účtu |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Vytvoří nebo aktualizuje proxy serveru připojení privátního koncového bodu k databázovému účtu. |
> | Action | Microsoft.DocumentDB/databaseAccounts/read | Přečte účet databáze. |
> | Action | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Přečte klíče databázového účtu jen pro čtení. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Přečte klíče databázového účtu jen pro čtení. |
> | Action | Microsoft. DocumentDB/databaseAccounts/regenerateKey/Action | Otočení klíčů databázového účtu |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/databáze/kolekce/metriky/čtení | Přečte místní metriky kolekce. |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Číst metriky na úrovni klíče oddílu účtu místní databáze |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/databáze/kolekce/oddíly/metriky/čtení | Číst metriky na úrovni oddílu účtu místní databáze |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/databáze/kolekce/partitions/Read | Čtení oddílů účtu místní databáze v kolekci |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/metrika/číst | Přečte metriky oblasti a účtu databáze. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Odeslat žádost o obnovení |
> | Action | Microsoft.DocumentDB/databaseAccounts/usages/read | Přečte využití databázového účtu. |
> | Action | Microsoft.DocumentDB/databaseAccounts/write | Aktualizuje účty databáze. |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoft. DocumentDB, že se odstraňuje VirtualNetwork nebo podsíť. |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Stav čtení asynchronní operace deleteVirtualNetworkOrSubnets |
> | Action | Microsoft.DocumentDB/locations/operationsStatus/read | Přečte stav asynchronních operací. |
> | Action | Microsoft. DocumentDB/operationResults/Read | Stav čtení asynchronní operace |
> | Action | Microsoft.DocumentDB/operations/read | Operace čtení dostupné pro Microsoft DocumentDB  |
> | Action | Microsoft. DocumentDB/Register/Action |  Registrace poskytovatele prostředků Microsoft DocumentDB pro předplatné |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. DomainRegistration/checkDomainAvailability/Action | Zjistit, jestli je doména dostupná k nákupu |
> | Action | Microsoft. DomainRegistration/domén/DELETE | Odstraní existující doménu. |
> | Action | Microsoft. DomainRegistration/domény/domainownershipidentifiers/DELETE | Odstranit identifikátor vlastnictví |
> | Action | Microsoft. DomainRegistration/doméns/domainownershipidentifiers/Read | Seznam identifikátorů vlastnictví |
> | Action | Microsoft. DomainRegistration/doméns/domainownershipidentifiers/Read | Získat identifikátor vlastnictví |
> | Action | Microsoft. DomainRegistration/domény/domainownershipidentifiers/Write | Vytvořit nebo aktualizovat identifikátor |
> | Action | Microsoft. DomainRegistration/doméns/operationresults/Read | Získat doménovou operaci |
> | Action | Microsoft. DomainRegistration/domény/číst | Získá seznam domén. |
> | Action | Microsoft. DomainRegistration/domény/číst | Získat doménu |
> | Action | Microsoft. DomainRegistration/domény/prodloužit/Action | Obnovte existující doménu. |
> | Action | Microsoft. DomainRegistration/domén/Write | Přidat novou doménu nebo aktualizovat existující |
> | Action | Microsoft. DomainRegistration/generateSsoRequest/Action | Vygenerujte žádost o přihlášení do centra řízení domény. |
> | Action | Microsoft. DomainRegistration/listDomainRecommendations/Action | Načtěte doporučení k doméně seznamu na základě klíčových slov. |
> | Action | Microsoft.DomainRegistration/operations/Read | Vypsat všechny operace z registrace domény App Service |
> | Action | Microsoft. DomainRegistration/Register/Action | Registrace poskytovatele prostředků domény Microsoft pro předplatné |
> | Action | Microsoft. DomainRegistration/topLevelDomains/listAgreements/Action | Akce výpisu smlouvy |
> | Action | Microsoft. DomainRegistration/topLevelDomains/Read | Získat domény Toplevel |
> | Action | Microsoft. DomainRegistration/topLevelDomains/Read | Získat doménu Toplevel |
> | Action | Microsoft. DomainRegistration/validateDomainRegistrationInformation/Action | Ověřit objekt nákupu domény bez odeslání |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. EventGrid/domén/DELETE | Odstranění domény |
> | Action | Microsoft. EventGrid/domény/klíče listkey/Action | Seznam klíčů pro doménu |
> | Action | Microsoft. EventGrid/doméns/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro domény. |
> | Action | Microsoft. EventGrid/domény/číst | Čtení domény |
> | Action | Microsoft.EventGrid/domains/regenerateKey/action | Znovu vygenerovat klíč pro doménu |
> | Action | Microsoft. EventGrid/domény/témata/číst | Přečíst téma domény |
> | Action | Microsoft. EventGrid/domén/Write | Vytvoření nebo aktualizace domény |
> | Action | Microsoft.EventGrid/eventSubscriptions/delete | Odstranění eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Získat úplnou adresu URL pro odběr události |
> | Action | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro odběry událostí. |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro odběry událostí. |
> | Action | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro eventSubscriptions. |
> | Action | Microsoft. EventGrid/eventSubscriptions/Read | Přečíst eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/write | Vytvořit nebo aktualizovat eventSubscription |
> | Action | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro témata. |
> | Action | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata. |
> | Action | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro témata. |
> | Action | Microsoft.EventGrid/extensionTopics/read | Přečtěte si extensionTopic. |
> | Action | Microsoft.EventGrid/locations/eventSubscriptions/read | Výpis odběrů místních událostí |
> | Action | Microsoft.EventGrid/locations/operationResults/read | Přečtěte si výsledek regionální operace. |
> | Action | Microsoft.EventGrid/locations/operationsStatus/read | Přečtěte si stav místní operace. |
> | Action | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Výpis odběrů místních událostí podle TopicType |
> | Action | Microsoft.EventGrid/operationResults/read | Přečtěte si výsledek operace. |
> | Action | Microsoft.EventGrid/operations/read | Vypíše EventGrid operace. |
> | Action | Microsoft.EventGrid/operationsStatus/read | Přečtěte si stav operace. |
> | Action | Microsoft.EventGrid/register/action | Zaregistruje předplatné pro poskytovatele prostředků EventGrid. |
> | Action | Microsoft. EventGrid/témata/DELETE | Odstranění tématu |
> | Action | Microsoft.EventGrid/topics/listKeys/action | Seznam klíčů pro téma |
> | Action | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro témata. |
> | Action | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata. |
> | Action | Microsoft. EventGrid/témata/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro témata. |
> | Action | Microsoft. EventGrid/témata/číst | Přečtěte si téma. |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | Znovu vygenerovat klíč pro téma |
> | Action | Microsoft.EventGrid/topics/write | Vytvoří nebo aktualizuje téma. |
> | Action | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Vypsat globální odběry událostí podle typu tématu |
> | Action | Microsoft.EventGrid/topictypes/eventtypes/read | Číst typ události podporovaný TopicType |
> | Action | Microsoft. EventGrid/topictypes/Read | Přečíst TopicType |
> | Action | Microsoft.EventGrid/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. EventHub/availableClusterRegions/Read | Operace čtení a výpisu dostupných předem zajištěných clusterů podle oblasti Azure. |
> | Action | Microsoft. EventHub/checkNameAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. |
> | Action | Microsoft. EventHub/checkNamespaceAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. Toto rozhraní API je zastaralé, použijte místo toho CheckNameAvailability. |
> | Action | Microsoft. EventHub/clustery/odstranit | Odstraní existující prostředek clusteru. |
> | Action | Microsoft. EventHub/clustery/obory názvů/číst | Vypíše ID ARM oboru názvů pro obory názvů v rámci clusteru. |
> | Action | Microsoft. EventHub/clustery/operationresults/Read | Získá stav asynchronní operace clusteru. |
> | Action | Microsoft. EventHub/clustery/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik clusteru. |
> | Action | Microsoft. EventHub/clustery/číst | Získá popis prostředku clusteru. |
> | Action | Microsoft. EventHub/clustery/Write | Vytvoří nebo upraví existující prostředek clusteru. |
> | Action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla virtuální sítě v poskytovateli prostředků EventHub pro zadanou virtuální síť. |
> | Action | Microsoft. EventHub/obory názvů/autorizačních pravidel/Action | Aktualizuje autorizační pravidlo oboru názvů. Toto rozhraní API je zastaralé. Místo toho prosím použijte volání PUT k aktualizaci autorizačního pravidla oboru názvů.. Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. EventHub/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Action | Microsoft. EventHub/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Action | Microsoft. EventHub/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Action | Microsoft. EventHub/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. EventHub/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Action | Microsoft. EventHub/obory názvů/odstranění | Odstranit prostředek oboru názvů |
> | Action | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/klíče listkey/Action | Získá klíče autorizačních pravidel pro primární obor názvů zotavení po havárii. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat autorizační pravidla primárního oboru názvů pro zotavení po havárii |
> | Action | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/breakPairing/Action | Zakáže zotavení po havárii a zastaví replikaci změn z primárních oborů názvů na sekundární. |
> | Action | Microsoft. EventHub/obory názvů/disasterrecoveryconfigs/checkNameAvailability/Action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Action | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/DELETE | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tuto operaci lze vyvolat pouze prostřednictvím primárního oboru názvů. |
> | Action | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/převzetí služeb při selhání/akce | Vyvolá převzetí služeb při selhání GEOGRAFICKého zotavení po havárii a změní konfiguraci aliasu oboru názvů tak, aby odkazoval na sekundární obor názvů. |
> | Action | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/Read | Získá konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Action | Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/Write | Vytvoří nebo aktualizuje konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/Action | Operace aktualizace EventHub Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel EventHub |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/klíče listkey/Action | Získá připojovací řetězec k centru EventHub. |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/Read |  Získat seznam autorizačních pravidel EventHub |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel/Write | Vytvořte autorizační pravidla EventHub a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Action | Microsoft. EventHub/obory názvů/eventHubs/consumergroups/DELETE | Operace odstranění prostředku uživatele |
> | Action | Microsoft. EventHub/obory názvů/eventHubs/consumergroups/Read | Získá seznam popisů prostředků uživatele. |
> | Action | Microsoft. EventHub/obory názvů/eventHubs/consumergroups/Write | Vytvoří nebo aktualizuje vlastnosti uživatele. |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/DELETE | Operace odstranění prostředku EventHub |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/Read | Získá seznam popisů prostředků EventHub. |
> | Action | Microsoft. EventHub/obory názvů/eventhubs/Write | Vytvoří nebo aktualizuje vlastnosti EventHub. |
> | Action | Microsoft. EventHub/obory názvů/ipFilterRules/DELETE | Odstranit prostředek filtru IP |
> | Action | Microsoft. EventHub/obory názvů/ipFilterRules/Read | Získat prostředek filtru IP |
> | Action | Microsoft. EventHub/obory názvů/ipFilterRules/Write | Vytvořit prostředek filtru IP |
> | Akce dataaction | Microsoft. EventHub/obory názvů/zprávy/přijmout/akce | Příjem zpráv |
> | Akce dataaction | Microsoft. EventHub/obory názvů/zprávy/odeslání/akce | Odesílání zpráv |
> | Action | Microsoft. EventHub/obory názvů/messagingPlan/Read | Získá plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. EventHub/obory názvů/messagingPlan/Write | Aktualizuje plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. EventHub/obory názvů/networkruleset/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Action | Microsoft. EventHub/obory názvů/networkruleset/Read | Získá prostředek NetworkRuleSet. |
> | Action | Microsoft. EventHub/obory názvů/networkruleset/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Action | Microsoft. EventHub/obory názvů/networkrulesets/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Action | Microsoft. EventHub/obory názvů/networkrulesets/Read | Získá prostředek NetworkRuleSet. |
> | Action | Microsoft. EventHub/obory názvů/networkrulesets/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Action | Microsoft. EventHub/obory názvů/operationresults/Read | Získat stav operace oboru názvů |
> | Action | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Action | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Write | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Action | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/logDefinitions/Read | Získá seznam popisů prostředků v názvech oborů názvů. |
> | Action | Microsoft. EventHub/obory názvů/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Action | Microsoft. EventHub/obory názvů/číst | Získat seznam popisu prostředku oboru názvů |
> | Action | Microsoft. EventHub/obory názvů/removeAcsNamepsace/Action | Odebrat obor názvů ACS |
> | Action | Microsoft. EventHub/obory názvů/virtualNetworkRules/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Action | Microsoft. EventHub/obory názvů/virtualNetworkRules/Read | Získá prostředek pravidla virtuální sítě. |
> | Action | Microsoft. EventHub/obory názvů/virtualNetworkRules/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Action | Microsoft. EventHub/obory názvů/zápis | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Action | Microsoft.EventHub/operations/read | Získat operace |
> | Action | Microsoft. EventHub/registrace/akce | Zaregistruje předplatné pro poskytovatele prostředků EventHub a povolí vytváření prostředků EventHub. |
> | Action | Microsoft.EventHub/sku/read | Získá seznam popisů prostředků SKU. |
> | Action | Microsoft. EventHub/SKU/Regions/Read | Získá seznam popisů prostředků SkuRegions. |
> | Action | Microsoft. EventHub/zrušit registraci/akce | Zaregistruje poskytovatele prostředků EventHub. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Features/Features/Read | Získá funkce předplatného. |
> | Action | Microsoft. Features/Operations/Read | Získá seznam operací. |
> | Action | Microsoft. Features/Providers/Features/Read | Získá funkci předplatného v daném poskytovateli prostředků. |
> | Action | Microsoft. Features/Providers/Features/Register/Action | Zaregistruje funkci pro předplatné v daném poskytovateli prostředků. |
> | Action | Microsoft. Features/poskytovatelé/funkce/zrušit registraci/akce | Zruší registraci funkce pro předplatné v daném poskytovateli prostředků. |
> | Action | Microsoft. Features/Register/Action | Zaregistruje funkci předplatného. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Odstraňte přiřazení konfigurace hostů. |
> | Action | Microsoft. GuestConfiguration/guestConfigurationAssignments/Read | Získat přiřazení konfigurace hostů. |
> | Action | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Získat sestavu přiřazení konfigurace hostů |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Vytvořte nové přiřazení konfigurace hostů. |
> | Action | Microsoft. GuestConfiguration/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. HDInsight/clustery/aplikace/odstranit | Odstranit aplikaci pro cluster HDInsight |
> | Action | Microsoft. HDInsight/clustery/aplikace/číst | Získat aplikaci pro cluster HDInsight |
> | Action | Microsoft. HDInsight/clustery/aplikace/zapisovat | Vytvořit nebo aktualizovat cluster aplikace pro HDInsight |
> | Action | Microsoft. HDInsight/Clusters/changerdpsetting/Action | Změna nastavení RDP pro cluster HDInsight |
> | Action | Microsoft. HDInsight/clustery/konfigurace/akce | Aktualizovat konfiguraci clusteru HDInsight |
> | Action | Microsoft. HDInsight/clustery/konfigurace/akce | Získat konfigurace clusteru HDInsight |
> | Action | Microsoft. HDInsight/clustery/konfigurace/číst | Získat konfigurace clusteru HDInsight |
> | Action | Microsoft.HDInsight/clusters/delete | Odstranění clusteru HDInsight |
> | Action | Microsoft. HDInsight/Clusters/getGatewaySettings/Action | Získat nastavení brány pro cluster HDInsight |
> | Action | Microsoft. HDInsight/clustery/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro cluster HDInsight prostředku. |
> | Action | Microsoft. HDInsight/clustery/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro cluster HDInsight prostředku. |
> | Action | Microsoft. HDInsight/clustery/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro cluster HDInsight. |
> | Action | Microsoft. HDInsight/Clusters/Read | Získat podrobnosti o clusteru HDInsight |
> | Action | Microsoft. HDInsight/clustery/role/Změna velikosti/akce | Změna velikosti clusteru HDInsight |
> | Action | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualizace nastavení brány pro cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/write | Vytvořit nebo aktualizovat cluster HDInsight |
> | Action | Microsoft. HDInsight/umístění/možnosti/číst | Získání možností předplatného |
> | Action | Microsoft. HDInsight/Locations/checkNameAvailability/Read | Ověřit dostupnost názvu |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ImportExport/Jobs/DELETE | Odstraní existující úlohu. |
> | Action | Microsoft. ImportExport/Jobs/listBitLockerKeys/Action | Získá klíče BitLockeru pro zadanou úlohu. |
> | Action | Microsoft. ImportExport/Jobs/číst | Získá vlastnosti pro zadanou úlohu nebo vrátí seznam úloh. |
> | Action | Microsoft. ImportExport/Jobs/Write | Vytvoří úlohu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou úlohu. |
> | Action | Microsoft. ImportExport/Locations/Read | Získá vlastnosti pro zadané umístění nebo vrátí seznam umístění. |
> | Action | Microsoft. ImportExport/Register/Action | Zaregistruje předplatné poskytovatele prostředků pro import/export a povolí vytváření úloh importu/exportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | Odstranění skupiny akcí |
> | Action | Microsoft.Insights/ActionGroups/Read | Čtení skupiny akcí |
> | Action | Microsoft.Insights/ActionGroups/Write | Vytvoří nebo aktualizuje skupinu akcí. |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivovalo se upozornění protokolu aktivit. |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | Odstranění upozornění protokolu aktivit |
> | Action | Microsoft. Insights/Upozorněníprotokoluaktivit/Read | Načtení upozornění protokolu aktivit |
> | Action | Microsoft. Insights/Upozorněníprotokoluaktivit/Write | Vytvoří nebo aktualizuje upozornění protokolu aktivit. |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | Aktivované klasické upozornění na metriku |
> | Action | Microsoft.Insights/AlertRules/Delete | Odstranění klasické výstrahy metriky |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | Přečíst klasický incident výstrahy metriky |
> | Action | Microsoft.Insights/AlertRules/Read | Čtení klasické výstrahy metriky |
> | Action | Microsoft.Insights/AlertRules/Resolved/Action | Vyřešeno klasické upozornění na metriku |
> | Action | Microsoft.Insights/AlertRules/Throttled/Action | Pravidlo upozornění na klasické metriky omezení |
> | Action | Microsoft.Insights/AlertRules/Write | Vytvořit nebo aktualizovat klasické upozornění na metriku |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | Odstranění nastavení automatického škálování |
> | Action | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Read | Přečíst nastavení diagnostiky prostředků |
> | Action | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředků. |
> | Action | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/logDefinitions/Read | Čtení definic protokolů |
> | Action | Microsoft. Insights/AutoscaleSettings/Providers/Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | Přečíst nastavení automatického škálování |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Vyvoláno automatické škálování pro horizontální navýšení kapacity |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Dokončí se horizontální horizontální navýšení kapacity |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Bylo zahájeno automatické škálování pro horizontální navýšení kapacity |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Automatické škálování pro horizontální navýšení kapacity dokončeno |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | Vytvoří nebo aktualizuje nastavení automatického škálování. |
> | Action | Microsoft. Insights/směrný plán/čtení | Čtení směrného plánu metriky (Preview) |
> | Action | Microsoft. Insights/CalculateBaseline/Read | Vypočítat směrný plán pro hodnoty metriky (Preview) |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | Odstraňování položky Application Insights Analytics |
> | Action | Microsoft. Insights/Components/AnalyticsItems/Read | Čtení položky Application Insights Analytics |
> | Action | Microsoft. Insights/Components/AnalyticsItems/Write | Zápis položky Application Insights Analytics |
> | Action | Microsoft. Insights/Components/AnalyticsTables/Action | Akce tabulky Application Insights Analytics |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Delete | Odstraňování schématu Application Insights Analytics Table |
> | Action | Microsoft. Insights/Components/AnalyticsTables/Read | Čtení schématu Application Insights Analytics tabulka |
> | Action | Microsoft. Insights/Components/AnalyticsTables/Write | Zápis schématu Application Insights Analytics tabulka |
> | Action | Microsoft. Insights/komponenty/poznámky/odstranit | Odstranění anotace Application Insights |
> | Action | Microsoft. Insights/komponenty/poznámky/číst | Čtení poznámky Application Insights |
> | Action | Microsoft. Insights/komponenty/poznámky/zápis | Zápis Application Insights anotace |
> | Action | Microsoft. Insights/komponenty/API/Read | Načítání rozhraní API pro Application Insights dat součásti |
> | Action | Microsoft. Insights/Components/ApiKeys/Action | Generuje se klíč rozhraní Application Insights API. |
> | Action | Microsoft.Insights/Components/ApiKeys/Delete | Odstraňuje se Application Insights klíč rozhraní API. |
> | Action | Microsoft. Insights/Components/ApiKeys/Read | Čtení klíče rozhraní API Application Insights |
> | Action | Microsoft. Insights/Components/BillingPlanForComponent/Read | Čtení plánu fakturace pro Application Insights komponentu |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Čtení aktuálních funkcí fakturace pro součást Application Insights |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Zápis aktuálních funkcí fakturace pro součást Application Insights |
> | Action | Microsoft. Insights/Components/DefaultWorkItemConfig/Read | Čtení Application Insights výchozí konfigurace integrace ALM |
> | Action | Microsoft. Insights/Components/DELETE | Odstraňuje se konfigurace komponenty Application Insights. |
> | Action | Microsoft. Insights/komponenty/Events/číst | Získání protokolů z Application Insights pomocí formátu dotazu OData |
> | Action | Microsoft. Insights/Components/ExportConfiguration/Action | Akce Application Insights exportu nastavení |
> | Action | Microsoft. Insights/Components/ExportConfiguration/DELETE | Odstraňování nastavení exportu Application Insights |
> | Action | Microsoft. Insights/Components/ExportConfiguration/Read | Čtení nastavení exportu Application Insights |
> | Action | Microsoft. Insights/Components/ExportConfiguration/Write | Zápis nastavení exportu Application Insights |
> | Action | Microsoft. Insights/Components/ExtendQueries/Read | Čtení výsledků rozšířeného dotazu součásti Application Insights |
> | Action | Microsoft. Insights/komponenty/oblíbené/odstranit | Odstraňování oblíbených Application Insights |
> | Action | Microsoft. Insights/komponenty/oblíbené/číst | Čtení Application Insights oblíbené položky |
> | Action | Microsoft. Insights/komponenty/oblíbená/Write | Zápis Application Insights oblíbené položky |
> | Action | Microsoft. Insights/Components/FeatureCapabilities/Read | Čtení možností funkce Application Insights součásti |
> | Action | Microsoft. Insights/Components/GetAvailableBillingFeatures/Read | Čtení funkcí vyúčtování dostupných pro součást Application Insights |
> | Action | Microsoft. Insights/komponenty/GetToken/číst | Načítá se token součásti Application Insights. |
> | Action | Microsoft. Insights/Components/MetricDefinitions/Read | Načítají se definice metriky Application Insights komponenty. |
> | Action | Microsoft. Insights/komponenty/metriky/číst | Čtení Application Insights metriky komponent |
> | Action | Microsoft. Insights/Components/Move/Action | Přesunutí součásti Application Insights do jiné skupiny prostředků nebo předplatného |
> | Action | Microsoft. Insights/Components/MyAnalyticsItems/DELETE | Odstranění Application Insights osobní položky analýzy |
> | Action | Microsoft. Insights/Components/MyAnalyticsItems/Read | Čtení položky Application Insights osobní analýzy |
> | Action | Microsoft. Insights/Components/MyAnalyticsItems/Write | Zápis položky Application Insights osobní analýzy |
> | Action | Microsoft. Insights/Components/MyFavorites/Read | Čtení osobní oblíbené položky Application Insights |
> | Action | Microsoft. Insights/komponenty/Operations/Read | Získat stav dlouhotrvajících operací v Application Insights |
> | Action | Microsoft. Insights/Components/PricingPlans/Read | Čtení cenového plánu součásti Application Insights |
> | Action | Microsoft. Insights/Components/PricingPlans/Write | Zápis cenového plánu součásti Application Insights |
> | Action | Microsoft. Insights/Components/ProactiveDetectionConfigs/Read | Čte se konfigurace Application Insights proaktivní detekce. |
> | Action | Microsoft. Insights/Components/ProactiveDetectionConfigs/Write | Zápis konfigurace Application Insights proaktivní detekce |
> | Action | Microsoft. Insights/Components/Providers/Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Action | Microsoft. Insights/komponenty/vyprázdnění/akce | Vymazání dat z Application Insights |
> | Action | Microsoft. Insights/Components/Query/Read | Spouštění dotazů proti protokolům Application Insights |
> | Action | Microsoft. Insights/Components/QuotaStatus/Read | Načítá se stav kvóty pro součást Application Insights. |
> | Action | Microsoft. Insights/komponenty/číst | Čtení konfigurace komponenty Application Insights |
> | Action | Microsoft. Insights/Components/SyntheticMonitorLocations/Read | Čtení Application Insights umístění WebTest |
> | Action | Microsoft. Insights/komponenty/webtests/číst | Čtení konfigurace webového testu |
> | Action | Microsoft. Insights/Components/WorkItemConfigs/DELETE | Odstraňování konfigurace integrace Application Insights ALM |
> | Action | Microsoft. Insights/Components/WorkItemConfigs/Read | Čtení konfigurace integrace Application Insights ALM |
> | Action | Microsoft. Insights/Components/WorkItemConfigs/Write | Zápis konfigurace integrace Application Insights ALM |
> | Action | Microsoft. Insights/komponenty/Write | Zápis do konfigurace komponenty Application Insights |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | Odstranění nastavení diagnostiky prostředků |
> | Action | Microsoft. Insights/DiagnosticSettings/Read | Přečíst nastavení diagnostiky prostředků |
> | Action | Microsoft. Insights/DiagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředků. |
> | Action | Microsoft.Insights/EventCategories/Read | Číst dostupné kategorie událostí protokolu aktivit |
> | Action | Microsoft.Insights/eventtypes/digestevents/Read | Výtah čtení typu události správy |
> | Action | Microsoft. Insights/EventTypes/Values/Read | Číst události protokolu aktivit |
> | Action | Microsoft. Insights/ExtendedDiagnosticSettings/DELETE | Odstraní nastavení diagnostiky protokolu síťového toku. |
> | Action | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Přečíst nastavení diagnostiky protokolů síťových toků |
> | Action | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky protokolu síťového toku. |
> | Action | Microsoft.Insights/ListMigrationDate/Action | Získat datum migrace předplatného |
> | Action | Microsoft. Insights/ListMigrationDate/Read | Získat datum migrace předplatného |
> | Action | Microsoft. Insights/LogDefinitions/Read | Čtení definic protokolů |
> | Action | Microsoft.Insights/LogProfiles/Delete | Odstraní profil protokolu aktivit. |
> | Action | Microsoft.Insights/LogProfiles/Read | Číst profil protokolu aktivit |
> | Action | Microsoft.Insights/LogProfiles/Write | Vytvoří nebo aktualizuje profil protokolu aktivit. |
> | Action | Microsoft. Insights/logs/ADAssessmentRecommendation/číst | Čtení dat z tabulky ADAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/ADReplicationResult/číst | Čtení dat z tabulky ADReplicationResult |
> | Action | Microsoft. Insights/logs/ADSecurityAssessmentRecommendation/číst | Čtení dat z tabulky ADSecurityAssessmentRecommendation |
> | Action | Microsoft. Insights/protokoly/výstraha/čtení | Čtení dat z tabulky výstrah |
> | Action | Microsoft.Insights/Logs/AlertHistory/Read | Čtení dat z tabulky AlertHistory |
> | Action | Microsoft.Insights/Logs/ApplicationInsights/Read | Čtení dat z tabulky ApplicationInsights |
> | Action | Microsoft. Insights/logs/AzureActivity/číst | Čtení dat z tabulky AzureActivity |
> | Action | Microsoft. Insights/logs/AzureMetrics/číst | Čtení dat z tabulky AzureMetrics |
> | Action | Microsoft. Insights/logs/BoundPort/číst | Čtení dat z tabulky BoundPort |
> | Action | Microsoft.Insights/Logs/CommonSecurityLog/Read | Čtení dat z tabulky CommonSecurityLog |
> | Action | Microsoft. Insights/protokoly/počítač/číst | Čtení dat z tabulky počítačů |
> | Action | Microsoft. Insights/logs/ConfigurationChange/číst | Čtení dat z tabulky ConfigurationChange |
> | Action | Microsoft. Insights/logs/ConfigurationData/číst | Čtení dat z tabulky ConfigurationData |
> | Action | Microsoft. Insights/logs/ContainerImageInventory/číst | Čtení dat z tabulky ContainerImageInventory |
> | Action | Microsoft. Insights/logs/ContainerInventory/číst | Čtení dat z tabulky ContainerInventory |
> | Action | Microsoft.Insights/Logs/ContainerLog/Read | Čtení dat z tabulky ContainerLog |
> | Action | Microsoft.Insights/Logs/ContainerServiceLog/Read | Čtení dat z tabulky ContainerServiceLog |
> | Action | Microsoft.Insights/Logs/DeviceAppCrash/Read | Čtení dat z tabulky DeviceAppCrash |
> | Action | Microsoft. Insights/logs/DeviceAppLaunch/číst | Čtení dat z tabulky DeviceAppLaunch |
> | Action | Microsoft. Insights/logs/DeviceCalendar/číst | Čtení dat z tabulky DeviceCalendar |
> | Action | Microsoft. Insights/logs/DeviceCleanup/číst | Čtení dat z tabulky DeviceCleanup |
> | Action | Microsoft. Insights/logs/DeviceConnectSession/číst | Čtení dat z tabulky DeviceConnectSession |
> | Action | Microsoft. Insights/logs/DeviceEtw/číst | Čtení dat z tabulky DeviceEtw |
> | Action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Čtení dat z tabulky DeviceHardwareHealth |
> | Action | Microsoft.Insights/Logs/DeviceHealth/Read | Čtení dat z tabulky přidružený |
> | Action | Microsoft. Insights/logs/DeviceHeartbeat/číst | Čtení dat z tabulky DeviceHeartbeat |
> | Action | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Čtení dat z tabulky DeviceSkypeHeartbeat |
> | Action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Čtení dat z tabulky DeviceSkypeSignIn |
> | Action | Microsoft.Insights/Logs/DeviceSleepState/Read | Čtení dat z tabulky DeviceSleepState |
> | Action | Microsoft. Insights/logs/DHAppFailure/číst | Čtení dat z tabulky DHAppFailure |
> | Action | Microsoft. Insights/logs/DHAppReliability/číst | Čtení dat z tabulky DHAppReliability |
> | Action | Microsoft. Insights/logs/DHDriverReliability/číst | Čtení dat z tabulky DHDriverReliability |
> | Action | Microsoft. Insights/logs/DHLogonFailures/číst | Čtení dat z tabulky DHLogonFailures |
> | Action | Microsoft. Insights/logs/DHLogonMetrics/číst | Čtení dat z tabulky DHLogonMetrics |
> | Action | Microsoft. Insights/logs/DHOSCrashData/číst | Čtení dat z tabulky DHOSCrashData |
> | Action | Microsoft. Insights/logs/DHOSReliability/číst | Čtení dat z tabulky DHOSReliability |
> | Action | Microsoft.Insights/Logs/DHWipAppLearning/Read | Čtení dat z tabulky DHWipAppLearning |
> | Action | Microsoft. Insights/logs/DnsEvents/číst | Čtení dat z tabulky DnsEvents |
> | Action | Microsoft. Insights/logs/DnsInventory/číst | Čtení dat z tabulky DnsInventory |
> | Action | Microsoft. Insights/logs/ETWEvent/číst | Čtení dat z tabulky ETWEvent |
> | Action | Microsoft. Insights/protokoly/událost/čtení | Čtení dat z tabulky událostí |
> | Action | Microsoft. Insights/logs/ExchangeAssessmentRecommendation/číst | Čtení dat z tabulky ExchangeAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/ExchangeOnlineAssessmentRecommendation/číst | Čtení dat z tabulky ExchangeOnlineAssessmentRecommendation |
> | Action | Microsoft. Insights/protokoly/prezenční signál/čtení | Čtení dat z tabulky prezenčního signálu |
> | Action | Microsoft. Insights/logs/IISAssessmentRecommendation/číst | Čtení dat z tabulky IISAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/InboundConnection/číst | Čtení dat z tabulky InboundConnection |
> | Action | Microsoft.Insights/Logs/KubeNodeInventory/Read | Čtení dat z tabulky KubeNodeInventory |
> | Action | Microsoft.Insights/Logs/KubePodInventory/Read | Čtení dat z tabulky KubePodInventory |
> | Action | Microsoft.Insights/Logs/LinuxAuditLog/Read | Čtení dat z tabulky LinuxAuditLog |
> | Action | Microsoft. Insights/logs/MAApplication/číst | Čtení dat z tabulky MAApplication |
> | Action | Microsoft.Insights/Logs/MAApplicationHealth/Read | Čtení dat z tabulky MAApplicationHealth |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Čtení dat z tabulky MAApplicationHealthAlternativeVersions |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Čtení dat z tabulky MAApplicationHealthIssues |
> | Action | Microsoft. Insights/logs/MAApplicationInstance/číst | Čtení dat z tabulky MAApplicationInstance |
> | Action | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Čtení dat z tabulky MAApplicationInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Čtení dat z tabulky MAApplicationReadiness |
> | Action | Microsoft. Insights/logs/MADeploymentPlan/číst | Čtení dat z tabulky MADeploymentPlan |
> | Action | Microsoft. Insights/logs/MADevice/číst | Čtení dat z tabulky MADevice |
> | Action | Microsoft. Insights/logs/MADevicePnPHealth/číst | Čtení dat z tabulky MADevicePnPHealth |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Čtení dat z tabulky MADevicePnPHealthAlternativeVersions |
> | Action | Microsoft. Insights/logs/MADevicePnPHealthIssues/číst | Čtení dat z tabulky MADevicePnPHealthIssues |
> | Action | Microsoft. Insights/logs/MADeviceReadiness/číst | Čtení dat z tabulky MADeviceReadiness |
> | Action | Microsoft. Insights/logs/MADriverInstanceReadiness/číst | Čtení dat z tabulky MADriverInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MADriverReadiness/Read | Čtení dat z tabulky MADriverReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeAddin/Read | Čtení dat z tabulky MAOfficeAddin |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Čtení dat z tabulky MAOfficeAddinHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Čtení dat z tabulky MAOfficeAddinHealthIssues |
> | Action | Microsoft. Insights/logs/MAOfficeAddinInstance/číst | Čtení dat z tabulky MAOfficeAddinInstance |
> | Action | Microsoft. Insights/logs/MAOfficeAddinInstanceReadiness/číst | Čtení dat z tabulky MAOfficeAddinInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Čtení dat z tabulky MAOfficeAddinReadiness |
> | Action | Microsoft. Insights/logs/MAOfficeApp/číst | Čtení dat z tabulky MAOfficeApp |
> | Action | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Čtení dat z tabulky MAOfficeAppHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Čtení dat z tabulky MAOfficeAppInstance |
> | Action | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Čtení dat z tabulky MAOfficeAppReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Čtení dat z tabulky MAOfficeBuildInfo |
> | Action | Microsoft. Insights/logs/MAOfficeCurrencyAssessment/číst | Čtení dat z tabulky MAOfficeCurrencyAssessment |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Čtení dat z tabulky MAOfficeCurrencyAssessmentDailyCounts |
> | Action | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Čtení dat z tabulky MAOfficeDeploymentStatus |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Čtení dat z tabulky MAOfficeMacroHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Čtení dat z tabulky MAOfficeMacroHealthIssues |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Čtení dat z tabulky MAOfficeMacroIssueInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Čtení dat z tabulky MAOfficeMacroIssueReadiness |
> | Action | Microsoft. Insights/logs/MAOfficeMacroSummary/číst | Čtení dat z tabulky MAOfficeMacroSummary |
> | Action | Microsoft.Insights/Logs/MAOfficeSuite/Read | Čtení dat z tabulky MAOfficeSuite |
> | Action | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Čtení dat z tabulky MAOfficeSuiteInstance |
> | Action | Microsoft. Insights/logs/MAProposedPilotDevices/číst | Čtení dat z tabulky MAProposedPilotDevices |
> | Action | Microsoft. Insights/logs/MAWindowsBuildInfo/číst | Čtení dat z tabulky MAWindowsBuildInfo |
> | Action | Microsoft. Insights/logs/MAWindowsCurrencyAssessment/číst | Čtení dat z tabulky MAWindowsCurrencyAssessment |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Čtení dat z tabulky MAWindowsCurrencyAssessmentDailyCounts |
> | Action | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Čtení dat z tabulky MAWindowsDeploymentStatus |
> | Action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Čtení dat z tabulky MAWindowsSysReqInstanceReadiness |
> | Action | Microsoft. Insights/logs/NetworkMonitoring/číst | Čtení dat z tabulky NetworkMonitoring |
> | Action | Microsoft. Insights/logs/OfficeActivity/číst | Čtení dat z tabulky OfficeActivity |
> | Action | Microsoft. Insights/protokoly/operace/čtení | Čtení dat z tabulky operací |
> | Action | Microsoft. Insights/logs/OutboundConnection/číst | Čtení dat z tabulky OutboundConnection |
> | Action | Microsoft. Insights/protokoly/výkon/čtení | Čtení dat z tabulky perf |
> | Action | Microsoft. Insights/logs/ProtectionStatus/číst | Čtení dat z tabulky ProtectionStatus |
> | Action | Microsoft. Insights/protokoly/číst | Čtení dat ze všech protokolů |
> | Action | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Čtení dat z tabulky ReservedAzureCommonFields |
> | Action | Microsoft. Insights/logs/ReservedCommonFields/číst | Čtení dat z tabulky ReservedCommonFields |
> | Action | Microsoft. Insights/logs/SCCMAssessmentRecommendation/číst | Čtení dat z tabulky SCCMAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/SCOMAssessmentRecommendation/číst | Čtení dat z tabulky SCOMAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/SecurityAlert/číst | Čtení dat z tabulky SecurityAlert |
> | Action | Microsoft.Insights/Logs/SecurityBaseline/Read | Čtení dat z tabulky SecurityBaseline |
> | Action | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Čtení dat z tabulky SecurityBaselineSummary |
> | Action | Microsoft. Insights/logs/SecurityDetection/číst | Čtení dat z tabulky SecurityDetection |
> | Action | Microsoft. Insights/logs/SecurityEvent/číst | Čtení dat z tabulky SecurityEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Čtení dat z tabulky ServiceFabricOperationalEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Čtení dat z tabulky ServiceFabricReliableActorEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Čtení dat z tabulky ServiceFabricReliableServiceEvent |
> | Action | Microsoft. Insights/logs/SfBAssessmentRecommendation/číst | Čtení dat z tabulky SfBAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/SfBOnlineAssessmentRecommendation/číst | Čtení dat z tabulky SfBOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Čtení dat z tabulky SharePointOnlineAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/SPAssessmentRecommendation/číst | Čtení dat z tabulky SPAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/SQLAssessmentRecommendation/číst | Čtení dat z tabulky SQLAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Čtení dat z tabulky SQLQueryPerformance |
> | Action | Microsoft. Insights/protokoly/syslog/Read | Čtení dat z tabulky syslog |
> | Action | Microsoft. Insights/logs/SysmonEvent/číst | Čtení dat z tabulky SysmonEvent |
> | Action | Microsoft. Insights/logs/UAApp/číst | Čtení dat z tabulky UAApp |
> | Action | Microsoft. Insights/logs/UAComputer/číst | Čtení dat z tabulky UAComputer |
> | Action | Microsoft. Insights/logs/UAComputerRank/číst | Čtení dat z tabulky UAComputerRank |
> | Action | Microsoft. Insights/logs/UADriver/číst | Čtení dat z tabulky UADriver |
> | Action | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Čtení dat z tabulky UADriverProblemCodes |
> | Action | Microsoft. Insights/logs/UAFeedback/číst | Čtení dat z tabulky UAFeedback |
> | Action | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Čtení dat z tabulky UAHardwareSecurity |
> | Action | Microsoft. Insights/logs/UAIESiteDiscovery/číst | Čtení dat z tabulky UAIESiteDiscovery |
> | Action | Microsoft. Insights/logs/UAOfficeAddIn/číst | Čtení dat z tabulky UAOfficeAddIn |
> | Action | Microsoft. Insights/logs/UAProposedActionPlan/číst | Čtení dat z tabulky UAProposedActionPlan |
> | Action | Microsoft.Insights/Logs/UASysReqIssue/Read | Čtení dat z tabulky UASysReqIssue |
> | Action | Microsoft. Insights/logs/UAUpgradedComputer/číst | Čtení dat z tabulky UAUpgradedComputer |
> | Action | Microsoft. Insights/protokoly/aktualizovat/číst | Čtení dat z tabulky aktualizací |
> | Action | Microsoft.Insights/Logs/UpdateRunProgress/Read | Čtení dat z tabulky UpdateRunProgress |
> | Action | Microsoft.Insights/Logs/UpdateSummary/Read | Čtení dat z tabulky UpdateSummary |
> | Action | Microsoft. Insights/protokoly/využití/čtení | Čtení dat z tabulky využití |
> | Action | Microsoft.Insights/Logs/W3CIISLog/Read | Čtení dat z tabulky W3CIISLog |
> | Action | Microsoft. Insights/logs/WaaSDeploymentStatus/číst | Čtení dat z tabulky WaaSDeploymentStatus |
> | Action | Microsoft. Insights/logs/WaaSInsiderStatus/číst | Čtení dat z tabulky WaaSInsiderStatus |
> | Action | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Čtení dat z tabulky WaaSUpdateStatus |
> | Action | Microsoft. Insights/logs/WDAVStatus/číst | Čtení dat z tabulky WDAVStatus |
> | Action | Microsoft. Insights/logs/WDAVThreat/číst | Čtení dat z tabulky WDAVThreat |
> | Action | Microsoft. Insights/logs/WindowsClientAssessmentRecommendation/číst | Čtení dat z tabulky WindowsClientAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/WindowsFirewall/číst | Čtení dat z tabulky WindowsFirewall |
> | Action | Microsoft. Insights/logs/WindowsServerAssessmentRecommendation/číst | Čtení dat z tabulky WindowsServerAssessmentRecommendation |
> | Action | Microsoft. Insights/logs/WireData/číst | Čtení dat z tabulky WireData |
> | Action | Microsoft. Insights/logs/WUDOAggregatedStatus/číst | Čtení dat z tabulky WUDOAggregatedStatus |
> | Action | Microsoft. Insights/logs/WUDOStatus/číst | Čtení dat z tabulky WUDOStatus |
> | Action | Microsoft.Insights/MetricAlerts/Delete | Odstranění výstrahy metriky |
> | Action | Microsoft. Insights/MetricAlerts/Read | Čtení výstrahy metriky |
> | Action | Microsoft. Insights/MetricAlerts/stav/čtení | Přečíst stav výstrahy metriky |
> | Action | Microsoft. Insights/MetricAlerts/Write | Vytvoření nebo aktualizace výstrahy metriky |
> | Action | Microsoft.Insights/MetricBaselines/Read | Čtení standardních hodnot metriky |
> | Action | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/Read | Čtení definic metriky |
> | Action | Microsoft. Insights/MetricDefinitions/Providers/Microsoft. Insights/Read | Čtení definic metriky |
> | Action | Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Action | Microsoft. Insights/Metricnamespaces/Read | Číst obory názvů metriky |
> | Action | Microsoft. Insights/metriky/akce | Akce metriky |
> | Action | Microsoft. Insights/metriky/Microsoft. Insights/číst | Čtení metrik |
> | Action | Microsoft. Insights/metriky/zprostředkovatelé/metriky/čtení | Čtení metrik |
> | Action | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Akce dataaction | Microsoft. Insights/metriky/Write | Zapsat metriky |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrovat předplatné do nového cenového modelu |
> | Action | Microsoft. Insights/Operations/Read | Operace čtení |
> | Action | Microsoft. Insights/registr/Action | Registrace poskytovatele Microsoft Insights |
> | Action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Vrátit zpět předplatné na starší verzi cenového modelu |
> | Action | Microsoft.Insights/ScheduledQueryRules/Delete | Odstranění pravidla plánovaného dotazu |
> | Action | Microsoft.Insights/ScheduledQueryRules/Read | Načetlo se pravidlo naplánovaného dotazu. |
> | Action | Microsoft.Insights/ScheduledQueryRules/Write | Zápis pravidla plánovaného dotazu |
> | Action | Microsoft. Insights/klienti/registrovat/akce | Inicializuje poskytovatele Microsoft Insights. |
> | Action | Microsoft.Insights/Unregister/Action | Registrace poskytovatele Microsoft Insights |
> | Action | Microsoft.Insights/Webtests/Delete | Odstranění konfigurace webového testu |
> | Action | Microsoft. Insights/webtests/GetToken/číst | Čtení tokenu WebTest |
> | Action | Microsoft. Insights/webtests/MetricDefinitions/Read | Čtení definic metriky webového testu |
> | Action | Microsoft. Insights/webtests/metriky/číst | Čtení metrik webového testu |
> | Action | Microsoft. Insights/webtests/číst | Čtení konfigurace webového testu |
> | Action | Microsoft. Insights/webtests/Write | Zápis do konfigurace webového testu |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Intune/diagnosticsettings/delete | Odstranění nastavení diagnostiky |
> | Action | Microsoft. Intune/diagnosticsettings/Read | Čtení nastavení diagnostiky |
> | Action | Microsoft.Intune/diagnosticsettings/write | Zápis nastavení diagnostiky |
> | Action | Microsoft. Intune/diagnosticsettingscategories/Read | Čtení kategorií nastavení diagnostiky |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. IoTCentral/appTemplates/Action | Získá všechny dostupné šablony aplikací v Azure IoT Central |
> | Action | Microsoft. IoTCentral/checkNameAvailability/Action | Zkontroluje, jestli je k dispozici IoT Central název aplikace. |
> | Action | Microsoft.IoTCentral/checkSubdomainAvailability/action | Kontroluje, zda je k dispozici IoT Central subdoména aplikace. |
> | Action | Microsoft.IoTCentral/IoTApps/delete | Odstraní aplikace IoT Central. |
> | Action | Microsoft. IoTCentral/IoTApps/Read | Načte jednu IoT Central aplikaci. |
> | Action | Microsoft. IoTCentral/IoTApps/Write | Vytvoří nebo aktualizuje aplikace IoT Central. |
> | Action | Microsoft.IoTCentral/operations/read | Získá všechny dostupné operace s IoT Central aplikacemi. |
> | Action | Microsoft. IoTCentral/Register/Action | Registrace předplatného pro poskytovatele prostředků Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. IoTSpaces/Graph/DELETE | Odstraní prostředek Microsoft. IoTSpaces Graph. |
> | Action | Microsoft. IoTSpaces/Graph/Read | Načte prostředky grafu Microsoft. IoTSpaces. |
> | Action | Microsoft.IoTSpaces/Graph/write | Vytvořit prostředek Microsoft. IoTSpaces Graph |
> | Action | Microsoft. IoTSpaces/Register/Action | Registrace předplatného pro poskytovatele prostředků Microsoft. IoTSpaces Graph pro povolení vytváření prostředků |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. checkNameAvailability trezor//Read | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | Action | Microsoft.KeyVault/deletedVaults/read | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | Action | Microsoft. a trezor/hsmPools/DELETE | Odstranění fondu HSM |
> | Action | Microsoft. joinVault trezor/hsmPools//Action | Připojte Trezor klíčů k fondu HSM. |
> | Action | Microsoft. hsmPools trezor//Read | Zobrazení vlastností fondu HSM |
> | Action | Microsoft. hsmPools – trezor//Write | Vytvoří nový fond HSM, ve kterém se aktualizuje vlastnosti existujícího fondu HSM. |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | Vymazání tichého odstraněného trezoru klíčů |
> | Action | Microsoft.KeyVault/locations/deletedVaults/read | Zobrazit vlastnosti neodstraněného trezoru klíčů |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Upozorní Microsoft. webtrezor, že se odstraňuje virtuální síť nebo podsíť. |
> | Action | Microsoft.KeyVault/locations/operationResults/read | Zkontroluje výsledek dlouhotrvající operace spuštění. |
> | Action | Microsoft.KeyVault/operations/read | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | Action | Microsoft... trezor/registrace/akce | Zaregistruje předplatné. |
> | Action | Microsoft.KeyVault/unregister/action | Zruší registraci předplatného. |
> | Action | Trezory Microsoft. a trezory/accessPolicies/Write | Aktualizujte existující zásady přístupu sloučením nebo nahrazením nebo přidejte nové zásady přístupu do trezoru. |
> | Action | Trezor Microsoft. a trezory/odstranění | Odstranění trezoru klíčů |
> | Action | Trezor Microsoft. a trezory/nasazení/akce | Umožňuje přístup k tajným klíčům v trezoru klíčů při nasazování prostředků Azure. |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/delete | Upozorní Microsoft. webtrezor, že se odstraňuje EventGrid odběr pro Key Vault. |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/read | Upozorní Microsoft. webtrezor, že se zobrazuje EventGrid odběr pro Key Vault. |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/write | Upozorní Microsoft. webtrezor, že se vytváří nové předplatné EventGrid pro Key Vault. |
> | Action | Trezor Microsoft. a trezory/čtení | Zobrazení vlastností trezoru klíčů |
> | Action | Trezor Microsoft. a trezory/tajné klíče/čtení | Umožňuje zobrazit vlastnosti tajného klíče, ale ne jeho hodnotu. |
> | Action | Trezor Microsoft. a trezory/tajné klíče/zápis | Vytvoří nový tajný klíč nebo aktualizuje hodnotu existujícího tajného kódu. |
> | Action | Microsoft.KeyVault/vaults/write | Vytvoří nový trezor klíčů nebo aktualizuje vlastnosti existujícího trezoru klíčů. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Kusto/Clusters/Activate/Action | Spustí cluster. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Odstraní prostředek konfigurace připojené databáze. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Načte prostředek konfigurace připojené databáze. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Zapíše prostředek připojené konfigurace databáze. |
> | Action | Microsoft. Kusto/Clusters/CheckNameAvailability/Action | Kontroluje dostupnost názvu clusteru. |
> | Action | Microsoft. Kusto/clustery/databáze/AddPrincipals/Action | Přidá objekty zabezpečení databáze. |
> | Action | Microsoft. Kusto/clustery/databáze/CheckNameAvailability/Action | Kontroluje dostupnost názvu pro daný typ. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Odstraní prostředek datových připojení. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Přečte prostředek datových připojení. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Zapíše prostředek datových připojení. |
> | Action | Microsoft. Kusto/clustery/databáze/DataConnectionValidation/Action | Ověří datové připojení databáze. |
> | Action | Microsoft.Kusto/Clusters/Databases/delete | Odstraní prostředek databáze. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Odstraní prostředek připojení centra událostí. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Přečte prostředek připojení centra událostí. |
> | Action | Microsoft. Kusto/clustery/databáze/EventHubConnections/Write | Zapíše prostředek připojení centra událostí. |
> | Action | Microsoft. Kusto/clustery/databáze/EventHubConnectionValidation/Action | Ověří připojení centra událostí databáze. |
> | Action | Microsoft. Kusto/clustery/databáze/ListPrincipals/Action | Seznam objektů zabezpečení databáze. |
> | Action | Microsoft.Kusto/Clusters/Databases/read | Přečte prostředek databáze. |
> | Action | Microsoft. Kusto/clustery/databáze/RemovePrincipals/Action | Odebere objekty zabezpečení databáze. |
> | Action | Microsoft.Kusto/Clusters/Databases/write | Zapíše prostředek databáze. |
> | Action | Microsoft. Kusto/Clusters/deaktivovat/Action | Zastaví cluster. |
> | Action | Microsoft.Kusto/Clusters/delete | Odstraní prostředek clusteru. |
> | Action | Microsoft.Kusto/Clusters/read | Přečte prostředek clusteru. |
> | Action | Microsoft. Kusto/Clusters/SKU/číst | Přečte prostředek SKU clusteru. |
> | Action | Microsoft. Kusto/Clusters/Start/Action | Spustí cluster. |
> | Action | Microsoft. Kusto/Clusters/stop/Action | Zastaví cluster. |
> | Action | Microsoft.Kusto/Clusters/write | Zapíše prostředek clusteru. |
> | Action | Microsoft. Kusto/Locations/CheckNameAvailability/Action | Zkontroluje dostupnost názvu prostředku. |
> | Action | Microsoft.Kusto/locations/operationresults/read | Načte provozní prostředky. |
> | Action | Microsoft.Kusto/Operations/read | Načte provozní prostředky. |
> | Action | Microsoft. Kusto/Register/Action | Zaregistruje předplatné do poskytovatele prostředků Kusto. |
> | Action | Microsoft.Kusto/SKUs/read | Přečte prostředek SKU. |
> | Action | Microsoft. Kusto/zrušit registraci/akce | Zruší registraci předplatného pro poskytovatele prostředků Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | Vytvořte testovací prostředí v účtu testovacího prostředí. |
> | Action | Microsoft.LabServices/labAccounts/delete | Odstraní účty testovacího prostředí. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | Odstranit image z Galerie |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/read | Čtení imagí galerie. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/write | Přidání nebo úprava imagí galerie. |
> | Action | Microsoft. LabServices/labAccounts/GetRegionalAvailability/Action | Získat informace o regionální dostupnosti pro každou kategorii velikostí nakonfigurovanou v rámci účtu testovacího prostředí |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/DELETE | Odstraňte idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Read | Přečtěte si idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Write | Přidat nebo upravit idleshutdowns |
> | Action | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Přidání uživatelů do testovacího prostředí |
> | Action | Microsoft.LabServices/labAccounts/labs/delete | Odstraňte Labs. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Deklarace náhodného prostředí pro uživatele v nastavení prostředí |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Odstranit nastavení prostředí. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Vydává deklarace do prostředí a přiřadí ho uživateli. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Odstraňte prostředí. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Čtení prostředí. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/prostředí/ResetPassword/Action | Resetování hesla uživatele v prostředí |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Spustí prostředí spuštěním všech prostředků v prostředí. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environment/stop/Action | Zastaví prostředí zastavením všech prostředků v prostředí. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Přidejte nebo upravte prostředí. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Publish/Action | Zřídí nebo odkládá požadované prostředky pro nastavení prostředí na základě aktuálního stavu nastavení testovacího prostředí nebo prostředí. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Čtení nastavení prostředí. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/Action | Resetuje heslo na virtuálním počítači šablony. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Uloží aktuální image šablony do sdílené galerie v účtu testovacího prostředí. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Odstraňte plány. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Načtou se plány. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Přidat nebo upravit plány. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Spustí šablonu spuštěním všech prostředků v rámci šablony. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Zastaví šablonu zastavením všech prostředků v šabloně. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Přidat nebo upravit nastavení prostředí |
> | Action | Microsoft.LabServices/labAccounts/labs/read | Přečtěte si Labs. |
> | Action | Microsoft. LabServices/labAccounts/Labs/Register/Action | Zaregistrujte se do spravovaného testovacího prostředí. |
> | Action | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Odeslání e-mailu s odkazem na registraci do testovacího prostředí |
> | Action | Microsoft.LabServices/labAccounts/labs/users/delete | Odstranit uživatele. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/read | Čtení uživatelů. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | Přidejte nebo upravte uživatele. |
> | Action | Microsoft.LabServices/labAccounts/labs/write | Přidat nebo upravit cvičení. |
> | Action | Microsoft.LabServices/labAccounts/read | Čtení účtů testovacího prostředí. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Odstraňte sharedgalleries. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/read | Přečtěte si sharedgalleries. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/write | Přidat nebo upravit sharedgalleries |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/delete | Odstraňte sharedimages. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/read | Přečtěte si sharedimages. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/write | Přidat nebo upravit sharedimages |
> | Action | Microsoft.LabServices/labAccounts/write | Přidat nebo upravit účty testovacího prostředí. |
> | Action | Microsoft.LabServices/locations/operations/read | Operace čtení. |
> | Action | Microsoft. LabServices/Register/Action | Zaregistruje předplatné. |
> | Action | Microsoft. LabServices/uživatelé/getenvironment/Action | Získá podrobnosti o virtuálním počítači. |
> | Action | Microsoft. LabServices/uživatelé/GetOperationBatchStatus/Action | Získat stav operace dávky |
> | Action | Microsoft. LabServices/uživatelé/GetOperationStatus/Action | Získá stav dlouho běžící operace. |
> | Action | Microsoft. LabServices/uživatelé/GetPersonalPreferences/Action | Získat osobní preference pro uživatele |
> | Action | Microsoft. LabServices/uživatelé/ListAllEnvironments/Action | Vypíše všechna prostředí pro uživatele. |
> | Action | Microsoft. LabServices/uživatelé/ListEnvironments/Action | Seznam prostředí pro uživatele |
> | Action | Microsoft. LabServices/uživatelé/ListLabs/Action | Vypíše cvičení pro uživatele. |
> | Action | Microsoft. LabServices/uživatelé/registrace/akce | Registrace uživatele ke spravovanému testovacímu prostředí |
> | Action | Microsoft. LabServices/uživatelé/ResetPassword/Action | Resetování hesla uživatele v prostředí |
> | Action | Microsoft. LabServices/uživatelé/StartEnvironment/Action | Spustí prostředí spuštěním všech prostředků v prostředí. |
> | Action | Microsoft. LabServices/uživatelé/StopEnvironment/Action | Zastaví prostředí zastavením všech prostředků v prostředí. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Logic/integrationAccounts/Agreements/DELETE | Odstraní smlouvu v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Agreements/listContentCallbackUrl/Action | Získá adresu URL zpětného volání pro obsah smlouvy v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Agreements/Read | Přečte smlouvu v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Agreements/Write | Vytvoří nebo aktualizuje smlouvu v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Assemblies/DELETE | Odstraní sestavení v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah sestavení v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Assemblies/Read | Přečte sestavení v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Assemblies/Write | Vytvoří nebo aktualizuje sestavení v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Odstraní konfiguraci dávky v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Přečte konfiguraci dávky v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Vytvoří nebo aktualizuje konfiguraci dávky v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | Odstraní certifikát v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Certificates/Read | Přečte certifikát v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | Vytvoří nebo aktualizuje certifikát v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/delete | Odstraní účet pro integraci. |
> | Action | Microsoft. Logic/integrationAccounts/JOIN/Action | Připojí se k účtu pro integraci. |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Získá adresu URL zpětného volání pro účet pro integraci. |
> | Action | Microsoft. Logic/integrationAccounts/listKeyVaultKeys/Action | Získá klíče v trezoru klíčů. |
> | Action | Microsoft. Logic/integrationAccounts/logTrackingEvents/Action | Protokoluje události sledování v účtu pro integraci. |
> | Action | Microsoft.Logic/integrationAccounts/maps/delete | Odstraní mapu v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah mapy v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Maps/Read | Přečte mapu v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | Vytvoří nebo aktualizuje mapu v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | Odstraní partnera v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah partnera v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | Přečte partnera v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | Vytvoří nebo aktualizuje partnera v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Přečte definice protokolu účtu pro integraci. |
> | Action | Microsoft.Logic/integrationAccounts/read | Přečte účet pro integraci. |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Obnoví tajné klíče přístupových klíčů. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | Odstraní schéma v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah schématu v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | Přečte schéma v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | Vytvoří nebo aktualizuje schéma v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | Odstraní relaci v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Sessions/Read | Přečte konfiguraci dávky v účtu integrace. |
> | Action | Microsoft. Logic/integrationAccounts/Sessions/Write | Vytvoří nebo aktualizuje relaci v účtu integrace. |
> | Action | Microsoft.Logic/integrationAccounts/write | Vytvoří nebo aktualizuje účet pro integraci. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | Odstraní prostředí integrační služby. |
> | Action | Microsoft. Logic/integrationServiceEnvironments/JOIN/Action | Spojí prostředí integrační služby. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Přečte operaci rozhraní API spravovaného prostředí integrační služby. |
> | Action | Microsoft. Logic/integrationServiceEnvironments/Inspirujte/Read | Přečte spravované rozhraní API prostředí integrační služby. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Přečte stavy operací prostředí integrační služby. |
> | Action | Microsoft. Logic/integrationServiceEnvironments/Providers/Microsoft. Insights/metricDefinitions/Read | Přečte definice metrik prostředí služby Integration Service. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | Přečte prostředí integrační služby. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | Vytvoří nebo aktualizuje prostředí integrační služby. |
> | Action | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Získá pracovní postup doporučit skupiny operací. |
> | Action | Microsoft. Logic/Locations/Workflows/Validate/Action | Ověří pracovní postup. |
> | Action | Microsoft.Logic/operations/read | Získá operaci. |
> | Action | Microsoft. Logic/Register/Action | Zaregistruje poskytovatele prostředků Microsoft. Logic pro dané předplatné. |
> | Action | Microsoft. Logic/Workflows/accessKeys/DELETE | Odstraní přístupový klíč. |
> | Action | Microsoft. Logic/Workflows/accessKeys/list/Action | Zobrazí seznam tajných klíčů přístupu. |
> | Action | Microsoft. Logic/Workflows/accessKeys/Read | Přečte přístupový klíč. |
> | Action | Microsoft. Logic/Workflows/accessKeys/regenerate/Action | Obnoví tajné klíče přístupových klíčů. |
> | Action | Microsoft. Logic/Workflows/accessKeys/Write | Vytvoří nebo aktualizuje přístupový klíč. |
> | Action | Microsoft.Logic/workflows/delete | Odstraní pracovní postup. |
> | Action | Microsoft. Logic/Workflows/Disable/Action | Zakáže pracovní postup. |
> | Action | Microsoft. Logic/Workflows/Enable/Action | Povolí pracovní postup. |
> | Action | Microsoft.Logic/workflows/listCallbackUrl/action | Získá adresu URL zpětného volání pro pracovní postup. |
> | Action | Microsoft. Logic/Workflows/listSwagger/Action | Získá definice Swagger pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/Move/Action | Přesune pracovní postup z existujícího ID předplatného, skupiny prostředků a názvu do jiného ID předplatného, skupinu prostředků nebo název. |
> | Action | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Read | Přečte nastavení diagnostiky pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/logDefinitions/Read | Přečte definice protokolu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/Providers/Microsoft. Insights/metricDefinitions/Read | Přečte definice metrik pracovního postupu. |
> | Action | Microsoft.Logic/workflows/read | Přečte pracovní postup. |
> | Action | Microsoft. Logic/Workflows/regenerateAccessKey/Action | Obnoví tajné klíče přístupových klíčů. |
> | Action | Microsoft. Logic/Workflows/Run/Action | Spustí spuštění pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Actions/listExpressionTraces/Action | Získá trasování výrazu akce běhu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Actions/Read | Přečte akci běhu pracovního postupu. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Získá trasování výrazu opakování akce spuštění pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhu/akce/opakování/čtení | Přečte opakování akce běhu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhu/akce/opakování/requestHistories/Read | Načte historii požadavku akce opakování běhu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Actions/requestHistories/Read | Načte historii požadavku akce běhu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Actions/scoperepetitions/Read | Přečte opakování rozsahu akce běhu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Cancel/Action | Zruší spuštění pracovního postupu. |
> | Action | Microsoft.Logic/workflows/runs/delete | Odstraní spuštění pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Operations/Read | Přečte stav operace běhu pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/běhy/Read | Přečte běh pracovního postupu. |
> | Action | Microsoft. Logic/Workflows/Suspend/Action | Pozastaví pracovní postup. |
> | Action | Microsoft. Logic/Workflows/Triggers/Histories/Read | Přečte historie aktivačních událostí. |
> | Action | Microsoft. Logic/Workflows/Triggers/Histories/Resubmit/Action | Znovu odešle Trigger pracovního postupu. |
> | Action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Získá adresu URL zpětného volání pro aktivační událost. |
> | Action | Microsoft.Logic/workflows/triggers/read | Přečte Trigger. |
> | Action | Microsoft.Logic/workflows/triggers/reset/action | Obnoví aktivační událost. |
> | Action | Microsoft. Logic/Workflows/Triggers/Run/Action | Spustí Trigger. |
> | Action | Microsoft.Logic/workflows/triggers/setState/action | Nastaví stav triggeru. |
> | Action | Microsoft. Logic/Workflows/Validate/Action | Ověří pracovní postup. |
> | Action | Microsoft. Logic/Workflows/verze/čtení | Přečte verzi pracovního postupu. |
> | Action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Získá adresu URL zpětného volání pro aktivační událost. |
> | Action | Microsoft.Logic/workflows/write | Vytvoří nebo aktualizuje pracovní postup. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Move/Action | Přesunout Machine Learning přidružení plánu závazku |
> | Action | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Read | Přečtěte si Machine Learning přidružení plánu závazku. |
> | Action | Microsoft. MachineLearning/commitmentPlans/DELETE | Odstranit jakýkoli plán závazku Machine Learning |
> | Action | Microsoft. MachineLearning/commitmentPlans/JOIN/Action | Zapojit se do plánu závazku Machine Learning |
> | Action | Microsoft. MachineLearning/commitmentPlans/Read | Přečtěte si jakýkoliv plán závazku Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/write | Vytvořit nebo aktualizovat jakýkoliv plán závazku Machine Learning |
> | Action | Microsoft. MachineLearning/Locations/operationresults/Read | Získání výsledku operace Machine Learning |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | Získat stav probíhající Machine Learning operace |
> | Action | Microsoft.MachineLearning/operations/read | Získat Machine Learning operace |
> | Action | Microsoft. MachineLearning/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků webové služby Machine Learning a povolí vytváření webových služeb. |
> | Action | Microsoft. MachineLearning/SKU/číst | Získat Machine Learning SKU plánu závazku |
> | Action | Microsoft. MachineLearning/WebServices/Action | Vytvoření vlastností místních webových služeb pro podporované oblasti |
> | Action | Microsoft.MachineLearning/webServices/delete | Odstranění jakékoli webové služby Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | Získání klíčů pro Machine Learning webovou službu |
> | Action | Microsoft.MachineLearning/webServices/read | Čtení libovolné Machine Learning webové služby |
> | Action | Microsoft.MachineLearning/webServices/write | Vytvoření nebo aktualizace jakékoli webové služby Machine Learning |
> | Action | Microsoft. MachineLearning/pracovní prostory/odstranit | Odstranit všechny pracovní prostor Machine Learning |
> | Action | Microsoft. MachineLearning/pracovní prostory/listworkspacekeys/Action | Seznam klíčů pro pracovní prostor Machine Learning |
> | Action | Microsoft. MachineLearning/pracovní prostory/číst | Číst všechny pracovní prostor Machine Learning |
> | Action | Microsoft. MachineLearning/pracovní prostory/resyncstoragekeys/Action | Opětovná synchronizace klíčů účtu úložiště nakonfigurovaného pro pracovní prostor Machine Learning |
> | Action | Microsoft. MachineLearning/pracovní prostory/zápis | Vytvořit nebo aktualizovat jakékoli pracovní prostor Machine Learning |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. MachineLearningCompute/operationalizationClusters/checkUpdate/Action | Ověřte, jestli jsou pro systémové služby pro cluster pro provoz dostupné aktualizace. |
> | Action | Microsoft. MachineLearningCompute/operationalizationClusters/DELETE | Odstranit libovolný hostitelský účet |
> | Action | Microsoft. MachineLearningCompute/operationalizationClusters/klíče listkey/Action | Výpis klíčů přidružených k clusteringu s provozním využitím |
> | Action | Microsoft. MachineLearningCompute/operationalizationClusters/Read | Číst libovolný hostitelský účet |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Aktualizace systémových služeb v clusteru pro provoz |
> | Action | Microsoft. MachineLearningCompute/operationalizationClusters/Write | Vytvořit nebo aktualizovat libovolný hostitelský účet |
> | Action | Microsoft.MachineLearningCompute/register/action | Zaregistruje ID předplatného poskytovateli prostředků a povolí vytváření výpočetních prostředků machine learningu. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. MachineLearningServices/Locations/computeoperationsstatus/Read | Získá stav konkrétní výpočetní operace. |
> | Action | Microsoft.MachineLearningServices/locations/usages/read | Sestava využití pro výpočetní prostředky AML v předplatném |
> | Action | Microsoft.MachineLearningServices/locations/vmsizes/read | Získání podporovaných velikostí virtuálních počítačů |
> | Action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Získá stav konkrétní operace pracovního prostoru. |
> | Action | Microsoft.MachineLearningServices/register/action | Zaregistruje předplatné pro poskytovatele prostředků Machine Learning Services. |
> | Action | Microsoft. MachineLearningServices/pracovní prostory/výpočty/odstranit | Odstraní výpočetní prostředky v Machine Learning Services pracovních prostorech. |
> | Action | Microsoft. MachineLearningServices/pracovní prostory/výpočty/klíče listkey/Action | Výpis tajných kódů pro výpočetní prostředky v pracovním prostoru Machine Learning Services |
> | Action | Microsoft. MachineLearningServices/pracovní prostory/výpočty/listNodes/Action | Seznam uzlů pro výpočetní prostředky v pracovním prostoru Machine Learning Services |
> | Action | Microsoft. MachineLearningServices/pracovní prostory/výpočetní výkon/čtení | Získá výpočetní prostředky v Machine Learning Services pracovních prostorech. |
> | Action | Microsoft. MachineLearningServices/pracovní prostory/výpočty/zapisovat | Vytvoří nebo aktualizuje výpočetní prostředky v Machine Learning Services pracovních prostorech. |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | Odstraní pracovní prostory Machine Learning Services. |
> | Akce dataaction | Microsoft.MachineLearningServices/workspaces/experiments/write | Vytvoří nebo aktualizuje experimenty v Machine Learning Servicesch pracovních prostorech. |
> | Action | Microsoft. MachineLearningServices/pracovní prostory/klíče listkey/Action | Výpis tajných kódů pro Machine Learning Services pracovní prostor |
> | Action | Microsoft.MachineLearningServices/workspaces/read | Získá Machine Learning Services pracovní prostor (y). |
> | Action | Microsoft.MachineLearningServices/workspaces/write | Vytvoří nebo aktualizuje Machine Learning Services pracovní prostor (y). |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ManagedIdentity/identity/číst | Získá existující identitu přiřazenou systémem. |
> | Action | Microsoft.ManagedIdentity/register/action | Zaregistruje předplatné pro poskytovatele spravovaného prostředku identity. |
> | Action | Microsoft. ManagedIdentity/userAssignedIdentities/Assign/Action | Akce RBAC pro přiřazení existující identity přiřazené uživateli k prostředku |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Odstraní existující identitu přiřazenou uživatelem. |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | Získá existující identitu přiřazenou uživatelem. |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | Vytvoří novou identitu přiřazenou uživatelem nebo aktualizuje značky přidružené k existující identitě přiřazené uživatelem. |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/Read | Načte seznam definic registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/operationStatuses/Read | Přečte stav operace pro daný prostředek. |
> | Action | Microsoft. ManagedServices/Register/Action | Zaregistrujte se do spravovaných služeb. |
> | Action | Microsoft. ManagedServices/registrationAssignments/DELETE | Odebere přiřazení registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/registrationAssignments/Read | Načte seznam přiřazení registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/registrationAssignments/Write | Přidejte nebo upravte přiřazení registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/DELETE | Odebere definici registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/Read | Načte seznam definic registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/Write | Přidat nebo upravit definici registrace spravovaných služeb. |
> | Action | Microsoft. ManagedServices/zrušit registraci/akce | Zrušte registraci ze spravovaných služeb. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Management/checkNameAvailability/Action | Kontroluje, zda je zadaný název skupiny pro správu platný a jedinečný. |
> | Action | Microsoft. Management/getentities/Action | Vypíše všechny entity (Skupiny pro správu, odběry atd.) pro ověřeného uživatele. |
> | Action | Microsoft.Management/managementGroups/delete | Odstraní skupinu pro správu. |
> | Action | Microsoft.Management/managementGroups/read | Vypíše skupiny pro správu ověřeného uživatele. |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | Odpojí předplatné ze skupiny pro správu. |
> | Action | Microsoft.Management/managementGroups/subscriptions/write | Přidruží stávající předplatné ke skupině pro správu. |
> | Action | Microsoft.Management/managementGroups/write | Vytvoří nebo aktualizuje skupinu pro správu. |
> | Action | Microsoft. Management/registr/Action | Zaregistrovat zadané předplatné pro Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce dataaction | Microsoft. Maps/Accounts/data/Read | Udělí datům přístup pro čtení účtu Maps. |
> | Action | Microsoft.Maps/accounts/delete | Odstraní účet Maps. |
> | Action | Microsoft.Maps/accounts/eventGridFilters/delete | Odstranění filtru Event Grid |
> | Action | Microsoft.Maps/accounts/eventGridFilters/read | Získat filtr Event Grid |
> | Action | Microsoft.Maps/accounts/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event Grid. |
> | Action | Microsoft. Maps/Accounts/klíče listkey/Action | Vypsat klíče účtu Maps |
> | Action | Microsoft. Maps/Accounts/Read | Získejte účet Maps. |
> | Action | Microsoft. Maps/Accounts/regenerateKey/Action | Generovat nový primární nebo sekundární klíč účtu Maps |
> | Action | Microsoft.Maps/accounts/write | Vytvořte nebo aktualizujte účet Maps. |
> | Action | Microsoft.Maps/operations/read | Přečtěte si operace poskytovatele. |
> | Action | Microsoft.Maps/register/action | Zaregistrovat poskytovatele |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Marketplace/offerTypes/vydavatelé/nabídky/plány/smlouvy/číst | Vrátí smlouvu. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Přijímá podepsané smlouvy. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje obrázek do ACR koncového uživatele. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Vrátí konfiguraci. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Uloží konfiguraci. |
> | Action | Microsoft. Marketplace/registrace/akce | Zaregistruje poskytovatele prostředků Microsoft. Marketplace v předplatném. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | Provádí operaci odstranění na klasickém prostředku služby dev Service. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Načte klíče správy prostředků klasické služby pro vývoj. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Získá adresu URL jednotného přihlašování pro klasickou službu pro vývojáře. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/read | Je operace GET na klasické vývojové službě. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Vygeneruje klíče správy prostředků klasické služby pro vývoj. |
> | Action | Microsoft. MarketplaceApps/Operations/Read | Přečtěte si operace pro všechny typy prostředků. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. MarketplaceOrdering/smlouvy/nabídky/plány/zrušit/akce | Zrušení smlouvy pro danou položku Marketplace |
> | Action | Microsoft. MarketplaceOrdering/smlouvy/nabídky/plány/číst | Vrácení smlouvy pro danou položku Marketplace |
> | Action | Microsoft. MarketplaceOrdering/smlouvy/nabídky/plány, přihlášení a akce | Podepsat smlouvu pro danou položku Marketplace |
> | Action | Microsoft. MarketplaceOrdering/Agreements/Read | Vrátí všechny smlouvy v rámci daného předplatného. |
> | Action | Microsoft. MarketplaceOrdering/offertypes/vydavatelé/nabídky/plány/smlouvy/čtení | Získat smlouvu pro danou položku virtuálního počítače Marketplace |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Podepsat nebo zrušit smlouvu pro danou položku virtuálního počítače Marketplace |
> | Action | Microsoft. MarketplaceOrdering/Operations/Read | Zobrazit všechny možné operace v rozhraní API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Media/checknameavailability/Action | Zkontroluje, jestli je k dispozici Media Services název účtu. |
> | Action | Microsoft. Media/Locations/checkNameAvailability/Action | Zkontroluje, jestli je k dispozici Media Services název účtu. |
> | Action | Microsoft.Media/mediaservices/accountfilters/delete | Odstranit libovolný filtr účtu |
> | Action | Microsoft.Media/mediaservices/accountfilters/read | Číst libovolný filtr účtu |
> | Action | Microsoft.Media/mediaservices/accountfilters/write | Vytvoří nebo aktualizuje libovolný filtr účtu. |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/delete | Odstranit jakýkoli filtr assetů |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/read | Číst libovolný filtr assetů |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/write | Vytvoří nebo aktualizuje libovolný filtr assetů. |
> | Action | Microsoft.Media/mediaservices/assets/delete | Odstranit jakýkoli Asset |
> | Action | Microsoft. Media/MediaServices/assets/getEncryptionKey/Action | Získat šifrovací klíč Assetu |
> | Action | Microsoft.Media/mediaservices/assets/listContainerSas/action | Vypsat adresy URL SAS kontejneru assetů |
> | Action | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Vypsat Lokátory streamování pro prostředek |
> | Action | Microsoft.Media/mediaservices/assets/read | Čtení libovolného prostředku |
> | Action | Microsoft.Media/mediaservices/assets/write | Vytvořit nebo aktualizovat jakýkoli Asset |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Odstranit všechny zásady klíčů obsahu |
> | Action | Microsoft. Media/MediaServices/contentKeyPolicies/getPolicyPropertiesWithSecrets/Action | Získat vlastnosti zásad s tajnými kódy |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | Načte všechny zásady klíče obsahu. |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | Vytvoří nebo aktualizuje všechny zásady klíčů obsahu. |
> | Action | Microsoft. Media/MediaServices/DELETE | Odstranit libovolný účet Media Services |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/delete | Odstranit libovolný filtr Event Grid |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/read | Číst libovolný Event Grid filtr |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/write | Vytvoří nebo aktualizuje libovolný Event Grid filtr. |
> | Action | Microsoft. Media/MediaServices/liveEventOperations/Read | Čtení jakékoli živé operace události |
> | Action | Microsoft. Media/MediaServices/liveEvents/DELETE | Odstranění jakékoli živé události |
> | Action | Microsoft. Media/MediaServices/liveEvents/liveOutputs/DELETE | Odstranit veškerý živý výstup |
> | Action | Microsoft. Media/MediaServices/liveEvents/liveOutputs/Read | Číst libovolný živý výstup |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Vytvořit nebo aktualizovat veškerý živý výstup |
> | Action | Microsoft. Media/MediaServices/liveEvents/Read | Čtení jakékoli živé události |
> | Action | Microsoft. Media/MediaServices/liveEvents/Reset/Action | Resetovat všechny operace živé události |
> | Action | Microsoft. Media/MediaServices/liveEvents/Start/Action | Spuštění jakékoli živé operace události |
> | Action | Microsoft. Media/MediaServices/liveEvents/stop/Action | Zastavit všechny operace živé události |
> | Action | Microsoft. Media/MediaServices/liveEvents/Write | Vytvořit nebo aktualizovat jakoukoliv živou událost |
> | Action | Microsoft.Media/mediaservices/liveOutputOperations/read | Čtení jakékoli živé operace výstupu |
> | Action | Microsoft.Media/mediaservices/read | Číst libovolný účet Media Services |
> | Action | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Načíst jakoukoli operaci streamování koncového bodu |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/delete | Odstranění libovolného koncového bodu streamování |
> | Action | Microsoft. Media/MediaServices/starají/Read | Číst libovolný koncový bod streamování |
> | Action | Microsoft. Media/MediaServices/starají/Scale/Action | Škálování operace koncového bodu streamování |
> | Action | Microsoft. Media/MediaServices/starají/Start/Action | Spustit všechny operace koncového bodu streamování |
> | Action | Microsoft. Media/MediaServices/starají/stop/Action | Zastavit všechny operace koncového bodu streamování |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/write | Vytvoří nebo aktualizuje libovolný koncový bod streamování. |
> | Action | Microsoft. Media/MediaServices/streamingLocators/DELETE | Odstranit všechny Lokátory streamování |
> | Action | Microsoft. Media/MediaServices/streamingLocators/listContentKeys/Action | Vypsat klíče obsahu |
> | Action | Microsoft. Media/MediaServices/streamingLocators/listPaths/Action | Seznam cest |
> | Action | Microsoft. Media/MediaServices/streamingLocators/Read | Čtení libovolného lokátoru streamování |
> | Action | Microsoft. Media/MediaServices/streamingLocators/Write | Vytvořit nebo aktualizovat libovolný Lokátor streamování |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/delete | Odstranit všechny zásady streamování |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/read | Číst všechny zásady streamování |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/write | Vytvořit nebo aktualizovat jakékoli zásady streamování |
> | Action | Microsoft. Media/MediaServices/syncStorageKeys/Action | Synchronizace klíčů úložiště pro připojený účet Azure Storage |
> | Action | Microsoft.Media/mediaservices/transforms/delete | Odstranit libovolnou transformaci |
> | Action | Microsoft. Media/MediaServices/transformes/Jobs/cancelJob/Action | Zrušit úlohu |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/delete | Odstranění libovolné úlohy |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/read | Čtení libovolné úlohy |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/write | Vytvořit nebo aktualizovat libovolnou úlohu |
> | Action | Microsoft.Media/mediaservices/transforms/read | Číst libovolnou transformaci |
> | Action | Microsoft.Media/mediaservices/transforms/write | Vytvořit nebo aktualizovat libovolnou transformaci |
> | Action | Microsoft.Media/mediaservices/write | Vytvořit nebo aktualizovat libovolný účet Media Services |
> | Action | Microsoft.Media/operations/read | Získat dostupné operace |
> | Action | Microsoft. Media/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků Media Services a povolí vytváření účtů Media Services. |
> | Action | Microsoft.Media/unregister/action | Zruší registraci předplatného pro Media Services poskytovatele prostředků. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. migruje/assessmentprojects/assessmentOptions/Read | Získá možnosti posouzení, které jsou k dispozici v daném umístění. |
> | Action | Microsoft. migrace/assessmentprojects/posouzení/čtení | Vypíše posouzení v rámci projektu. |
> | Action | Microsoft. migruje/assessmentprojects/DELETE | Odstraní projekt vyhodnocení. |
> | Action | Microsoft. migruje/assessmentprojects/Groups/Assessments/assessedmachines/Read | Získá vlastnosti vyhodnoceného počítače. |
> | Action | Microsoft. migruje/assessmentprojects/skupiny/posouzení/odstranění | Odstraní posouzení. |
> | Action | Microsoft. migruje/assessmentprojects/skupiny/posouzení/DownloadUrl/Action | Stáhne adresu URL sestavy posouzení. |
> | Action | Microsoft. migruje/assessmentprojects/skupiny/posouzení/čtení | Získá vlastnosti posouzení. |
> | Action | Microsoft. migruje/assessmentprojects/skupiny/posouzení/zápis | Vytvoří nové posouzení nebo aktualizuje existující posouzení. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/delete | Odstraní skupinu. |
> | Action | Microsoft. migruje/assessmentprojects/Groups/Read | Získá vlastnosti skupiny. |
> | Action | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Aktualizovat skupinu přidáním nebo odebráním počítačů |
> | Action | Microsoft. migruje/assessmentprojects/Groups/Write | Vytvoří novou skupinu nebo aktualizuje stávající skupinu. |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Odstraní kolektor HyperV. |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Získá vlastnosti kolekce HyperV. |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Vytvoří nový kolektor HyperV nebo aktualizuje existující kolektor HyperV. |
> | Action | Microsoft. migruje/assessmentprojects/Machines/Read | Získá vlastnosti počítače. |
> | Action | Microsoft. migruje/assessmentprojects/Read | Získá vlastnosti projektu vyhodnocení. |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Odstraní kolektor VMware. |
> | Action | Microsoft. migruje/assessmentprojects/vmwarecollectors/Read | Získá vlastnosti kolekce VMware. |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Vytvoří nový kolektor VMware nebo aktualizuje existující kolektor VMware. |
> | Action | Microsoft. migruje/assessmentprojects/Write | Vytvoří nový projekt vyhodnocení nebo aktualizuje stávající. |
> | Action | Microsoft. migruje/Locations/assessmentOptions/Read | Získá možnosti posouzení, které jsou k dispozici v daném umístění. |
> | Action | Microsoft. migruje/Locations/checknameavailability/Action | Ověří dostupnost názvu prostředku pro dané předplatné v daném umístění. |
> | Action | Microsoft. migruje/migrateprojects/DatabaseInstances/Read | Získá vlastnosti instance databáze. |
> | Action | Microsoft. migrace/migrateprojects/databáze/čtení | Získá vlastnosti databáze. |
> | Action | Microsoft. migruje/migrateprojects/DELETE | Odstraní projekt migrace. |
> | Action | Microsoft. migruje/migrateprojects/Machines/Read | Získá vlastnosti počítače. |
> | Action | Microsoft. migruje/migrateprojects/MigrateEvents/DELETE | Odstraní událost migrace. |
> | Action | Microsoft. migruje/migrateprojects/MigrateEvents/Read | Získá vlastnosti události migrace. |
> | Action | Microsoft. migruje/migrateprojects/Read | Získá vlastnosti migrace projektu. |
> | Action | Microsoft. migruje/migrateprojects/registerTool/Action | Registruje nástroj do projektu migrace. |
> | Action | Microsoft. migruje/migrateprojects/Solutions/cleanupData/Action | Vyčištění dat řešení migrace projektů |
> | Action | Microsoft. migruje/migrateprojects/Solutions/DELETE | Odstraní řešení migrace projektu. |
> | Action | Microsoft. migruje/migrateprojects/Solutions/GetConfig/Action | Získá konfiguraci řešení migrace projektu. |
> | Action | Microsoft. migruje/migrateprojects/Solutions/Read | Získá vlastnosti migrace projektového řešení. |
> | Action | Microsoft. migruje/migrateprojects/Solutions/Write | Vytvoří nové řešení migrace projektu nebo aktualizuje existující řešení migrace projektu. |
> | Action | Microsoft. migruje/migrateprojects/Write | Vytvoří nový projekt migrace nebo aktualizuje existující projekt migrace. |
> | Action | Microsoft. migrace/operace/čtení | Zobrazí seznam operací, které jsou k dispozici v Microsoft. migrace prostředků Provider |
> | Action | Microsoft. migrace/projekty/posouzení/čtení | Vypíše posouzení v rámci projektu. |
> | Action | Microsoft. migrace/projekty/odstranit | Odstraní projekt. |
> | Action | Microsoft. migrace/projekty/skupiny/posouzení/assessedmachines/číst | Získá vlastnosti vyhodnoceného počítače. |
> | Action | Microsoft. migrace/projekty/skupiny/posouzení/odstranění | Odstraní posouzení. |
> | Action | Microsoft. migruje/projekty/skupiny/posouzení/DownloadUrl/Action | Stáhne adresu URL sestavy posouzení. |
> | Action | Microsoft. migrace/projekty/skupiny/posouzení/čtení | Získá vlastnosti posouzení. |
> | Action | Microsoft. migrace/projekty/skupiny/posouzení/zápis | Vytvoří nové posouzení nebo aktualizuje existující posouzení. |
> | Action | Microsoft. migruje/projekty/skupiny/odstranit | Odstraní skupinu. |
> | Action | Microsoft. migruje/projekty/skupiny/číst | Získá vlastnosti skupiny. |
> | Action | Microsoft. migrace/projekty/skupiny/zápis | Vytvoří novou skupinu nebo aktualizuje stávající skupinu. |
> | Action | Microsoft. migrace/projekty/klíče/akce | Načte sdílené klíče projektu. |
> | Action | Microsoft. migrace/projekty/počítače/číst | Získá vlastnosti počítače. |
> | Action | Microsoft. migrace/projekty/číst | Získá vlastnosti projektu. |
> | Action | Microsoft. migrace/projekty/zapisovat | Vytvoří nový projekt nebo aktualizuje existující projekt. |
> | Action | Microsoft. migrace/registrace/akce | Zaregistruje předplatné u poskytovatele prostředků Microsoft. migruje. |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. MixedReality/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků smíšené reality. |
> | Akce dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Vytvořit prostorové kotvy |
> | Akce dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/DELETE | Odstranit prostorové kotvy |
> | Akce dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | Akce dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/– vlastnosti/číst | Získat vlastnosti prostorových ukotvení |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro Microsoft. MixedReality/SpatialAnchorsAccounts. |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro Microsoft. MixedReality/SpatialAnchorsAccounts. |
> | Action | Microsoft. MixedReality/SpatialAnchorsAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro Microsoft. MixedReality/SpatialAnchorsAccounts. |
> | Akce dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/dotaz/číst | Vyhledat prostorové kotvy |
> | Akce dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | Akce dataaction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizovat vlastnosti prostorových ukotvení |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. NetApp/Locations/operationresults/Read | Přečte prostředek výsledku operace. |
> | Action | Microsoft. NetApp/Locations/Read | Přečte prostředek kontroly dostupnosti. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Odstraní prostředek fondu. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/read | Přečte prostředek fondu. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Odstraní prostředek svazku. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Přečte cílový prostředek připojení. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Přečte prostředek svazku. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Odstraní prostředek snímku. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Přečte prostředek snímku. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Zapíše prostředek snímku. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Zapíše prostředek svazku. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | Zapíše prostředek fondu. |
> | Action | Microsoft.NetApp/netAppAccounts/delete | Odstraní prostředek účtu. |
> | Action | Microsoft.NetApp/netAppAccounts/read | Přečte prostředek účtu. |
> | Action | Microsoft.NetApp/netAppAccounts/write | Zapíše prostředek účtu. |
> | Action | Microsoft.NetApp/Operations/read | Přečte prostředky operace. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Získat Application Gateway dostupné hlavičky žádosti |
> | Action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Získat Application Gateway dostupnou hlavičku odpovědi |
> | Action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Získat Application Gateway dostupné proměnné serveru |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Předdefinovaná zásada Application Gateway SSL |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway dostupné možnosti SSL |
> | Action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Získá Application Gateway dostupné sady pravidel WAF. |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Připojí se ke fondu back-end adres služby Application Gateway. Nelze upozorňovat. |
> | Action | Microsoft. Network/applicationGateways/backendhealth/Action | Získá stav back-endu aplikační brány. |
> | Action | Microsoft. Network/applicationGateways/DELETE | Odstraní Aplikační bránu. |
> | Action | Microsoft.Network/applicationGateways/read | Načte Aplikační bránu. |
> | Action | Microsoft. Network/applicationGateways/Start/Action | Spustí Aplikační bránu. |
> | Action | Microsoft. Network/applicationGateways/stop/Action | Zastaví aplikační bránu. |
> | Action | Microsoft. Network/applicationGateways/Write | Vytvoří Aplikační bránu nebo aktualizuje Aplikační bránu. |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Odstraní zásady Application Gateway WAF. |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Načte zásady Application Gateway WAF. |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Vytvoří Application Gateway zásady WAF nebo aktualizuje zásady Application Gateway WAF. |
> | Action | Microsoft.Network/applicationSecurityGroups/delete | Odstraní skupinu zabezpečení aplikace. |
> | Action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Připojí konfiguraci protokolu IP ke skupinám zabezpečení aplikace. Nelze upozorňovat. |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Spojí pravidlo zabezpečení se skupinami zabezpečení aplikací. Nelze upozorňovat. |
> | Action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Zobrazí seznam konfigurací protokolu IP v ApplicationSecurityGroup. |
> | Action | Microsoft.Network/applicationSecurityGroups/read | Získá ID skupiny zabezpečení aplikace. |
> | Action | Microsoft.Network/applicationSecurityGroups/write | Vytvoří skupinu zabezpečení aplikace nebo aktualizuje existující skupinu zabezpečení aplikace. |
> | Action | Microsoft.Network/azureFirewallFqdnTags/read | Získá Azure Firewall značek plně kvalifikovaného názvu domény. |
> | Action | Microsoft. Network/azurefirewalls/DELETE | Odstranit Azure Firewall |
> | Action | Microsoft. Network/azurefirewalls/Read | Získat Azure Firewall |
> | Action | Microsoft. Network/azurefirewalls/Write | Vytvoří nebo aktualizuje Azure Firewall. |
> | Action | Microsoft.Network/bastionHosts/delete | Odstraní hostitele bastionu. |
> | Action | Microsoft.Network/bastionHosts/read | Načte hostitele bastionu. |
> | Action | Microsoft.Network/bastionHosts/write | Vytvoření nebo aktualizace hostitele bastionu |
> | Action | Microsoft.Network/bgpServiceCommunities/read | Získat komunity služby BGP |
> | Action | Microsoft. Network/checkFrontDoorNameAvailability/Action | Ověří, jestli je k dispozici název přední dveře. |
> | Action | Microsoft. Network/checkTrafficManagerNameAvailability/Action | Kontroluje dostupnost Traffic Manager relativního názvu DNS. |
> | Action | Microsoft. Network/Connections/DELETE | Deletes VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/Connections/Read | Gets VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/Connections/REVOKE/Action | Označuje stav připojení Express Route jako odvolaný. |
> | Action | Microsoft. Network/Connections/sharedkey/Action | Získat VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft. Network/Connections/sharedKey/Read | Získá VirtualNetworkGatewayConnection SharedKey. |
> | Action | Microsoft. Network/Connections/sharedKey/Write | Vytvoří nebo aktualizuje existující SharedKey VirtualNetworkGatewayConnection. |
> | Action | Microsoft. Network/Connections/vpndeviceconfigurationscript/Action | Načte konfiguraci zařízení VPN pro VirtualNetworkGatewayConnection. |
> | Action | Microsoft. Network/Connections/Write | Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection. |
> | Action | Microsoft. Network/ddosCustomPolicies/DELETE | Odstraní přizpůsobené zásady DDoS. |
> | Action | Microsoft. Network/ddosCustomPolicies/Read | Načte definici definice vlastní zásady DDoS. |
> | Action | Microsoft. Network/ddosCustomPolicies/Write | Vytvoří vlastní zásadu DDoS nebo aktualizuje stávající zásady DDoS. |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | Odstraní plán DDoS Protection. |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | Připojí DDoS Protection plán. Nelze upozorňovat. |
> | Action | Microsoft.Network/ddosProtectionPlans/read | Získá plán DDoS Protection. |
> | Action | Microsoft.Network/ddosProtectionPlans/write | Vytvoří plán DDoS Protection nebo aktualizuje plán DDoS Protection.  |
> | Action | Microsoft. Network/dnsoperationresults/Read | Získá výsledky operace DNS. |
> | Action | Microsoft.Network/dnsoperationstatuses/read | Získá stav operace DNS.  |
> | Action | Microsoft.Network/dnszones/A/delete | Odebere sadu záznamů daného názvu a zapíše ' A ' ze zóny DNS. |
> | Action | Microsoft.Network/dnszones/A/read | Získá sadu záznamů typu A ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft. Network/dnszones/A/Write | Vytvoří nebo aktualizuje sadu záznamů typu A v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/dnszones/AAAA/delete | Odebere sadu záznamů daného názvu a zapíše "AAAA" ze zóny DNS. |
> | Action | Microsoft.Network/dnszones/AAAA/read | Získá sadu záznamů typu AAAA ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/dnszones/AAAA/write | Vytvoří nebo aktualizuje sadu záznamů typu AAAA v zóně DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft. Network/dnszones/All/Read | Načte sady záznamů DNS napříč typy. |
> | Action | Microsoft.Network/dnszones/CAA/delete | Odeberte sadu záznamů daného názvu a zadejte ' CAA ' ze zóny DNS. |
> | Action | Microsoft.Network/dnszones/CAA/read | Získá sadu záznamů typu ' CAA ' ve formátu JSON. Sada záznamů obsahuje hodnoty TTL, Tags a ETag. |
> | Action | Microsoft.Network/dnszones/CAA/write | Vytvoří nebo aktualizuje sadu záznamů typu ' CAA ' v zóně DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/dnszones/CNAME/delete | Odebere sadu záznamů daného názvu a zadáte "CNAME" ze zóny DNS. |
> | Action | Microsoft.Network/dnszones/CNAME/read | Získá sadu záznamů typu CNAME ve formátu JSON. Sada záznamů obsahuje hodnoty TTL, Tags a ETag. |
> | Action | Microsoft.Network/dnszones/CNAME/write | Vytvoří nebo aktualizuje sadu záznamů typu CNAME v zóně DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/dnszones/delete | Odstraňte zónu DNS ve formátu JSON. Mezi vlastnosti zóny patří značky, ETag, numberOfRecordSets a maxNumberOfRecordSets. |
> | Action | Microsoft.Network/dnszones/MX/delete | Odebere sadu záznamů daného názvu a v zóně DNS typ MX. |
> | Action | Microsoft.Network/dnszones/MX/read | Získá sadu záznamů typu MX ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/dnszones/MX/write | Vytvoří nebo aktualizuje sadu záznamů typu MX v zóně DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft. Network/dnszones/NS/DELETE | Odstraní sadu záznamů DNS typu NS. |
> | Action | Microsoft. Network/dnszones/NS/Read | Načte sadu záznamů DNS typu NS. |
> | Action | Microsoft. Network/dnszones/NS/Write | Vytvoří nebo aktualizuje sadu záznamů DNS typu NS. |
> | Action | Microsoft. Network/dnszones/PTR/DELETE | Odebere sadu záznamů daného názvu a typ PTR ze zóny DNS. |
> | Action | Microsoft. Network/dnszones/PTR/Read | Získá sadu záznamů typu PTR ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft. Network/dnszones/PTR/Write | Vytvoří nebo aktualizuje sadu záznamů typu PTR v zóně DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/dnszones/read | Získejte zónu DNS ve formátu JSON. Mezi vlastnosti zóny patří značky, ETag, numberOfRecordSets a maxNumberOfRecordSets. Všimněte si, že tento příkaz nenačte sady záznamů obsažené v zóně. |
> | Action | Microsoft.Network/dnszones/recordsets/read | Načte sady záznamů DNS napříč typy. |
> | Action | Microsoft.Network/dnszones/SOA/read | Načte sadu záznamů DNS typu SOA. |
> | Action | Microsoft.Network/dnszones/SOA/write | Vytvoří nebo aktualizuje sadu záznamů DNS typu SOA. |
> | Action | Microsoft.Network/dnszones/SRV/delete | Odebere sadu záznamů daného názvu a zadáte ' SRV ' ze zóny DNS. |
> | Action | Microsoft.Network/dnszones/SRV/read | Získá sadu záznamů typu SRV ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/dnszones/SRV/write | Vytvořit nebo aktualizovat sadu záznamů typu SRV |
> | Action | Microsoft.Network/dnszones/TXT/delete | Odeberte sadu záznamů daného názvu a zadejte ' TXT ' ze zóny DNS. |
> | Action | Microsoft.Network/dnszones/TXT/read | Získá sadu záznamů typu TXT ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/dnszones/TXT/write | Vytvoří nebo aktualizuje sadu záznamů typu TXT v zóně DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/dnszones/write | Vytvoří nebo aktualizuje zónu DNS v rámci skupiny prostředků.  Používá se k aktualizaci značek v prostředku zóny DNS. Všimněte si, že tento příkaz nelze použít k vytvoření nebo aktualizaci sad záznamů v rámci zóny. |
> | Action | Microsoft. Network/expressRouteCircuits/Authorizations/DELETE | Odstraní autorizaci ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCircuits/autorizace/číst | Získá autorizaci ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCircuits/Authorizations/Write | Vytvoří nebo aktualizuje existující autorizaci ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCircuits/DELETE | Odstraní ExpressRoute |
> | Action | Microsoft. Network/expressRouteCircuits/JOIN/Action | Spojí okruh Express Route. Nelze upozorňovat. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Získá ArpTable partnerského vztahu ExpressRoute. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Odstraní připojení ExpressRoute. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Získá připojení ExpressRoute. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Vytvoří nebo aktualizuje existující prostředek připojení ExpressRoute. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/delete | Odstraní partnerský vztah ExpressRoute. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Získá připojení okruhu peer Express Route. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/read | Získá partnerský vztah ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCircuits/peering/routeTables/Action | Získá trasu partnerského vztahu ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCircuits/peering/routeTablesSummary/Action | Načte souhrn ExpressRoute partnerských vztahů. |
> | Action | Microsoft. Network/expressRouteCircuits/peering/stats/Read | Získá statu partnerského vztahu ExpressRoute. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/write | Vytvoří nebo aktualizuje existující partnerský vztah ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCircuits/Read | Získat ExpressRoute |
> | Action | Microsoft. Network/expressRouteCircuits/stats/Read | Načte ExpressRoute stat. |
> | Action | Microsoft.Network/expressRouteCircuits/write | Vytvoří nebo aktualizuje existující ExpressRoute. |
> | Action | Microsoft. Network/expressRouteCrossConnections/JOIN/Action | Připojí mezi připojení Express Route. Nelze upozorňovat. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Načte tabulku protokolu ARP vzájemného propojení Express Route. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Odstraní partnerský vztah vzájemného propojení Express Route. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | Získá partnerský vztah vzájemného propojení Express Route. |
> | Action | Microsoft. Network/expressRouteCrossConnections/peering/routeTables/Action | Načte tabulku směrování partnerských vztahů mezi připojeními Express Route. |
> | Action | Microsoft. Network/expressRouteCrossConnections/peering/routeTableSummary/Action | Získá Souhrn tabulky směrování vzájemného propojení Express Route. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/write | Vytvoří partnerský vztah mezi propojením Express Route nebo aktualizuje existující partnerský vztah mezi propojením Express Route. |
> | Action | Microsoft. Network/expressRouteCrossConnections/Read | Získat vzájemné propojení Express Route |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Odstraní připojení Express Route. |
> | Action | Microsoft. Network/expressRouteGateways/expressRouteConnections/Read | Získá připojení Express Route. |
> | Action | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Vytvoří připojení Express Route nebo aktualizuje existující připojení Express Route. |
> | Action | Microsoft. Network/expressRouteGateways/JOIN/Action | Připojí bránu Express Route. Nelze upozorňovat. |
> | Action | Microsoft. Network/expressRouteGateways/Read | Získat bránu Express Route |
> | Action | Microsoft.Network/expressRoutePorts/delete | Odstraní ExpressRoutePorts |
> | Action | Microsoft. Network/expressRoutePorts/JOIN/Action | Připojí porty Express Route. Nelze upozorňovat. |
> | Action | Microsoft. Network/expressRoutePorts/propojuje/číst | Získá ExpressRouteLink |
> | Action | Microsoft. Network/expressRoutePorts/Read | Získá ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/write | Vytvoří nebo aktualizuje ExpressRoutePorts |
> | Action | Microsoft. Network/expressRoutePortsLocations/Read | Získat umístění portů Express Route |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | Získá poskytovatele expresního Route Service. |
> | Action | Microsoft. Network/firewallPolicies/DELETE | Odstraní zásady brány firewall. |
> | Action | Microsoft. Network/firewallPolicies/JOIN/Action | Připojí se k zásadě brány firewall. Nelze upozorňovat. |
> | Action | Microsoft. Network/firewallPolicies/Read | Načte zásady brány firewall. |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/DELETE | Odstraní skupinu pravidel zásad brány firewall. |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/Read | Načte skupinu pravidel zásad brány firewall. |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/Write | Vytvoří skupinu pravidel zásad brány firewall nebo aktualizuje stávající skupinu pravidel zásad brány firewall. |
> | Action | Microsoft. Network/firewallPolicies/Write | Vytvoří zásadu brány firewall nebo aktualizuje stávající zásady brány firewall. |
> | Action | Microsoft.Network/frontDoors/backendPools/delete | Odstraní back-end fond. |
> | Action | Microsoft. Network/frontDoors/backendPools/Read | Načte back-end fond. |
> | Action | Microsoft.Network/frontDoors/backendPools/write | Vytvoří nebo aktualizuje back-end fond. |
> | Action | Microsoft.Network/frontDoors/delete | Odstraní přední dveře. |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/delete | Odstraní koncový bod front-endu. |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Zakáže HTTPS u koncového bodu front-endu. |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Povolí HTTPS u koncového bodu front-endu. |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/read | Načte koncový bod front-endu. |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/write | Vytvoří nebo aktualizuje koncový bod front-endu. |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/delete | Odstraní nastavení sondy stavu. |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/read | Načte nastavení sondy stavu. |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/write | Vytvoří nebo aktualizuje nastavení sondy stavu. |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Vytvoří nebo aktualizuje nastavení vyrovnávání zatížení. |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/read | Načte nastavení vyrovnávání zatížení. |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/write | Vytvoří nebo aktualizuje nastavení vyrovnávání zatížení. |
> | Action | Microsoft. Network/frontDoors/vyprázdnění/akce | Vyprázdnit obsah uložený v mezipaměti z přední dveře |
> | Action | Microsoft. Network/frontDoors/Read | Načte přední dveře. |
> | Action | Microsoft.Network/frontDoors/routingRules/delete | Odstraní pravidlo směrování. |
> | Action | Microsoft.Network/frontDoors/routingRules/read | Získá pravidlo směrování. |
> | Action | Microsoft.Network/frontDoors/routingRules/write | Vytvoří nebo aktualizuje pravidlo směrování. |
> | Action | Microsoft. Network/frontDoors/validateCustomDomain/Action | Ověří koncový bod front-endu pro přední dveře. |
> | Action | Microsoft.Network/frontDoors/write | Vytvoří nebo aktualizuje přední dveře. |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Načte sady pravidel spravované přes bránu firewall webových aplikací. |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Odstraní zásady firewallu webových aplikací. |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Získá zásady firewallu webových aplikací. |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Vytvoří nebo aktualizuje zásady firewallu webových aplikací. |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Připojí fond adres back-endu nástroje pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Action | Microsoft. Network/loadBalancers/backendAddressPools/Read | Získá definici fondu adres back-endu nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/DELETE | Odstraní nástroj pro vyrovnávání zatížení. |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Připojí Load Balancer konfiguraci protokolu IP front-endu. Nelze upozorňovat. |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Získá definici konfigurace protokolu IP front-endu nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Připojí příchozí fond NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Action | Microsoft. Network/loadBalancers/inboundNatPools/Read | Získá definici příchozího fondu NAT pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/inboundNatRules/DELETE | Odstraní příchozí pravidlo NAT pro vyrovnávání zatížení. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Připojí příchozí pravidlo NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Action | Microsoft. Network/loadBalancers/inboundNatRules/Read | Získá definici příchozího pravidla NAT pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/inboundNatRules/Write | Vytvoří příchozí pravidlo NAT pro vyrovnávání zatížení nebo aktualizuje stávající příchozí pravidlo NAT pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/loadBalancingRules/Read | Získá definici pravidla vyrovnávání zatížení nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | Získá odkazy na všechna síťová rozhraní v rámci nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | Získá definici odchozího pravidla nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/probs/JOIN/Action | Umožňuje používat sondy nástroje pro vyrovnávání zatížení. Například s touto vlastností oprávnění healthProbe sady VM scaleing můžete odkazovat na test. Nelze upozorňovat. |
> | Action | Microsoft. Network/loadBalancers/probs/Read | Získá test nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft.Network/loadBalancers/read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/virtualMachines/Read | Získá odkazy na všechny virtuální počítače v nástroji pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/loadBalancers/Write | Vytvoří nástroj pro vyrovnávání zatížení nebo aktualizuje existující Nástroj pro vyrovnávání zatížení. |
> | Action | Microsoft. Network/localnetworkgateways/DELETE | Odstraní LocalNetworkGateway |
> | Action | Microsoft. Network/localnetworkgateways/Read | Získá LocalNetworkGateway |
> | Action | Microsoft. Network/localnetworkgateways/Write | Vytvoří nebo aktualizuje existující LocalNetworkGateway. |
> | Action | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Získá automaticky schválené služby privátního propojení. |
> | Action | Microsoft. Network/Locations/availableDelegations/Read | Získá dostupná delegování. |
> | Action | Microsoft. Network/Locations/availablePrivateEndpointTypes/Read | Získá dostupné prostředky privátního koncového bodu. |
> | Action | Microsoft. Network/umístění/bareMetalTenants/Action | Přidělí nebo ověří tenanta holého klienta. |
> | Action | Microsoft. Network/umístění/checkAcceleratedNetworkingSupport/Action | Kontroluje podporu akcelerované sítě. |
> | Action | Microsoft. Network/Locations/checkDnsNameAvailability/Read | Zkontroluje, jestli je popisek DNS k dispozici v zadaném umístění. |
> | Action | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Kontroluje viditelnost služby privátního propojení. |
> | Action | Microsoft.Network/locations/operationResults/read | Vrátí výsledek operace asynchronní operace POST nebo DELETE. |
> | Action | Microsoft.Network/locations/operations/read | Získá prostředek operace, který reprezentuje stav asynchronní operace. |
> | Action | Microsoft. Network/Locations/serviceTags/Read | Získat značky služby |
> | Action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Získá podporované velikosti virtuálních počítačů. |
> | Action | Microsoft. Network/umístění/použití/čtení | Získá metriky využití prostředků. |
> | Action | Microsoft. Network/Locations/virtualNetworkAvailableEndpointServices/Read | Načte seznam dostupných Virtual Network služeb koncového bodu. |
> | Action | Microsoft.Network/networkIntentPolicies/delete | Odstraní zásadu záměru sítě. |
> | Action | Microsoft.Network/networkIntentPolicies/read | Získá popis zásady záměru sítě. |
> | Action | Microsoft.Network/networkIntentPolicies/write | Vytvoří zásadu záměru sítě nebo aktualizuje stávající zásady záměru sítě. |
> | Action | Microsoft.Network/networkInterfaces/delete | Odstraní síťové rozhraní. |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Získá skupiny zabezpečení sítě nakonfigurované na síťovém rozhraní virtuálního počítače. |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Získat směrovací tabulku nakonfigurovanou pro síťové rozhraní virtuálního počítače |
> | Action | Microsoft. Network/networkInterfaces/IPConfiguration/JOIN/Action | Připojí se ke konfiguraci protokolu IP síťového rozhraní. Nelze upozorňovat. |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/read | Získá definici konfigurace IP adresy síťového rozhraní.  |
> | Action | Microsoft.Network/networkInterfaces/join/action | Připojí virtuální počítač k síťovému rozhraní. Nelze upozorňovat. |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | Načte všechny nástroje pro vyrovnávání zatížení, které jsou součástí síťového rozhraní. |
> | Action | Microsoft.Network/networkInterfaces/read | Načte definici síťového rozhraní.  |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Odstraní síťové rozhraní klepněte na konfigurace. |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/read | Získá síťové rozhraní klepněte na konfigurace. |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/write | Vytvoří síťové rozhraní klepnutím na konfigurace nebo aktualizuje existující síťové rozhraní. |
> | Action | Microsoft.Network/networkInterfaces/write | Vytvoří síťové rozhraní nebo aktualizuje stávající síťové rozhraní.  |
> | Action | Microsoft.Network/networkProfiles/delete | Odstraní profil sítě. |
> | Action | Microsoft.Network/networkProfiles/read | Získá profil sítě. |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | Odebere kontejnery. |
> | Action | Microsoft. Network/networkProfiles/setContainers/Action | Nastaví kontejnery. |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Nastaví síťová rozhraní kontejneru. |
> | Action | Microsoft.Network/networkProfiles/write | Vytvoří nebo aktualizuje profil sítě. |
> | Action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Získá výchozí definici pravidla zabezpečení. |
> | Action | Microsoft.Network/networkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě. |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | Action | Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě. |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | Odstraní pravidlo zabezpečení. |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | Získá definici pravidla zabezpečení. |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | Vytvoří pravidlo zabezpečení nebo aktualizuje stávající pravidlo zabezpečení. |
> | Action | Microsoft.Network/networkSecurityGroups/write | Vytvoří skupinu zabezpečení sítě nebo aktualizuje existující skupinu zabezpečení sítě. |
> | Action | Microsoft. Network/networkWatchers/availableProvidersList/Action | Vrátí všechny dostupné poskytovatele internetových služeb pro zadanou oblast Azure. |
> | Action | Microsoft. Network/networkWatchers/azureReachabilityReport/Action | Vrátí skóre relativní latence pro poskytovatele internetových služeb ze zadaného umístění do oblastí Azure. |
> | Action | Microsoft. Network/networkWatchers/configureFlowLog/Action | Nakonfiguruje protokolování toku pro cílový prostředek. |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/DELETE | Odstraní monitorování připojení. |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Query/Action | Dotaz na monitorování připojení mezi zadanými koncovými body |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Read | Získat podrobnosti o monitorování připojení |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Start/Action | Zahájit monitorování připojení mezi zadanými koncovými body |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/stop/Action | Zastavení nebo pozastavení připojení monitorování mezi zadanými koncovými body |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Write | Vytvoří monitorování připojení. |
> | Action | Microsoft. Network/networkWatchers/connectivityCheck/Action | Ověřuje možnost navázání přímého připojení TCP z virtuálního počítače k danému koncovému bodu, včetně jiného virtuálního počítače nebo libovolného vzdáleného serveru. |
> | Action | Microsoft.Network/networkWatchers/delete | Odstraní sledovací proces sítě. |
> | Action | Microsoft. Network/networkWatchers/ipFlowVerify/Action | Vrátí, zda je paket povolen nebo zamítnut do konkrétního cíle. |
> | Action | Microsoft. Network/networkWatchers/čočky/DELETE | Odstraní rozptylovou hodnotu. |
> | Action | Microsoft. Network/networkWatchers/čočky/dotaz/akce | Dotaz na monitorování síťového provozu v zadaném koncovém bodu |
> | Action | Microsoft. Network/networkWatchers/čočky/číst | Získat podrobnosti o objektivu |
> | Action | Microsoft. Network/networkWatchers/čočky/Start/Action | Spustit monitorování síťového provozu v zadaném koncovém bodu |
> | Action | Microsoft. Network/networkWatchers/čočky/stop/Action | Zastavení nebo pozastavení monitorování síťového provozu v zadaném koncovém bodě |
> | Action | Microsoft. Network/networkWatchers/čočky/Write | Vytvoří objektiv |
> | Action | Microsoft. Network/networkWatchers/networkConfigurationDiagnostic/Action | Diagnostika konfigurace sítě. |
> | Action | Microsoft.Network/networkWatchers/nextHop/action | Pro zadanou cílovou a cílovou IP adresu vraťte typ dalšího segmentu směrování a IP adresu příštího připojení. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/DELETE | Odstraní zachytávání paketů. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Získá informace o vlastnostech a stavu prostředku zachytávání paketů. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/Read | Získání definice zachytávání paketů |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/stop/Action | Zastavte spuštěnou relaci zachycení paketů. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/Write | Vytvoří zachytávání paketů. |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/delete | Odstraní PingMesh |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/Read | Získat podrobnosti o PingMesh |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/Start/Action | Spustit PingMesh mezi zadanými virtuálními počítači |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/stop/Action | Zastavit PingMesh mezi zadanými virtuálními počítači |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/Write | Vytvoří PingMesh. |
> | Action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Připojení monitorování dotazů služby Batch mezi zadanými koncovými body |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Získá stav protokolování toku u prostředku. |
> | Action | Microsoft. Network/networkWatchers/queryTroubleshootResult/Action | Získá výsledek řešení potíží z předchozího běhu nebo aktuálně běžící operace odstraňování potíží. |
> | Action | Microsoft. Network/networkWatchers/Read | Získat definici sledovacího procesu sítě |
> | Action | Microsoft. Network/networkWatchers/securityGroupView/Action | Zobrazit nakonfigurovaná a efektivní pravidla skupiny zabezpečení sítě použitá na virtuálním počítači. |
> | Action | Microsoft. Network/networkWatchers/Topology/Action | Načte zobrazení prostředků na úrovni sítě a jejich vztahů ve skupině prostředků. |
> | Action | Microsoft. Network/networkWatchers/Troubleshoot/Action | Spustí řešení potíží se síťovými prostředky v Azure. |
> | Action | Microsoft. Network/networkWatchers/Write | Vytvoří sledovací proces sítě nebo aktualizuje existující sledovací proces sítě. |
> | Action | Microsoft. Network/Operations/Read | Získat dostupné operace |
> | Action | Microsoft.Network/p2sVpnGateways/delete | Odstraní objekt P2SVpnGateway. |
> | Action | Microsoft. Network/p2sVpnGateways/generatevpnprofile/Action | Vygenerovat profil sítě VPN pro P2SVpnGateway |
> | Action | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealth/Action | Získá stav připojení VPN P2S pro P2SVpnGateway. |
> | Action | Microsoft.Network/p2sVpnGateways/read | Načte objekt P2SVpnGateway. |
> | Action | Microsoft.Network/p2sVpnGateways/write | Vloží P2SVpnGateway. |
> | Action | Microsoft.Network/privateDnsOperationResults/read | Získá výsledky operace Privátní DNS. |
> | Action | Microsoft.Network/privateDnsOperationStatuses/read | Získá stav operace Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/A/delete | Odebere sadu záznamů daného názvu a zapíše ' A ' ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/A/read | Získá sadu záznamů typu A v rámci zóny Privátní DNS ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/privateDnsZones/A/write | Vytvoří nebo aktualizuje sadu záznamů typu A v rámci zóny Privátní DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/delete | Odebere sadu záznamů daného názvu a zapíše "AAAA" ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/read | Získá sadu záznamů typu AAAA v zóně Privátní DNS ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/write | Vytvoří nebo aktualizuje sadu záznamů typu "AAAA" v zóně Privátní DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/privateDnsZones/ALL/read | Načte sady záznamů Privátní DNS napříč typy. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/delete | Odebere sadu záznamů daného názvu a zadáte "CNAME" ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/read | Získá sadu záznamů typu CNAME v zóně Privátní DNS ve formátu JSON. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/write | Vytvoří nebo aktualizuje sadu záznamů typu CNAME v zóně Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/delete | Odstraní zónu Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/MX/delete | Odebere sadu záznamů daného názvu a typ MX ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/MX/read | Získá sadu záznamů typu MX v zóně Privátní DNS ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/privateDnsZones/MX/write | Vytvoří nebo aktualizuje sadu záznamů typu MX v zóně Privátní DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/privateDnsZones/PTR/delete | Odebere sadu záznamů se zadaným názvem a typem PTR ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/PTR/read | Získá sadu záznamů typu PTR v zóně Privátní DNS ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/privateDnsZones/PTR/write | Vytvoří nebo aktualizuje sadu záznamů typu PTR v rámci zóny Privátní DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/privateDnsZones/read | Získá vlastnosti Privátní DNS zóny ve formátu JSON. Všimněte si, že tento příkaz nenačte virtuální sítě, ke kterým je propojená zóna Privátní DNS, nebo sady záznamů obsažené v zóně. |
> | Action | Microsoft.Network/privateDnsZones/recordsets/read | Načte sady záznamů Privátní DNS napříč typy. |
> | Action | Microsoft.Network/privateDnsZones/SOA/read | Získá sadu záznamů typu "SOA" v zóně Privátní DNS ve formátu JSON. |
> | Action | Microsoft.Network/privateDnsZones/SOA/write | Aktualizuje sadu záznamů typu "SOA" v zóně Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/SRV/delete | Odebere sadu záznamů daného názvu a zadáte ' SRV ' ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/SRV/read | Získá sadu záznamů typu SRV v zóně Privátní DNS ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/privateDnsZones/SRV/write | Vytvoří nebo aktualizuje sadu záznamů typu SRV v zóně Privátní DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/privateDnsZones/TXT/delete | Odeberte sadu záznamů daného názvu a zadejte ' TXT ' ze zóny Privátní DNS. |
> | Action | Microsoft.Network/privateDnsZones/TXT/read | Získá sadu záznamů typu TXT v zóně Privátní DNS ve formátu JSON. Sada záznamů obsahuje seznam záznamů a také hodnoty TTL, značek a ETag. |
> | Action | Microsoft.Network/privateDnsZones/TXT/write | Vytvoří nebo aktualizuje sadu záznamů typu TXT v zóně Privátní DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Umožňuje odstranit odkaz na zónu Privátní DNS k virtuální síti. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Umožňuje získat odkaz Privátní DNS zóny na vlastnosti virtuální sítě ve formátu JSON. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Umožňuje vytvořit nebo aktualizovat odkaz na zónu Privátní DNS k virtuální síti. |
> | Action | Microsoft.Network/privateDnsZones/write | Vytvoří nebo aktualizuje zónu Privátní DNS v rámci skupiny prostředků. Všimněte si, že tento příkaz nelze použít k vytvoření nebo aktualizaci odkazů na virtuální sítě ani sad záznamů v rámci zóny. |
> | Action | Microsoft. Network/privateEndpoints/DELETE | Odstraní prostředek privátního koncového bodu. |
> | Action | Microsoft. Network/privateEndpoints/Read | Získá prostředek privátního koncového bodu. |
> | Action | Microsoft. Network/privateEndpoints/Write | Vytvoří nový privátní koncový bod nebo aktualizuje existující privátní koncový bod. |
> | Action | Microsoft.Network/privateLinkServices/delete | Odstraní prostředek služby privátního propojení. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Odstraní připojení privátního koncového bodu. |
> | Action | Microsoft. Network/privateLinkServices/privateEndpointConnections/Read | Získá definici propojení privátního koncového bodu. |
> | Action | Microsoft. Network/privateLinkServices/privateEndpointConnections/Write | Vytvoří nové připojení privátního koncového bodu nebo aktualizuje existující připojení privátního koncového bodu. |
> | Action | Microsoft.Network/privateLinkServices/read | Načte prostředek služby privátního propojení. |
> | Action | Microsoft.Network/privateLinkServices/write | Vytvoří novou službu privátního propojení nebo aktualizuje existující službu privátních odkazů. |
> | Action | Microsoft.Network/publicIPAddresses/delete | Odstraní veřejnou IP adresu. |
> | Action | Microsoft.Network/publicIPAddresses/join/action | Připojí se k veřejné IP adrese. Nelze upozorňovat. |
> | Action | Microsoft.Network/publicIPAddresses/read | Získá definici veřejné IP adresy. |
> | Action | Microsoft.Network/publicIPAddresses/write | Vytvoří veřejnou IP adresu nebo aktualizuje stávající veřejnou IP adresu.  |
> | Action | Microsoft.Network/publicIPPrefixes/delete | Odstraní předponu veřejné IP adresy. |
> | Action | Microsoft. Network/publicIPPrefixes/JOIN/Action | Připojí se k PublicIPPrefix. Nelze upozorňovat. |
> | Action | Microsoft.Network/publicIPPrefixes/read | Získá definici předpony veřejné IP adresy. |
> | Action | Microsoft.Network/publicIPPrefixes/write | Vytvoří předponu veřejné IP adresy nebo aktualizuje existující předponu veřejné IP adresy. |
> | Action | Microsoft. Network/registrace/akce | Zaregistruje předplatné. |
> | Action | Microsoft.Network/routeFilters/delete | Odstraní definici filtru tras. |
> | Action | Microsoft. Network/routeFilters/JOIN/Action | Spojí filtr tras. Nelze upozorňovat. |
> | Action | Microsoft. Network/routeFilters/Read | Získá definici filtru tras. |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | Odstraní definici pravidla filtru tras. |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | Načte definici pravidla filtru tras. |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | Vytvoří pravidlo filtru tras nebo aktualizuje existující pravidlo filtru tras. |
> | Action | Microsoft.Network/routeFilters/write | Vytvoří filtr tras nebo aktualizuje existující filtr tras. |
> | Action | Microsoft. Network/routeTables/DELETE | Odstraní definici směrovací tabulky. |
> | Action | Microsoft. Network/routeTables/JOIN/Action | Spojí tabulku směrování. Nelze upozorňovat. |
> | Action | Microsoft. Network/routeTables/Read | Získá definici směrovací tabulky. |
> | Action | Microsoft. Network/routeTables/Routes/DELETE | Odstraní definici trasy. |
> | Action | Microsoft. Network/routeTables/Routes/Read | Získá definici trasy. |
> | Action | Microsoft. Network/routeTables/Routes/Write | Vytvoří trasu nebo aktualizuje existující trasu. |
> | Action | Microsoft. Network/routeTables/Write | Vytvoří směrovací tabulku nebo aktualizuje existující směrovací tabulku. |
> | Action | Microsoft. Network/securegateways/DELETE | Odstranit zabezpečenou bránu |
> | Action | Microsoft. Network/securegateways/Read | Získat zabezpečenou bránu |
> | Action | Microsoft. Network/securegateways/Write | Vytvoří nebo aktualizuje zabezpečenou bránu. |
> | Action | Microsoft. Network/serviceEndpointPolicies/DELETE | Odstraní zásadu koncového bodu služby. |
> | Action | Microsoft. Network/serviceEndpointPolicies/JOIN/Action | Připojí se k zásadě koncového bodu služby. Nelze upozorňovat. |
> | Action | Microsoft. Network/serviceEndpointPolicies/joinSubnet/Action | Připojí podsíť ke zásadám koncového bodu služby. Nelze upozorňovat. |
> | Action | Microsoft. Network/serviceEndpointPolicies/Read | Načte popis zásady koncového bodu služby. |
> | Action | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/DELETE | Odstraní definici zásady koncového bodu služby. |
> | Action | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Read | Načte popis definice zásad koncového bodu služby. |
> | Action | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Vytvoří definici zásady koncového bodu služby nebo aktualizuje existující definici zásad koncového bodu služby. |
> | Action | Microsoft. Network/serviceEndpointPolicies/Write | Vytvoří zásadu koncového bodu služby nebo aktualizuje existující zásady koncového bodu služby. |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | Získá Traffic Manager geografickou hierarchii obsahující oblasti, které se dají použít spolu s metodou geografického směrování provozu. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Odstraní Koncový bod Azure z existujícího profilu Traffic Manager. Traffic Manager zastaví směrování provozu do odstraněných Koncový bod Azure. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Získá Koncový bod Azure, který patří do profilu Traffic Manager, včetně všech vlastností tohoto Koncový bod Azure. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Přidejte nový Koncový bod Azure v existujícím profilu Traffic Manager nebo aktualizujte vlastnosti existující Koncový bod Azure v profilu Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | Odstraňte profil Traffic Manager. Všechna nastavení přidružená k profilu Traffic Manager budou ztracena a profil již nebude možné používat ke směrování provozu. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Odstraní externí koncový bod z existujícího profilu Traffic Manager. Traffic Manager zastaví směrování provozu do odstraněného externího koncového bodu. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Načte externí koncový bod, který patří do profilu Traffic Manager, včetně všech vlastností tohoto externího koncového bodu. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Přidejte nový externí koncový bod do existujícího profilu Traffic Manager nebo aktualizujte vlastnosti existujícího externího koncového bodu v profilu Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Získá Traffic Manager Heat mapu pro daný profil Traffic Manager, který obsahuje počty dotazů a data latence podle umístění a zdrojové IP adresy. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Odstraní vnořený koncový bod z existujícího profilu Traffic Manager. Traffic Manager zastaví směrování provozu do odstraněného vnořeného koncového bodu. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Načte vnořený koncový bod, který patří do profilu Traffic Manager, včetně všech vlastností vnořeného koncového bodu. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Přidá nový vnořený koncový bod do existujícího profilu Traffic Manager nebo aktualizuje vlastnosti existujícího vnořeného koncového bodu v profilu Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/read | Získání konfigurace profilu Traffic Manager. To zahrnuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncových bodů a seznam koncových bodů směrovaných tímto profilem Traffic Manager. |
> | Action | Microsoft.Network/trafficManagerProfiles/write | Vytvořte profil Traffic Manager nebo upravte konfiguraci existujícího profilu Traffic Manager.<br>To zahrnuje povolení nebo zakázání profilu a úpravy nastavení DNS, nastavení směrování provozu nebo nastavení monitorování koncového bodu.<br>Koncové body směrované profilem Traffic Manager lze přidat, odebrat, povolit nebo zakázat. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Odstraní klíč na úrovni předplatného, který se používá pro kolekci metrik uživatele v reálném čase. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | Získá klíč na úrovni předplatného, který se používá pro kolekci metrik uživatele v reálném čase. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | Vytvoří nový klíč na úrovni předplatného, který se použije pro kolekci metrik uživatelů v reálném čase. |
> | Action | Microsoft.Network/unregister/action | Zruší registraci předplatného. |
> | Action | Microsoft. Network/virtualHubs/DELETE | Odstraní virtuální rozbočovač. |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Odstraní HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Získat HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Vytvořit nebo aktualizovat HubVirtualNetworkConnection |
> | Action | Microsoft. Network/virtualHubs/Read | Získat virtuální rozbočovač |
> | Action | Microsoft. Network/virtualHubs/Write | Vytvoří nebo aktualizuje virtuální rozbočovač. |
> | Action | Microsoft. Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/virtualNetworkGateways/DELETE | Odstraní virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generovat balíček VpnClient pro virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/generatevpnprofile/Action | Generovat balíček VpnProfile pro VirtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/Action | Získá inzerované trasy virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/Action | Získá stav partnerského uzlu protokolu BGP virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/Action | Získá virtualnetworkgateway zjištěné trasy. |
> | Action | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/Action | Získat stav připojení klienta k síti VPN pro VirtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Získejte parametry protokolu IPSec vpnclient pro klienta VirtualNetworkGateway P2S. |
> | Action | Microsoft. Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Získá adresu URL předem generovaného balíčku profilu klienta VPN. |
> | Action | Microsoft.Network/virtualNetworkGateways/read | Načte VirtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/Reset/Action | Resetuje virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | Resetovat sdílený klíč vpnclient pro klienta VirtualNetworkGateway P2S |
> | Action | Microsoft. Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Nastavte parametry protokolu IPSec vpnclient pro klienta VirtualNetworkGateway P2S. |
> | Action | Microsoft. Network/virtualnetworkgateways/supportedvpndevices/Action | Seznam podporovaných zařízení VPN |
> | Action | Microsoft. Network/virtualNetworkGateways/Write | Vytvoří nebo aktualizuje VirtualNetworkGateway. |
> | Action | Microsoft. Network/virtualNetworks/BastionHosts/Action | Získá odkazy na hostitele bastionu v Virtual Network. |
> | Action | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Získá odkazy na hostitele bastionu v Virtual Network. |
> | Action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ověřte, jestli je v zadané virtuální síti dostupná IP adresa. |
> | Action | Microsoft.Network/virtualNetworks/delete | Odstraní virtuální síť. |
> | Action | Microsoft. Network/virtualNetworks/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Action | Microsoft. Network/virtualNetworks/peer/Action | Navázání partnerského vztahu virtuální sítě s jinou virtuální sítí |
> | Action | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Action | Microsoft.Network/virtualNetworks/subnets/delete | Odstraní podsíť virtuální sítě. |
> | Action | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | Action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Připraví podsíť použitím nezbytných síťových zásad. |
> | Action | Microsoft.Network/virtualNetworks/subnets/read | Získá definici podsítě virtuální sítě. |
> | Action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Získá odkazy na všechny virtuální počítače v podsíti virtuální sítě. |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | Vytvoří podsíť virtuální sítě nebo aktualizuje existující podsíť virtuální sítě. |
> | Action | Microsoft. Network/virtualNetworks/využití/čtení | Získat využití IP adres pro každou podsíť virtuální sítě |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | Získá odkazy na všechny virtuální počítače ve virtuální síti. |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Odstraní partnerský vztah virtuální sítě. |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Získá definici partnerského vztahu virtuální sítě. |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Vytvoří partnerský vztah virtuální sítě nebo aktualizuje stávající partnerský vztah virtuální sítě. |
> | Action | Microsoft.Network/virtualNetworks/write | Vytvoří virtuální síť nebo aktualizuje stávající virtuální síť. |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | Virtual Network klepněte na tlačítko Odstranit |
> | Action | Microsoft.Network/virtualNetworkTaps/join/action | Připojí se k virtuální síti klepnutím. Nelze upozorňovat. |
> | Action | Microsoft.Network/virtualNetworkTaps/read | Získat Virtual Network klepněte |
> | Action | Microsoft.Network/virtualNetworkTaps/write | Vytvořit nebo aktualizovat Virtual Network klepněte |
> | Action | Microsoft. Network/virtualWans/DELETE | Odstraní virtuální síť WAN. |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Odstraní virtuální síť WAN P2SVpnServerConfiguration. |
> | Action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Načte virtuální síť WAN P2SVpnServerConfiguration. |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Vytvoří virtuální síť WAN P2SVpnServerConfiguration nebo aktualizuje stávající virtuální síť WAN P2SVpnServerConfiguration. |
> | Action | Microsoft. Network/virtualWans/Read | Získat virtuální síť WAN |
> | Action | Microsoft. Network/virtualwans/supportedSecurityProviders/Read | Získá podporované poskytovatele zabezpečení VirtualWan. |
> | Action | Microsoft. Network/virtualWans/virtualHubs/Read | Načte všechna virtuální centra, která odkazují na virtuální síť WAN. |
> | Action | Microsoft. Network/virtualwans/vpnconfiguration/Action | Načte konfiguraci sítě VPN. |
> | Action | Microsoft. Network/virtualWans/vpnSites/Read | Načte všechny weby sítě VPN, které odkazují na virtuální síť WAN. |
> | Action | Microsoft. Network/virtualWans/Write | Vytvoření nebo aktualizace virtuální sítě WAN |
> | Action | Microsoft.Network/vpnGateways/delete | Odstraní objekt VpnGateway. |
> | Action | Microsoft. Network/vpngateways/listvpnconnectionshealth/Action | Získá stav připojení pro všechny nebo jen podmnožinu připojení VpnGateway. |
> | Action | Microsoft. Network/vpnGateways/Read | Načte objekt VpnGateway. |
> | Action | Microsoft. Network/vpngateways/Reset/Action | Resetuje VpnGateway |
> | Action | microsoft.network/vpnGateways/vpnConnections/delete | Odstraní objekt VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | Načte objekt VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | Vloží VpnConnection. |
> | Action | Microsoft. Network/vpnGateways/Write | Vloží VpnGateway. |
> | Action | Microsoft.Network/vpnsites/delete | Odstraní prostředek sítě VPN. |
> | Action | Microsoft. Network/vpnsites/Read | Získá prostředek sítě VPN. |
> | Action | Microsoft.Network/vpnsites/write | Vytvoří nebo aktualizuje prostředek sítě VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. NotificationHubs/CheckNamespaceAvailability/Action | Kontroluje, zda je daný název prostředku oboru názvů v rámci služby NotificationHub k dispozici. |
> | Action | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/Action | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Action | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Action | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Action | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Action | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/regenerateKeys/Action | Autorizační pravidlo oboru názvů znovu vygeneruje primární/SecondaryKey. Zadejte klíč, který se musí znovu vygenerovat. |
> | Action | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Action | Microsoft. NotificationHubs/obory názvů/CheckNotificationHubAvailability/Action | Kontroluje, zda je daný název NotificationHub k dispozici v rámci oboru názvů. |
> | Action | Microsoft. NotificationHubs/obory názvů/DELETE | Odstranit prostředek oboru názvů |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/Action | Získá seznam autorizačních pravidel centra oznámení. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/DELETE | Odstranit autorizační pravidla centra oznámení |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce do centra oznámení |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/Read | Získá seznam autorizačních pravidel centra oznámení. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/regenerateKeys/Action | Autorizační pravidlo centra oznámení znovu vygeneruje primární/SecondaryKey. Zadejte klíč, který se má znovu vygenerovat. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/Write | Vytvořte autorizační pravidla centra oznámení a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/debugSend/Action | Odeslat testovací nabízené oznámení. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/DELETE | Odstranit prostředek centra oznámení |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/metricDefinitions/Read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/pnsCredentials/Action | Získejte všechna pověření PNS centra oznámení. To zahrnuje přihlašovací údaje, WNS, MPNS, APNS, GCM a Baidu. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/Read | Získá seznam popisů prostředků centra oznámení. |
> | Action | Microsoft. NotificationHubs/obory názvů/NotificationHubs/Write | Vytvořte centrum oznámení a aktualizujte jeho vlastnosti. Mezi vlastnosti patří hlavně přihlašovací údaje PNS. Autorizační pravidla a hodnota TTL |
> | Action | Microsoft. NotificationHubs/obory názvů/číst | Získat seznam popisu prostředku oboru názvů |
> | Action | Microsoft. NotificationHubs/obory názvů/zápis | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Action | Microsoft. NotificationHubs/operationResults/Read | Vrátí výsledky operace pro poskytovatele Notification Hubs. |
> | Action | Microsoft.NotificationHubs/operations/read | Vrátí seznam podporovaných operací pro poskytovatele Notification Hubs. |
> | Action | Microsoft. NotificationHubs/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků NotificationHubs a povolí vytváření oborů názvů a NotificationHubs. |
> | Action | Microsoft.NotificationHubs/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků NotificationHubs a povolí vytváření oborů názvů a NotificationHubs. |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. OffAzure/HyperVSites/Clusters/Read | Získá vlastnosti clusteru Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/write | Vytvoří nebo aktualizuje cluster Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/delete | Odstraní lokalitu Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Získá vlastnosti hostitele Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | Vytvoří nebo aktualizuje hostitele Hyper-V. |
> | Action | Microsoft. OffAzure/HyperVSites/Jobs/Read | Získá vlastnosti úloh technologie Hyper-V. |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/Read | Získá vlastnosti počítačů Hyper-V. |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/Start/Action | Spustit počítače s technologií Hyper-V |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/stop/Action | Zastaví počítače s technologií Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Získá vlastnosti stavu operace technologie Hyper-V. |
> | Action | Microsoft. OffAzure/HyperVSites/Read | Získá vlastnosti lokality Hyper-V. |
> | Action | Microsoft. OffAzure/HyperVSites/Refresh/Action | Aktualizuje objekty v rámci lokality Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Získá vlastnosti účtů spustit jako technologie Hyper-V. |
> | Action | Microsoft. OffAzure/HyperVSites/Usage/Read | Získá použití webu Hyper-V. |
> | Action | Microsoft.OffAzure/HyperVSites/write | Vytvoří nebo aktualizuje lokalitu Hyper-V. |
> | Action | Microsoft.OffAzure/Operations/read | Přečte vystavené operace. |
> | Action | Microsoft. OffAzure/Register/Action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. OffAzure. |
> | Action | Microsoft.OffAzure/VMwareSites/delete | Odstraní lokalitu VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/Jobs/Read | Získá vlastnosti úloh VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/Machines/Read | Získá vlastnosti počítačů VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/Machines/Start/Action | Spuštění počítačů VMware |
> | Action | Microsoft. OffAzure/VMwareSites/Machines/stop/Action | Zastaví počítače VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/operationsstatus/Read | Získá vlastnosti stavu operace VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/Read | Získá vlastnosti webu VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/Refresh/Action | Aktualizuje objekty v rámci lokality VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/runasaccounts/Read | Získá vlastnosti účtů spustit jako pro VMware. |
> | Action | Microsoft. OffAzure/VMwareSites/Usage/Read | Získá použití webu VMware. |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | Získá vlastnosti VMware vCenter. |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | Vytvoří nebo aktualizuje VMware vCenter. |
> | Action | Microsoft. OffAzure/VMwareSites/Write | Vytvoří nebo aktualizuje lokalitu VMware. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.OperationalInsights/linkTargets/read | Zobrazí seznam existujících účtů, které nejsou přidružené k předplatnému Azure. Pokud chcete toto předplatné Azure propojit s pracovním prostorem, použijte ID zákazníka vrácené touto operací ve vlastnosti ID zákazníka operace vytvořit pracovní prostor. |
> | Action | microsoft.operationalinsights/operations/read | Zobrazí seznam všech dostupných operací OperationalInsights REST API. |
> | Action | microsoft.operationalinsights/register/action | Rergisters předplatné. |
> | Action | Microsoft.OperationalInsights/register/action | Registrace předplatného pro poskytovatele prostředků. |
> | Action | microsoft.operationalinsights/unregister/action | Zruší registraci předplatného. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | Hledání pomocí nového stroje |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Získejte schéma vyhledávání v2. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/action | Hledání pomocí nového stroje |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Získejte schéma vyhledávání v2. |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Odstranit obor konfigurace |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Získat rozsah konfigurace |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Nastavit rozsah konfigurace |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/delete | Odstranění zdrojů dat v pracovním prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/read | Získat zdroje dat v pracovním prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/write | Vytvořit nebo aktualizovat zdroje dat v pracovním prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/delete | Odstraní pracovní prostor. Pokud pracovní prostor byl během vytváření propojený s existujícím pracovním prostorem, pracovní prostor, ke kterému byl odkaz přiřazený, se neodstraní. |
> | Action | Microsoft.OperationalInsights/workspaces/gateways/delete | Odebere bránu nakonfigurovanou pro pracovní prostor. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/generateregistrationcertificate/Action | Vygeneruje registrační certifikát pro pracovní prostor. Tento certifikát se používá k připojení nástroje Microsoft System Center Operation Manager k pracovnímu prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Zakáže sadu Intelligence Pack pro daný pracovní prostor. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/intelligencepacks/Enable/Action | Povoluje sadu Intelligence Pack pro daný pracovní prostor. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Zobrazí seznam všech inteligentních sad, které jsou pro daný pracovní prostor viditelné, a také uvádí, jestli je balíček pro tento pracovní prostor povolený nebo zakázaný. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Odstraňte propojené služby v daném pracovním prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/read | Získejte propojené služby v daném pracovním prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/write | Vytvoří nebo aktualizuje propojené služby v daném pracovním prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/action | Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/read | Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/managementGroups/read | Získá názvy a metadata pro System Center Operations Manager skupiny pro správu připojené k tomuto pracovnímu prostoru. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/metricDefinitions/číst | Získat definice metrik v pracovním prostoru |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Odstraní nastavení oznámení uživatele pro pracovní prostor. |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Získá nastavení oznámení uživatele pro pracovní prostor. |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Nastavte nastavení oznámení uživatele pro pracovní prostor. |
> | Action | microsoft.operationalinsights/workspaces/operations/read | Získá stav operace pracovního prostoru OperationalInsights. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/vyprázdnit/akce | Odstranit zadaná data z pracovního prostoru |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Čtení dat z tabulky AADDomainServicesAccountLogon |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Čtení dat z tabulky AADDomainServicesAccountManagement |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Čtení dat z tabulky AADDomainServicesDirectoryServiceAccess |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Čtení dat z tabulky AADDomainServicesLogonLogoff |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Čtení dat z tabulky AADDomainServicesPolicyChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Čtení dat z tabulky AADDomainServicesPrivilegeUse |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Čtení dat z tabulky AADDomainServicesSystemSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Čtení dat z tabulky ADAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Čtení dat z tabulky ADFActivityRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Čtení dat z tabulky ADFPipelineRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Čtení dat z tabulky ADFTriggerRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Čtení dat z tabulky ADReplicationResult |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Čtení dat z tabulky ADSecurityAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/Alert/read | Čtení dat z tabulky výstrah |
> | Action | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Čtení dat z tabulky AlertHistory |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Čtení dat z tabulky AppCenterError |
> | Action | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Čtení dat z tabulky ApplicationInsights |
> | Action | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Čtení dat z tabulky AuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Čtení dat z tabulky AutoscaleEvaluationsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Čtení dat z tabulky AutoscaleScaleActionsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Čtení dat z tabulky AWSCloudTrail |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Čtení dat z tabulky AzureActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Čtení dat z tabulky AzureAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Čtení dat z tabulky AzureMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Čtení dat z tabulky BlockchainApplicationLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Čtení dat z tabulky BlockchainProxyLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Čtení dat z tabulky BoundPort |
> | Action | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Čtení dat z tabulky CommonSecurityLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Čtení dat z tabulky počítačů |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Čtení dat z tabulky ConfigurationChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Čtení dat z tabulky ConfigurationData |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Čtení dat z tabulky ContainerImageInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Čtení dat z tabulky ContainerInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Čtení dat z tabulky ContainerLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Čtení dat z tabulky ContainerNodeInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Čtení dat z tabulky ContainerServiceLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Čtení dat z tabulky DatabricksAccounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Čtení dat z tabulky DatabricksClusters |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Čtení dat z tabulky DatabricksDBFS |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Čtení dat z tabulky DatabricksJobs |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Čtení dat z tabulky DatabricksNotebook |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Čtení dat z tabulky DatabricksSecrets |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Čtení dat z tabulky DatabricksSQLPermissions |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Čtení dat z tabulky DatabricksSSH |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Čtení dat z tabulky DatabricksTables |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Čtení dat z tabulky DatabricksWorkspace |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Čtení dat z tabulky DeviceAppCrash |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Čtení dat z tabulky DeviceAppLaunch |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Čtení dat z tabulky DeviceCalendar |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Čtení dat z tabulky DeviceCleanup |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Čtení dat z tabulky DeviceConnectSession |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Čtení dat z tabulky DeviceEtw |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Čtení dat z tabulky DeviceHardwareHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Čtení dat z tabulky přidružený |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Čtení dat z tabulky DeviceHeartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Čtení dat z tabulky DeviceSkypeHeartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Čtení dat z tabulky DeviceSkypeSignIn |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Čtení dat z tabulky DeviceSleepState |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Čtení dat z tabulky DHAppFailure |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Čtení dat z tabulky DHAppReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Čtení dat z tabulky DHDriverReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Čtení dat z tabulky DHLogonFailures |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Čtení dat z tabulky DHLogonMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Čtení dat z tabulky DHOSCrashData |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/DHOSReliability/číst | Čtení dat z tabulky DHOSReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Čtení dat z tabulky DHWipAppLearning |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Čtení dat z tabulky DnsEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Čtení dat z tabulky DnsInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Čtení dat z tabulky ETWEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/Event/read | Čtení dat z tabulky událostí |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/ExchangeAssessmentRecommendation/číst | Čtení dat z tabulky ExchangeAssessmentRecommendation |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/ExchangeOnlineAssessmentRecommendation/číst | Čtení dat z tabulky ExchangeOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Čtení dat z tabulky prezenčního signálu |
> | Action | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Čtení dat z tabulky HuntingBookmark |
> | Action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Čtení dat z tabulky IISAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Čtení dat z tabulky InboundConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Čtení dat z tabulky InsightsMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Čtení dat z tabulky IntuneAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Čtení dat z tabulky IntuneOperationalLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Čtení dat z tabulky KubeEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Čtení dat z tabulky KubeNodeInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Čtení dat z tabulky KubePodInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Čtení dat z tabulky KubeServices |
> | Action | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Čtení dat z tabulky LinuxAuditLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Čtení dat z tabulky MAApplication |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Čtení dat z tabulky MAApplicationHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Čtení dat z tabulky MAApplicationHealthAlternativeVersions |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Čtení dat z tabulky MAApplicationHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Čtení dat z tabulky MAApplicationInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Čtení dat z tabulky MAApplicationInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Čtení dat z tabulky MAApplicationReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Čtení dat z tabulky MADeploymentPlan |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Čtení dat z tabulky MADevice |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Čtení dat z tabulky MADeviceNotEnrolled |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Čtení dat z tabulky MADeviceNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Čtení dat z tabulky MADevicePnPHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Čtení dat z tabulky MADevicePnPHealthAlternativeVersions |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Čtení dat z tabulky MADevicePnPHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Čtení dat z tabulky MADeviceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Čtení dat z tabulky MADriverInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Čtení dat z tabulky MADriverReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Čtení dat z tabulky MAOfficeAddin |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Čtení dat z tabulky MAOfficeAddinEntityHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Čtení dat z tabulky MAOfficeAddinHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Čtení dat z tabulky MAOfficeAddinHealthEventNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Čtení dat z tabulky MAOfficeAddinHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Čtení dat z tabulky MAOfficeAddinInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Čtení dat z tabulky MAOfficeAddinInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Čtení dat z tabulky MAOfficeAddinReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Čtení dat z tabulky MAOfficeApp |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Čtení dat z tabulky MAOfficeAppCrashesNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Čtení dat z tabulky MAOfficeAppHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Čtení dat z tabulky MAOfficeAppInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Čtení dat z tabulky MAOfficeAppInstanceHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Čtení dat z tabulky MAOfficeAppReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Čtení dat z tabulky MAOfficeAppSessionsNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Čtení dat z tabulky MAOfficeBuildInfo |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Čtení dat z tabulky MAOfficeCurrencyAssessment |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Čtení dat z tabulky MAOfficeCurrencyAssessmentDailyCounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Čtení dat z tabulky MAOfficeDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Čtení dat z tabulky MAOfficeDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Čtení dat z tabulky MAOfficeMacroErrorNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Čtení dat z tabulky MAOfficeMacroGlobalHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Čtení dat z tabulky MAOfficeMacroHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Čtení dat z tabulky MAOfficeMacroHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Čtení dat z tabulky MAOfficeMacroIssueInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Čtení dat z tabulky MAOfficeMacroIssueReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Čtení dat z tabulky MAOfficeMacroSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Čtení dat z tabulky MAOfficeSuite |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Čtení dat z tabulky MAOfficeSuiteInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Čtení dat z tabulky MAProposedPilotDevices |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Čtení dat z tabulky MAWindowsBuildInfo |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Čtení dat z tabulky MAWindowsCurrencyAssessment |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Čtení dat z tabulky MAWindowsCurrencyAssessmentDailyCounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Čtení dat z tabulky MAWindowsDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Čtení dat z tabulky MAWindowsDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Čtení dat z tabulky MAWindowsSysReqInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebApplicationLog/read | Čtení dat z tabulky MicrosoftWebApplicationLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebFunctionExecutionLogs/read | Čtení dat z tabulky MicrosoftWebFunctionExecutionLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Čtení dat z tabulky MicrosoftWebStdOutStdErrLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Čtení dat z tabulky MicrosoftWebW3CLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Čtení dat z tabulky NetworkMonitoring |
> | Action | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Čtení dat z tabulky OfficeActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/Operation/read | Čtení dat z tabulky operací |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/OutboundConnection/číst | Čtení dat z tabulky OutboundConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/Perf/read | Čtení dat z tabulky perf |
> | Action | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Čtení dat z tabulky ProtectionStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/read | Spouštění dotazů nad daty v pracovním prostoru |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Čtení dat z tabulky ReservedAzureCommonFields |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Čtení dat z tabulky ReservedCommonFields |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Čtení dat z tabulky SCCMAssessmentRecommendation |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/SCOMAssessmentRecommendation/číst | Čtení dat z tabulky SCOMAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Čtení dat z tabulky SecurityAlert |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Čtení dat z tabulky SecurityBaseline |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Čtení dat z tabulky SecurityBaselineSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Čtení dat z tabulky SecurityDetection |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/SecurityEvent/číst | Čtení dat z tabulky SecurityEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Čtení dat z tabulky SecurityIoTRawEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Čtení dat z tabulky SecurityRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Čtení dat z tabulky ServiceFabricOperationalEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Čtení dat z tabulky ServiceFabricReliableActorEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Čtení dat z tabulky ServiceFabricReliableServiceEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Čtení dat z tabulky SfBAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Čtení dat z tabulky SfBOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Čtení dat z tabulky SharePointOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Čtení dat z tabulky SigninLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Čtení dat z tabulky SPAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Čtení dat z tabulky SQLAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Čtení dat z tabulky SQLQueryPerformance |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Čtení dat z tabulky SqlThreatProtectionLoginAudits |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Čtení dat z tabulky SqlVulnerabilityAssessmentResult |
> | Action | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Čtení dat z tabulky syslog |
> | Action | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Čtení dat z tabulky SysmonEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Čtení dat z libovolného vlastního protokolu |
> | Action | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Čtení dat z tabulky ThreatIntelligenceIndicator |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Čtení dat z tabulky UAApp |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Čtení dat z tabulky UAComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Čtení dat z tabulky UAComputerRank |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Čtení dat z tabulky UADriver |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Čtení dat z tabulky UADriverProblemCodes |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Čtení dat z tabulky UAFeedback |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Čtení dat z tabulky UAHardwareSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Čtení dat z tabulky UAIESiteDiscovery |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Čtení dat z tabulky UAOfficeAddIn |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Čtení dat z tabulky UAProposedActionPlan |
> | Action | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Čtení dat z tabulky UASysReqIssue |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Čtení dat z tabulky UAUpgradedComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/Update/read | Čtení dat z tabulky aktualizací |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Čtení dat z tabulky UpdateRunProgress |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Čtení dat z tabulky UpdateSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/Usage/read | Čtení dat z tabulky využití |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/VMBoundPort/číst | Čtení dat z tabulky VMBoundPort |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/VMConnection/číst | Čtení dat z tabulky VMConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Čtení dat z tabulky W3CIISLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Čtení dat z tabulky WaaSDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Čtení dat z tabulky WaaSInsiderStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Čtení dat z tabulky WaaSUpdateStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Čtení dat z tabulky WDAVStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Čtení dat z tabulky WDAVThreat |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Čtení dat z tabulky WindowsClientAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Čtení dat z tabulky WindowsEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Čtení dat z tabulky WindowsFirewall |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Čtení dat z tabulky WindowsServerAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/WireData/read | Čtení dat z tabulky WireData |
> | Action | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Čtení dat z tabulky WorkloadMonitoringPerf |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Čtení dat z tabulky WUDOAggregatedStatus |
> | Action | Microsoft. OperationalInsights/pracovní prostory/dotazy/WUDOStatus/číst | Čtení dat z tabulky WUDOStatus |
> | Action | Microsoft.OperationalInsights/workspaces/read | Získá existující pracovní prostor. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/regeneratesharedkey/Action | Znovu vygeneruje zadaný sdílený klíč pracovního prostoru. |
> | Action | microsoft.operationalinsights/workspaces/rules/read | Získat všechna pravidla výstrahy. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Odstraní uložený vyhledávací dotaz. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/read | Načte uložený vyhledávací dotaz. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/results/read | Získání výsledků uložených hledání Zastaralé |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Odstraňte plánované akce hledání. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Získejte plánované akce hledání. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Vytvoří nebo aktualizuje plánované akce vyhledávání. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Odstranění naplánovaných hledání. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Získat naplánovaná hledání. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Vytvoří nebo aktualizuje naplánovaná hledání. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/write | Vytvoří uložený vyhledávací dotaz. |
> | Action | Microsoft.OperationalInsights/workspaces/schema/read | Získá schéma vyhledávání pro pracovní prostor.  Schéma hledání zahrnuje vystavená pole a jejich typy. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Action | microsoft.operationalinsights/workspaces/search/read | Získejte výsledky hledání. Zastaralé. |
> | Action | Microsoft. OperationalInsights/pracovní prostory/sharedKeys/Action | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Odstraní konfiguraci úložiště. Tím se zastaví, aby Microsoft Operational Insights číst data z účtu úložiště. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Získá konfiguraci úložiště. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Vytvoří novou konfiguraci úložiště. Tyto konfigurace se používají k vyžádání dat z umístění v existujícím účtu úložiště. |
> | Action | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Získat protokol selhání překladu upgradu pro pracovní prostor |
> | Action | Microsoft.OperationalInsights/workspaces/usages/read | Načte data o využití pro pracovní prostor, včetně množství dat, které pracovní prostor přečetl. |
> | Action | Microsoft.OperationalInsights/workspaces/write | Vytvoří nový pracovní prostor nebo vytvoří odkaz na existující pracovní prostor tím, že mu poskytne ID zákazníka z existujícího pracovního prostoru. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. OperationsManagement/managementAssociations/DELETE | Odstranit existující přidružení správy |
> | Action | Microsoft. OperationsManagement/managementAssociations/Read | Získat existující přidružení správy |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | Vytvořit nové přidružení pro správu |
> | Action | Microsoft. OperationsManagement/managementConfigurations/DELETE | Odstranit existující konfiguraci správy |
> | Action | Microsoft. OperationsManagement/managementConfigurations/Read | Získat existující konfiguraci správy |
> | Action | Microsoft. OperationsManagement/managementConfigurations/Write | Vytvořit novou konfiguraci správy |
> | Action | Microsoft. OperationsManagement/Register/Action | Registrace předplatného pro poskytovatele prostředků. |
> | Action | Microsoft. OperationsManagement/Solutions/DELETE | Odstranit existující řešení OMS |
> | Action | Microsoft. OperationsManagement/Solutions/Read | Získat ukončení řešení OMS |
> | Action | Microsoft. OperationsManagement/Solutions/Write | Vytvořit nové řešení OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | Načte výsledek asynchronní operace. |
> | Action | Microsoft.PolicyInsights/operations/read | Načte podporované operace v oboru názvů Microsoft. PolicyInsights. |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | Dotaz na informace o událostech zásad. |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/read | Dotaz na informace o událostech zásad. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | Dotaz na informace o stavech zásad. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/read | Dotaz na informace o stavech zásad. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/action | Dotaz na souhrnné informace o aktuálních stavech zásad. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/read | Dotaz na souhrnné informace o aktuálních stavech zásad. |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Aktivuje nové vyhodnocení dodržování předpisů pro vybraný obor. |
> | Action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Dotaz na informace o prostředcích vyžadovaných zásadami DeployIfNotExists |
> | Action | Microsoft.PolicyInsights/register/action | Zaregistruje poskytovatele prostředků Microsoft Policy Insights a povolí akce. |
> | Action | Microsoft. PolicyInsights/nápravy/zrušit/akce | Zruší probíhající nápravu zásad Microsoftu. |
> | Action | Microsoft.PolicyInsights/remediations/delete | Odstraňte nápravy zásad. |
> | Action | Microsoft.PolicyInsights/remediations/listDeployments/read | Zobrazí seznam nasazení vyžadovaných nápravou zásad. |
> | Action | Microsoft.PolicyInsights/remediations/read | Získejte nápravy zásad. |
> | Action | Microsoft.PolicyInsights/remediations/write | Vytvoří nebo aktualizuje nápravy zásad Microsoftu. |
> | Action | Microsoft.PolicyInsights/unregister/action | Zruší registraci poskytovatele prostředků Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Portal/konzoly/DELETE | Odebere instanci Cloud Shell. |
> | Action | Microsoft. Portal/konzoly/číst | Přečte instanci Cloud Shell. |
> | Action | Microsoft. Portal/konzoly/zápis | Vytvoří nebo aktualizuje instanci Cloud Shell. |
> | Action | Microsoft. Portal/řídicí panely/odstranit | Odebere řídicí panel z předplatného. |
> | Action | Microsoft. Portal/řídicí panely/číst | Přečte řídicí panely pro předplatné. |
> | Action | Microsoft. Portal/řídicí panely/zápis | Umožňuje přidat nebo upravit řídicí panel k předplatnému. |
> | Action | Microsoft. Portal/registrace/akce | Zaregistrovat na portál |
> | Action | Microsoft.Portal/usersettings/delete | Odebere Cloud Shell nastavení uživatele. |
> | Action | Microsoft.Portal/usersettings/read | Přečte nastavení Cloud Shell uživatele. |
> | Action | Microsoft.Portal/usersettings/write | Vytvoří nebo aktualizuje nastavení uživatele Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. PowerBIDedicated/kapacit/DELETE | Odstraní vyhrazenou kapacitu Power BI. |
> | Action | Microsoft. PowerBIDedicated/kapacit/čtení | Načte informace o zadané Power BI vyhrazené kapacitě. |
> | Action | Microsoft. PowerBIDedicated/kapacit/pokračování/akce | Obnoví kapacitu. |
> | Action | Microsoft. PowerBIDedicated/kapacity/SKU/číst | Načíst dostupné informace o SKU pro kapacitu |
> | Action | Microsoft. PowerBIDedicated/kapacit/pozastavit/akce | Pozastaví kapacitu. |
> | Action | Microsoft. PowerBIDedicated/kapacit/Write | Vytvoří nebo aktualizuje zadanou vyhrazenou kapacitu Power BI. |
> | Action | Microsoft. PowerBIDedicated/Locations/checkNameAvailability/Action | Kontroluje, že zadaný název vyhrazené kapacity Power BI je platný a nepoužívá se. |
> | Action | Microsoft.PowerBIDedicated/locations/operationresults/read | Načte informace o výsledku zadané operace. |
> | Action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Načte informace o stavu zadané operace. |
> | Action | Microsoft.PowerBIDedicated/operations/read | Načte informace o operacích. |
> | Action | Microsoft. PowerBIDedicated/Register/Action | Zaregistruje Power BI vyhrazeného poskytovatele prostředků. |
> | Action | Microsoft. PowerBIDedicated/SKU/číst | Načte informace o SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Action | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp je interní operace, kterou používá služba. |
> | Action | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItem/write | Vytvořit položku chráněnou zálohováním |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Action | microsoft.recoveryservices/Locations/backupStatus/action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | Action | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Ověřit funkce |
> | Action | Microsoft. RecoveryServices/Locations/checkNameAvailability/Action | Ověřit dostupnost názvu prostředku je rozhraní API, které kontroluje, jestli je název prostředku dostupný. |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci. |
> | Action | Microsoft. RecoveryServices/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Action | Microsoft. RecoveryServices/Register/Action | Zaregistruje předplatné pro daného poskytovatele prostředků. |
> | Action | Microsoft. recoveryservices/trezory/backupconfig/Read | Vrátí konfiguraci pro trezor Recovery Services. |
> | Action | Microsoft. recoveryservices/trezory/backupconfig/Write | Aktualizuje konfiguraci pro trezor Recovery Services. |
> | Action | Microsoft. recoveryservices/trezory/backupEngines/Read | Vrátí všechny servery správy zálohování zaregistrované s trezorem. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | Odstranit záložní záměr ochrany |
> | Action | Microsoft. recoveryservices/trezory/backupFabrics/backupProtectionIntent/Read | Získat záložní záměr ochrany |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvořit záložní záměr ochrany |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace. |
> | Action | Microsoft. recoveryservices/trezory/backupFabrics/protectableContainers/Read | Získat všechny chráněné kontejnery |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | Odstraní registrovaný kontejner. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | Dotazování na úlohy v rámci kontejneru |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Získat všechny položky v kontejneru |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Provede zálohování chráněné položky. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Odstraní chráněnou položku. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti objektu chráněné položky. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zřídit rychlé obnovení položky pro chráněnou položku |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Obnoví body obnovení pro chráněné položky |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odvolání okamžitého obnovení položky pro chráněnou položku |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvořit položku chráněnou zálohováním |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Vytvoří registrovaný kontejner. |
> | Action | Microsoft. recoveryservices/trezory/backupFabrics/refreshContainers/Action | Aktualizuje seznam kontejnerů. |
> | Action | Microsoft. recoveryservices/trezory/backupJobs/Cancel/Action | Zrušit úlohu |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Action | Microsoft. recoveryservices/trezory/backupJobs/Read | Vrátí všechny objekty úlohy. |
> | Action | Microsoft. recoveryservices/trezory/backupJobsExport/Action | Exportovat úlohy |
> | Action | microsoft.recoveryservices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování trezoru Recovery Services. |
> | Action | Microsoft. recoveryservices/trezory/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/delete | Odstraní zásady ochrany. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Action | Microsoft. recoveryservices/trezory/backupPolicies/operace/číst | Získá stav operace zásad. |
> | Action | Microsoft. recoveryservices/trezory/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | Action | Microsoft. recoveryservices/trezory/backupPolicies/Write | Vytvoří zásady ochrany. |
> | Action | Microsoft. recoveryservices/trezory/backupProtectableItems/Read | Vrátí seznam chránitelných položek. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery patřící k předplatnému. |
> | Action | Microsoft. recoveryservices/trezory/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | Action | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Vrátí informace o bezpečnostním PIN kódu pro trezor Recovery Services. |
> | Action | Microsoft. recoveryservices/trezory/backupstorageconfig/Read | Vrátí konfiguraci úložiště pro Recovery Services trezor. |
> | Action | Microsoft. recoveryservices/trezory/backupstorageconfig/Write | Aktualizuje konfiguraci úložiště pro trezor Recovery Services. |
> | Action | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | Action | Microsoft. recoveryservices/trezory/backupValidateOperation/Action | Ověřit operaci pro chráněnou položku |
> | Action | Microsoft. RecoveryServices/trezory/certifikáty/zapisovat | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | Action | Microsoft. RecoveryServices/trezory/odstranit | Operace odstranění trezoru odstraní zadaný prostředek Azure typu trezor. |
> | Action | Microsoft. RecoveryServices/trezory/extendedInformation/DELETE | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služby Recovery Services. |
> | Action | Microsoft. RecoveryServices/trezory/monitoringAlerts/Write | Vyřeší upozornění. |
> | Action | Microsoft. RecoveryServices/trezory/monitoringConfigurations/Read | Získá konfiguraci oznámení trezoru služby Recovery Services. |
> | Action | Microsoft. RecoveryServices/trezory/monitoringConfigurations/Write | Nakonfiguruje e-mailová oznámení pro trezor služby Recovery Services. |
> | Action | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft. RecoveryServices/trezory/registeredIdentities/DELETE | Operaci zrušení registrace kontejneru lze použít k odregistraci kontejneru. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operaci kontejneru registrovat službu lze použít k registraci kontejneru pomocí služby Recovery Services. |
> | Action | Microsoft. RecoveryServices/trezory/replicationAlertSettings/Read | Přečtěte si všechna nastavení výstrah. |
> | Action | Microsoft. RecoveryServices/trezory/replicationAlertSettings/Write | Vytvořit nebo aktualizovat nastavení výstrah |
> | Action | Microsoft. RecoveryServices/trezory/replicationEvents/Read | Čtení jakýchkoli událostí |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/checkConsistency/Action | Zkontroluje konzistenci prostředí infrastruktury. |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/DELETE | Odstranění všech prostředků infrastruktury |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/deployProcessServerImage/Action | Nasadit image procesového serveru |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/migratetoaad/Action | Migrace prostředků infrastruktury do AAD |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Čtení jakýchkoli prostředků infrastruktury |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Znovu přidružit bránu |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/Remove/Action | Odebrat prostředky infrastruktury |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Prodloužit platnost certifikátu pro Fabric |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationLogicalNetworks/Read | Čtení libovolných logických sítí |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Čtení všech sítí |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/replicationNetworkMappings/DELETE | Odstranit všechna mapování sítě |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/replicationNetworkMappings/Write | Vytvoří nebo aktualizuje všechna mapování sítě. |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/Action | Zjistit chráněnou položku |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/Remove/Action | Odebrat kontejner ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/DELETE | Odstranit všechny položky migrace |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migruje/Action | Migrovat položku |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/číst | Čtení všech bodů obnovení migrace |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Read | Čtení všech položek migrace |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/Action | Test migrace |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/Action | Vyčištění migrace testu |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Write | Vytvoření nebo aktualizace položek migrace |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Čtení všech chráněných položek |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/Action | Přidat disky |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Použít bod obnovení |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/DELETE | Odstranit všechny chráněné položky |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Potvrzení převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Plánované převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/číst | Čtení všech bodů obnovení replikace |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Remove/Action | Odebrat chráněnou položku |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/Action | Odebrat disky |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Opravit replikaci |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reprotect/Action | Znovu chránit chráněnou položku |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/Action |  |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/Action | Odeslat názor |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/číst | Čtení všech cílových výpočetních velikostí |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Testovací převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Vyčištění testovacího převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Převzetí služeb při selhání |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizovat službu mobility |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Vytvořit nebo aktualizovat jakékoli chráněné položky |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Odstranit všechna mapování kontejnerů ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Remove/Action | Odebrat mapování kontejneru ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Write | Vytvořit nebo aktualizovat všechna mapování kontejnerů ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Přepnout kontejner ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/Write | Vytvořit nebo aktualizovat všechny kontejnery ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/DELETE | Odstranit všechny poskytovatele Recovery Services |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Aktualizovat zprostředkovatele |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/Remove/Action | Odebrat poskytovatele Recovery Services |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/Write | Vytvořit nebo aktualizovat Recovery Services poskytovatelé |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/DELETE | Odstranit všechna mapování klasifikace úložiště |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Write | Vytvoří nebo aktualizuje všechna mapování klasifikace úložiště. |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/DELETE | Odstranit všechny servery vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst libovolný servery vCenter |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/Write | Vytvořit nebo aktualizovat libovolný servery vCenter |
> | Action | Microsoft. RecoveryServices/trezory/replicationFabrics/Write | Vytvoření nebo aktualizace prostředků infrastruktury |
> | Action | Microsoft. RecoveryServices/trezory/replicationJobs/Cancel/Action | Zrušit úlohu |
> | Action | Microsoft. RecoveryServices/trezory/replicationJobs/Read | Čtení všech úloh |
> | Action | Microsoft. RecoveryServices/trezory/replicationJobs/restart/Action | Restartovat úlohu |
> | Action | Microsoft. RecoveryServices/trezory/replicationJobs/Resume/Action | Pokračovat v úloze |
> | Action | Microsoft. RecoveryServices/trezory/replicationMigrationItems/Read | Čtení všech položek migrace |
> | Action | Microsoft. RecoveryServices/trezory/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | Action | Microsoft. RecoveryServices/trezory/replicationNetworks/Read | Čtení všech sítí |
> | Action | Microsoft. RecoveryServices/trezory/replicationPolicies/DELETE | Odstranit všechny zásady |
> | Action | Microsoft. RecoveryServices/trezory/replicationPolicies/Read | Čtení všech zásad |
> | Action | Microsoft. RecoveryServices/trezory/replicationPolicies/Write | Vytvořit nebo aktualizovat všechny zásady |
> | Action | Microsoft. RecoveryServices/trezory/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | Action | Microsoft. RecoveryServices/trezory/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/DELETE | Odstranění všech plánů obnovení |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/failoverCommit/Action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánovaného převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/Read | Čtení všech plánů obnovení |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/reprotect/Action | Znovu zapnout ochranu plánu obnovení |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/testFailover/Action | Plán obnovení testovacího převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/testFailoverCleanup/Action | Plán obnovení pro vyčištění testovacího převzetí služeb při selhání |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/Write | Vytvoření nebo aktualizace jakýchkoli plánů obnovení |
> | Action | Microsoft. RecoveryServices/trezory/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | Action | Microsoft. RecoveryServices/trezory/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | Action | Microsoft. RecoveryServices/trezory/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | Action | Microsoft. RecoveryServices/trezory/replicationSupportedOperatingSystems/Read | Přečíst vše  |
> | Action | Microsoft. RecoveryServices/trezory/replicationUsages/Read | Čtení všech využití replikace trezoru |
> | Action | Microsoft. RecoveryServices/trezory/replicationVaultHealth/Read | Přečtěte si stav replikace trezoru |
> | Action | Microsoft. RecoveryServices/trezory/replicationVaultHealth/Refresh/Action | Aktualizovat stav trezoru |
> | Action | Microsoft. RecoveryServices/trezory/replicationVaultSettings/Read | Přečíst vše  |
> | Action | Microsoft. RecoveryServices/trezory/replicationVaultSettings/Write | Vytvořit nebo aktualizovat libovolnou  |
> | Action | Microsoft. RecoveryServices/trezory/replicationvCenters/Read | Číst libovolný servery vCenter |
> | Action | Microsoft. recoveryservices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Action | Microsoft. RecoveryServices/trezory/používání/čtení | Čtení všech použití trezoru |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | Action | Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Relay/checkNameAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. |
> | Action | Microsoft. Relay/checkNamespaceAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. Toto rozhraní API je zastaralé, použijte místo toho CheckNameAvailability. |
> | Action | Microsoft. Relay/obory názvů/autorizačních pravidel/Action | Aktualizuje autorizační pravidlo oboru názvů. Toto rozhraní API je zastaralé. Místo toho prosím použijte volání PUT k aktualizaci autorizačního pravidla oboru názvů.. Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. Relay/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Action | Microsoft. Relay/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Action | Microsoft. Relay/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Action | Microsoft. Relay/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. Relay/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Action | Microsoft. Relay/obory názvů/odstranění | Odstranit prostředek oboru názvů |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/klíče listkey/Action | Získá klíče autorizačních pravidel pro primární obor názvů zotavení po havárii. |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/Read | Získat autorizační pravidla primárního oboru názvů pro zotavení po havárii |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/breakPairing/Action | Zakáže zotavení po havárii a zastaví replikaci změn z primárních oborů názvů na sekundární. |
> | Action | Microsoft. Relay/obory názvů/disasterrecoveryconfigs/checkNameAvailability/Action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/DELETE | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tuto operaci lze vyvolat pouze prostřednictvím primárního oboru názvů. |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/převzetí služeb při selhání/akce | Vyvolá převzetí služeb při selhání GEOGRAFICKého zotavení po havárii a změní konfiguraci aliasu oboru názvů tak, aby odkazoval na sekundární obor názvů. |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/Read | Získá konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Action | Microsoft. Relay/obory názvů/disasterRecoveryConfigs/Write | Vytvoří nebo aktualizuje konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/Action | Operace pro aktualizaci HybridConnection Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel HybridConnection |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce pro HybridConnection |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/Read |  Získat seznam autorizačních pravidel HybridConnection |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/regeneratekeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel/Write | Vytvořte autorizační pravidla HybridConnection a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/DELETE | Operace odstranění prostředku HybridConnection |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/Read | Získá seznam popisů prostředků HybridConnection. |
> | Action | Microsoft. Relay/obory názvů/HybridConnections/Write | Vytvoří nebo aktualizuje vlastnosti HybridConnection. |
> | Action | Microsoft. Relay/obory názvů/messagingPlan/Read | Získá plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. Relay/obory názvů/messagingPlan/Write | Aktualizuje plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. Relay/obory názvů/operationresults/Read | Získat stav operace oboru názvů |
> | Action | Microsoft. Relay/obory názvů/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Action | Microsoft. Relay/obory názvů/čtení | Získat seznam popisu prostředku oboru názvů |
> | Action | Microsoft. Relay/obory názvů/removeAcsNamepsace/Action | Odebrat obor názvů ACS |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel/Action | Operace pro aktualizaci WcfRelay Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel WcfRelay |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce pro WcfRelay |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel/Read |  Získat seznam autorizačních pravidel WcfRelay |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel/regeneratekeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel/Write | Vytvořte autorizační pravidla WcfRelay a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/DELETE | Operace odstranění prostředku WcfRelay |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/Read | Získá seznam popisů prostředků WcfRelay. |
> | Action | Microsoft. Relay/obory názvů/WcfRelays/Write | Vytvoří nebo aktualizuje vlastnosti WcfRelay. |
> | Action | Microsoft. Relay/obory názvů/zápis | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Action | Microsoft. Relay/Operations/Read | Získat operace |
> | Action | Microsoft. Relay/registrace/akce | Zaregistruje předplatné pro poskytovatele prostředků Relay a povolí vytváření prostředků přenosu. |
> | Action | Microsoft. Relay/zrušit registraci/akce | Zaregistruje předplatné pro poskytovatele prostředků Relay a povolí vytváření prostředků přenosu. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Získá stav dostupnosti pro zadaný prostředek. |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Action | Microsoft. ResourceHealth/Events/Read | Získat Service Health události pro dané předplatné |
> | Action | Microsoft. Resourcehealth/healthevent/Action | Označuje změnu ve stavu pro zadaný prostředek. |
> | Action | Microsoft. Resourcehealth/healthevent/Activated/Action | Označuje změnu ve stavu pro zadaný prostředek. |
> | Action | Microsoft. Resourcehealth/healthevent/InProgress/Action | Označuje změnu ve stavu pro zadaný prostředek. |
> | Action | Microsoft. Resourcehealth/healthevent/Pending/Action | Označuje změnu ve stavu pro zadaný prostředek. |
> | Action | Microsoft. Resourcehealth/healthevent/vyřešený/Action | Označuje změnu ve stavu pro zadaný prostředek. |
> | Action | Microsoft. Resourcehealth/healthevent/aktualizované/Action | Označuje změnu ve stavu pro zadaný prostředek. |
> | Action | Microsoft.ResourceHealth/impactedResources/read | Získat ovlivněné prostředky pro dané předplatné |
> | Action | Microsoft.ResourceHealth/metadata/read | Načte metadata |
> | Action | Microsoft.ResourceHealth/Operations/read | Získejte dostupné operace pro Microsoft ResourceHealth. |
> | Action | Microsoft.ResourceHealth/register/action | Zaregistruje předplatné pro Microsoft ResourceHealth. |
> | Action | Microsoft.ResourceHealth/unregister/action | Zruší registraci předplatného pro Microsoft ResourceHealth. |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Resources/checkPolicyCompliance/Action | Zkontroluje stav dodržování předpisů daného prostředku proti zásadám prostředků. |
> | Action | Microsoft. Resources/checkResourceName/Action | Ověřte platnost názvu prostředku. |
> | Action | Microsoft. Resources/nasazení/zrušení/akce | Zruší nasazení. |
> | Action | Microsoft. Resources/nasazení/odstranění | Odstraní nasazení. |
> | Action | Microsoft. Resources/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Action | Microsoft. Resources/nasazení/čtení | Načte nebo vypíše nasazení. |
> | Action | Microsoft. Resources/nasazení/ověření/akce | Ověří nasazení. |
> | Action | Microsoft. Resources/nasazení/whatIf/Action | Odhadne změny nasazení šablony. |
> | Action | Microsoft. Resources/nasazení/zápis | Vytvoří nebo aktualizuje nasazení. |
> | Action | Microsoft. Resources/Links/DELETE | Odstraní odkaz na prostředek. |
> | Action | Microsoft. Resources/Links/Read | Načte nebo vypíše odkazy na prostředky. |
> | Action | Microsoft. Resources/Links/Write | Vytvoří nebo aktualizuje odkaz na prostředek. |
> | Action | Microsoft. Resources/Marketplace/purchase/Action | Nákup prostředku z Marketplace. |
> | Action | Microsoft. Resources/Providers/Read | Získá seznam zprostředkovatelů. |
> | Action | Microsoft. Resources/Resources/Read | Získá seznam prostředků založený na filtrech. |
> | Action | Microsoft. Resources/Subscriptions/Locations/Read | Získá seznam podporovaných umístění. |
> | Action | Microsoft. Resources/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | Action | Microsoft. Resources/Subscriptions/Providers/Read | Získá nebo vypíše poskytovatele prostředků. |
> | Action | Microsoft. Resources/Subscriptions/Read | Získá seznam předplatných. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/DELETE | Odstraní skupinu prostředků a všechny její prostředky. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/operationstatuses/Read | Získá nebo zobrazí seznam stavů operací nasazení. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Read | Načte nebo vypíše nasazení. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Write | Vytvoří nebo aktualizuje nasazení. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action | Přesune prostředky z jedné skupiny prostředků do jiné. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/Resources/Read | Získá prostředky pro skupinu prostředků. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/validateMoveResources/Action | Ověřte přesunutí prostředků z jedné skupiny prostředků do jiné. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/Write | Vytvoří nebo aktualizuje skupinu prostředků. |
> | Action | Microsoft. Resources/Subscriptions/Resources/Read | Získá prostředky předplatného. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/DELETE | Odstraní značku předplatného. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/Read | Načte nebo vypíše značky předplatného. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Odstraní hodnotu značky předplatného. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/tagValues/Read | Získá nebo zobrazí seznam hodnot značek předplatného. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Přidá hodnotu značky předplatného. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/Write | Přidá značku předplatného. |
> | Action | Microsoft. Resources/Tags/DELETE | Odebere všechny značky na prostředku. |
> | Action | Microsoft. Resources/Tags/Read | Načte všechny značky na prostředku. |
> | Action | Microsoft. Resources/Tags/Write | Aktualizuje značky na prostředku nahrazením nebo sloučením stávajících značek novou sadou značek nebo odebráním existujících značek. |
> | Action | Microsoft. Resources/klienti/číst | Získá seznam tenantů. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Scheduler/jobcollections/DELETE | Odstraní kolekci úloh. |
> | Action | Microsoft. Scheduler/jobcollections/Disable/Action | Zakáže shromažďování úloh. |
> | Action | Microsoft. Scheduler/jobcollections/Enable/Action | Povolí shromažďování úloh. |
> | Action | Microsoft. Scheduler/jobcollections/Jobs/DELETE | Odstraní úlohu. |
> | Action | Microsoft. Scheduler/jobcollections/Jobs/generateLogicAppDefinition/Action | Vygeneruje definici aplikace logiky na základě úlohy plánovače. |
> | Action | Microsoft. Scheduler/jobcollections/Jobs/jobhistories/Read | Získá historii úlohy. |
> | Action | Microsoft. Scheduler/jobcollections/Jobs/Read | Získá úlohu. |
> | Action | Microsoft. Scheduler/jobcollections/Jobs/Run/Action | Spustí úlohu. |
> | Action | Microsoft. Scheduler/jobcollections/Jobs/Write | Vytvoří nebo aktualizuje úlohu. |
> | Action | Microsoft. Scheduler/jobcollections/Read | Získat kolekci úloh |
> | Action | Microsoft.Scheduler/jobcollections/write | Vytvoří nebo aktualizuje kolekci úloh. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Search/checkNameAvailability/Action | Zkontroluje dostupnost názvu služby. |
> | Action | Microsoft.Search/operations/read | Zobrazí seznam všech dostupných operací poskytovatele Microsoft. Search. |
> | Action | Microsoft. Search/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků vyhledávání a povolí vytváření vyhledávacích služeb. |
> | Action | Microsoft.Search/searchServices/createQueryKey/action | Vytvoří klíč dotazu. |
> | Action | Microsoft.Search/searchServices/delete | Odstraní vyhledávací službu. |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | Odstraní klíč dotazu. |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | Přečte klíče správce. |
> | Action | Microsoft.Search/searchServices/listQueryKeys/read | Vrátí seznam klíčů rozhraní API dotazů pro danou službu Azure Search. |
> | Action | Microsoft.Search/searchServices/read | Přečte vyhledávací službu. |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | Znovu vygeneruje klíč správce. |
> | Action | Microsoft.Search/searchServices/start/action | Spustí vyhledávací službu. |
> | Action | Microsoft. Search/searchServices/stop/Action | Zastaví vyhledávací službu. |
> | Action | Microsoft.Search/searchServices/write | Vytvoří nebo aktualizuje vyhledávací službu. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Security/adaptiveNetworkHardenings/vymáhat/Action | Vynutilí daná pravidla posílení provozu tím, že vytváří v daných skupinách zabezpečení sítě vytvořením odpovídajícího pravidla zabezpečení. |
> | Action | Microsoft. Security/adaptiveNetworkHardenings/Read | Získá doporučení adaptivního posílení zabezpečení sítě u chráněného prostředku Azure. |
> | Action | Microsoft. Security/advancedThreatProtectionSettings/Read | Získá nastavení rozšířené ochrany před internetovými útoky pro daný prostředek. |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualizuje nastavení rozšířené ochrany před internetovými útoky pro daný prostředek. |
> | Action | Microsoft. Security/Alerts/číst | Získá všechny dostupné výstrahy zabezpečení. |
> | Action | Microsoft. Security/applicationWhitelistings/Read | Získá seznam povolených aplikací. |
> | Action | Microsoft. Security/applicationWhitelistings/Write | Vytvoří novou aplikaci, která bude seznam povolených, nebo aktualizuje stávající. |
> | Action | Microsoft. Security/complianceResults/Read | Získá výsledky dodržování předpisů pro daný prostředek. |
> | Action | Microsoft. Security/informationProtectionPolicies/Read | Získá zásady ochrany informací pro prostředek. |
> | Action | Microsoft. Security/informationProtectionPolicies/Write | Aktualizuje zásady ochrany informací pro daný prostředek. |
> | Action | Microsoft. Security/umístění/výstrahy/aktivace/akce | Aktivace výstrahy zabezpečení |
> | Action | Microsoft. Security/umístění/výstrahy/přeskočit/akce | Zavřít výstrahu zabezpečení |
> | Action | Microsoft. Security/umístění/výstrahy/čtení | Získá všechny dostupné výstrahy zabezpečení. |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Odstraní zásady přístupu k síti za běhu. |
> | Action | Microsoft. Security/Locations/jitNetworkAccessPolicies/iniciovat/Action | Inicializuje požadavek zásad přístupu k síti za běhu. |
> | Action | Microsoft. Security/Locations/jitNetworkAccessPolicies/Read | Získá zásady přístupu k síti za běhu. |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Vytvoří novou zásadu přístupu k síti za běhu nebo aktualizuje stávající. |
> | Action | Microsoft. Security/Locations/Read | Získá umístění dat zabezpečení. |
> | Action | Microsoft. Security/Locations/Tasks/Activate/Action | Aktivovat doporučení zabezpečení |
> | Action | Microsoft. Security/umístění/úkoly/zavřít/akce | Přeskočit doporučení zabezpečení |
> | Action | Microsoft. Security/umístění/úkoly/číst | Získá všechna dostupná doporučení zabezpečení. |
> | Action | Microsoft. Security/umístění/úkoly/vyřešit/akce | Vyřešit doporučení zabezpečení |
> | Action | Microsoft. Security/Locations/Tasks/Start/Action | Spustit doporučení zabezpečení |
> | Action | Microsoft. Security/policies/Read | Získá zásady zabezpečení. |
> | Action | Microsoft. Security/policies/Write | Aktualizuje zásady zabezpečení. |
> | Action | Microsoft.Security/pricings/delete | Odstraní nastavení cen pro obor. |
> | Action | Microsoft. Security/Prices/Ready | Získá nastavení cen pro obor. |
> | Action | Microsoft. Security/Prices/Write | Aktualizuje nastavení cen pro obor. |
> | Action | Microsoft. Security/registr/Action | Zaregistruje předplatné pro Azure Security Center. |
> | Action | Microsoft. Security/securityContacts/DELETE | Odstraní kontakt zabezpečení. |
> | Action | Microsoft. Security/securityContacts/Read | Získá kontakt zabezpečení. |
> | Action | Microsoft. Security/securityContacts/Write | Aktualizuje kontakt na zabezpečení. |
> | Action | Microsoft.Security/securitySolutions/delete | Odstraní řešení zabezpečení. |
> | Action | Microsoft.Security/securitySolutions/read | Získá řešení zabezpečení. |
> | Action | Microsoft.Security/securitySolutions/write | Vytvoří nové řešení zabezpečení nebo aktualizuje stávající. |
> | Action | Microsoft. Security/securitySolutionsReferenceData/Read | Získá referenční data řešení zabezpečení. |
> | Action | Microsoft. Security/securityStatuses/Read | Získá stav zabezpečení pro prostředky Azure. |
> | Action | Microsoft. Security/securityStatusesSummaries/Read | Získá souhrn stavů zabezpečení pro obor. |
> | Action | Microsoft. Security/Settings/Read | Získá nastavení oboru. |
> | Action | Microsoft.Security/settings/write | Aktualizuje nastavení oboru. |
> | Action | Microsoft. Security/Tasks/Read | Získá všechna dostupná doporučení zabezpečení. |
> | Action | Microsoft. Security/zrušit registraci/akce | Zruší registraci předplatného z Azure Security Center. |
> | Action | Microsoft.Security/webApplicationFirewalls/delete | Odstraní bránu firewall webových aplikací. |
> | Action | Microsoft. Security/webApplicationFirewalls/Read | Načte brány firewall webových aplikací. |
> | Action | Microsoft.Security/webApplicationFirewalls/write | Vytvoří novou bránu firewall webových aplikací nebo aktualizuje stávající. |
> | Action | Microsoft. Security/workspaceSettings/Connect/Action | Změna nastavení opětovného připojení nastavení pracovního prostoru |
> | Action | Microsoft. Security/workspaceSettings/DELETE | Odstraní nastavení pracovního prostoru. |
> | Action | Microsoft. Security/workspaceSettings/Read | Získá nastavení pracovního prostoru. |
> | Action | Microsoft. Security/workspaceSettings/Write | Aktualizuje nastavení pracovního prostoru. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | Odstranění nastavení diagnostiky |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | Čtení nastavení diagnostiky |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | Zápis nastavení diagnostiky |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Čtení kategorií nastavení diagnostiky |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. ServiceBus/checkNameAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. |
> | Action | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Zkontroluje dostupnost oboru názvů v rámci daného předplatného. Toto rozhraní API je zastaralé, použijte místo toho CheckNameAvailability. |
> | Action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla virtuální sítě v poskytovateli prostředků ServiceBus pro zadanou virtuální síť. |
> | Action | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/Action | Aktualizuje autorizační pravidlo oboru názvů. Toto rozhraní API je zastaralé. Místo toho prosím použijte volání PUT k aktualizaci autorizačního pravidla oboru názvů.. Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/DELETE | Odstraní autorizační pravidlo oboru názvů. Výchozí autorizační pravidlo oboru názvů nelze odstranit.  |
> | Action | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k oboru názvů |
> | Action | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/Read | Získá seznam popisů autorizačních pravidel oborů názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. ServiceBus/obory názvů/autorizačních pravidel/Write | Vytvořte autorizační pravidla na úrovni oboru názvů a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel, primární a sekundární klíče lze aktualizovat. |
> | Action | Microsoft. ServiceBus/obory názvů/DELETE | Odstranit prostředek oboru názvů |
> | Action | Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/autorizačních pravidel/klíče listkey/Action | Získá klíče autorizačních pravidel pro primární obor názvů zotavení po havárii. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat autorizační pravidla primárního oboru názvů pro zotavení po havárii |
> | Action | Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/breakPairing/Action | Zakáže zotavení po havárii a zastaví replikaci změn z primárních oborů názvů na sekundární. |
> | Action | Microsoft. ServiceBus/obory názvů/disasterrecoveryconfigs/checkNameAvailability/Action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tuto operaci lze vyvolat pouze prostřednictvím primárního oboru názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/převzetí služeb při selhání/akce | Vyvolá převzetí služeb při selhání GEOGRAFICKého zotavení po havárii a změní konfiguraci aliasu oboru názvů tak, aby odkazoval na sekundární obor názvů. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci zotavení po havárii přidruženou k oboru názvů. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Odstraní filtr Event Grid přidružený k oboru názvů. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Získá filtr Event Grid přidružený k oboru názvů. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event Grid přidružený k oboru názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/eventhubs/Read | Získá seznam popisů prostředků EventHub. |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Odstranit prostředek filtru IP |
> | Action | Microsoft. ServiceBus/obory názvů/ipFilterRules/Read | Získat prostředek filtru IP |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Vytvořit prostředek filtru IP |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/procházet/akce | Procházet messges |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/odložení/akce | Odložit zprávy |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/přijmout/akce | Příjem zpráv |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/plán/akce | Shedule zprávy |
> | Akce dataaction | Microsoft. ServiceBus/obory názvů/zprávy/odeslání/akce | Odesílání zpráv |
> | Akce dataaction | Microsoft. ServiceBus/Namespaces/Messages/setstate/Action | Nastavit stav relace |
> | Action | Microsoft. ServiceBus/obory názvů/messagingPlan/Read | Získá plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. ServiceBus/obory názvů/messagingPlan/Write | Aktualizuje plán zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti vystavené prostřednictvím prostředku MessagingPlan se přesunou do prostředku oboru názvů (nadřazený) v pozdějších verzích rozhraní API.<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Action | Microsoft. ServiceBus/obory názvů/migrace/akce | Migrovat operaci oboru názvů |
> | Action | Microsoft. ServiceBus/obory názvů/migrationConfigurations/DELETE | Odstraní konfiguraci migrace. |
> | Action | Microsoft. ServiceBus/obory názvů/migrationConfigurations/Read | Získá konfiguraci migrace, která indikuje stav migrace a probíhající operace replikace. |
> | Action | Microsoft. ServiceBus/obory názvů/migrationConfigurations/REVERT/Action | Obnoví migraci oboru názvů Standard na Premium. |
> | Action | Microsoft. ServiceBus/obory názvů/migrationConfigurations/upgrade/akce | Přiřadí DNS přidružené k oboru názvů Standard do oboru názvů Premium, který dokončí migraci a zastaví synchronizaci prostředků z oboru názvů Standard na Premium. |
> | Action | Microsoft. ServiceBus/obory názvů/migrationConfigurations/Write | Vytvoří nebo aktualizuje konfiguraci migrace. Tím se spustí synchronizace prostředků z úrovně Standard na obor názvů Premium. |
> | Action | Microsoft. ServiceBus/obory názvů/networkruleset/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Action | Microsoft. ServiceBus/obory názvů/networkruleset/Read | Získá prostředek NetworkRuleSet. |
> | Action | Microsoft. ServiceBus/obory názvů/networkruleset/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Action | Microsoft. ServiceBus/obory názvů/networkrulesets/DELETE | Odstranit prostředek pravidla virtuální sítě |
> | Action | Microsoft. ServiceBus/obory názvů/networkrulesets/Read | Získá prostředek NetworkRuleSet. |
> | Action | Microsoft. ServiceBus/obory názvů/networkrulesets/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Action | Microsoft. ServiceBus/obory názvů/operationresults/Read | Získat stav operace oboru názvů |
> | Action | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Read | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/diagnosticSettings/Write | Získá seznam popisů prostředků nastavení diagnostiky oboru názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/logDefinitions/Read | Získá seznam popisů prostředků v názvech oborů názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/Providers/Microsoft. Insights/metricDefinitions/Read | Získá seznam popisů prostředků metrik oborů názvů. |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/Action | Operace pro aktualizaci fronty Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel fronty |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/klíče listkey/Action | Získá připojovací řetězec k zařazení do fronty. |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/Read |  Získá seznam autorizačních pravidel fronty. |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel/Write | Vytvořte autorizační pravidla fronty a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Action | Microsoft. ServiceBus/obory názvů/Queues/DELETE | Operace odstranění prostředku fronty |
> | Action | Microsoft. ServiceBus/obory názvů/fronty/čtení | Získá seznam popisů prostředků fronty. |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | Vytvoří nebo aktualizuje vlastnosti fronty. |
> | Action | Microsoft. ServiceBus/obory názvů/číst | Získat seznam popisu prostředku oboru názvů |
> | Action | Microsoft. ServiceBus/obory názvů/removeAcsNamepsace/Action | Odebrat obor názvů ACS |
> | Action | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/Action | Operace pro aktualizaci tématu Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. K aktualizaci autorizačního pravidla prosím použijte volání PUT. |
> | Action | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/DELETE | Operace odstranění autorizačních pravidel tématu |
> | Action | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/klíče listkey/Action | Získání připojovacího řetězce k tématu |
> | Action | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/číst |  Získat seznam autorizačních pravidel pro témata |
> | Action | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat primární nebo sekundární klíč k prostředku |
> | Action | Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel/Write | Vytvořte pravidla autorizace tématu a aktualizujte její vlastnosti. Přístupová práva autorizačních pravidel je možné aktualizovat. |
> | Action | Microsoft. ServiceBus/obory názvů/témata/DELETE | Operace odstranění prostředku tématu |
> | Action | Microsoft. ServiceBus/obory názvů/témata/číst | Získá seznam popisů prostředků tématu. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operace odstranění prostředku TopicSubscription |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Získá seznam popisů prostředků TopicSubscription. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operace odstranění prostředku pravidla |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Získá seznam popisů prostředků pravidel. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Vytvoří nebo aktualizuje vlastnosti pravidla. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Vytvoří nebo aktualizuje vlastnosti TopicSubscription. |
> | Action | Microsoft.ServiceBus/namespaces/topics/write | Vytvoří nebo aktualizuje vlastnosti tématu. |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Odstranit prostředek pravidla virtuální sítě |
> | Action | Microsoft. ServiceBus/obory názvů/virtualNetworkRules/Read | Získá prostředek pravidla virtuální sítě. |
> | Action | Microsoft. ServiceBus/obory názvů/virtualNetworkRules/Write | Vytvořit prostředek pravidla virtuální sítě |
> | Action | Microsoft. ServiceBus/obory názvů/zápis | Vytvořte prostředek oboru názvů a aktualizujte jeho vlastnosti. Značky a kapacita oboru názvů jsou vlastnosti, které lze aktualizovat. |
> | Action | Microsoft.ServiceBus/operations/read | Získat operace |
> | Action | Microsoft. ServiceBus/Register/Action | Zaregistruje předplatné pro poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |
> | Action | Microsoft. ServiceBus/SKU/číst | Získá seznam popisů prostředků SKU. |
> | Action | Microsoft. ServiceBus/SKU/Regions/Read | Získá seznam popisů prostředků SkuRegions. |
> | Action | Microsoft. ServiceBus/zrušit registraci/akce | Zaregistruje předplatné pro poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.ServiceFabric/clusters/applications/delete | Odstranění libovolné aplikace |
> | Action | Microsoft. ServiceFabric/clustery/aplikace/číst | Čtení libovolné aplikace |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/delete | Odstranění jakékoli služby |
> | Action | Microsoft. ServiceFabric/Clusters/Applications/Services/partitions/Read | Číst libovolný oddíl |
> | Action | Microsoft. ServiceFabric/clustery/aplikace/služby/oddíly/repliky/čtení | Čtení libovolné repliky |
> | Action | Microsoft. ServiceFabric/Clusters/Applications/Services/Ready | Čtení libovolné služby |
> | Action | Microsoft. ServiceFabric/Clusters/Applications/Services/STATUSS/Read | Číst libovolný stav služby |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/write | Vytvoření nebo aktualizace jakékoli služby |
> | Action | Microsoft.ServiceFabric/clusters/applications/write | Vytvořit nebo aktualizovat libovolnou aplikaci |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Odstranění libovolného typu aplikace |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/Read | Čtení libovolného typu aplikace |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/Versions/DELETE | Odstranění libovolné verze typu aplikace |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/verze/číst | Čtení libovolné verze typu aplikace |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/verze/Write | Vytvořit nebo aktualizovat libovolnou verzi typu aplikace |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/write | Vytvořit nebo aktualizovat libovolný typ aplikace |
> | Action | Microsoft.ServiceFabric/clusters/delete | Odstraní libovolný cluster. |
> | Action | Microsoft. ServiceFabric/Clusters/Nodes/Read | Číst libovolný uzel |
> | Action | Microsoft.ServiceFabric/clusters/read | Číst libovolný cluster |
> | Action | Microsoft. ServiceFabric/clustery/stavy/číst | Čtení všech stavů clusteru |
> | Action | Microsoft.ServiceFabric/clusters/write | Vytvoří nebo aktualizuje libovolný cluster. |
> | Action | Microsoft. ServiceFabric/Locations/clusterVersions/Read | Čtení libovolné verze clusteru |
> | Action | Microsoft. ServiceFabric/umístění/prostředí/clusterVersions/číst | Čtení libovolné verze clusteru pro konkrétní prostředí |
> | Action | Microsoft. ServiceFabric/Locations/operationresults/Read | Čtení všech výsledků operací |
> | Action | Microsoft.ServiceFabric/locations/operations/read | Čtení libovolných operací podle umístění |
> | Action | Microsoft.ServiceFabric/operations/read | Čtení všech dostupných operací |
> | Action | Microsoft. ServiceFabric/Register/Action | Registrovat jakoukoli akci |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. SignalRService/Locations/checknameavailability/Action | Zkontroluje, jestli je název k dispozici pro použití s novou službou Signal. |
> | Action | Microsoft. SignalRService/Locations/operationresults/signaler/Read | Dotaz na stav asynchronní operace |
> | Action | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Dotaz na stav asynchronní operace |
> | Action | Microsoft. SignalRService/umístění/použití/číst | Získání využití kvót pro službu Azure Signal |
> | Action | Microsoft. SignalRService/operationresults/Read | Dotaz na stav asynchronní operace |
> | Action | Microsoft. SignalRService/stav operationstatus/Read | Dotaz na stav asynchronní operace |
> | Action | Microsoft. SignalRService/Register/Action | Zaregistruje poskytovatele prostředků Microsoft. SignalRService s předplatným. |
> | Action | Microsoft. SignalRService/Signaler/DELETE | Odstraní celou službu Signal. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Odstraní filtr Event gridu z signálu. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/read | Získá vlastnosti zadaného filtru Event gridu nebo zobrazí seznam všech filtrů Event gridu určených pro daný signál. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event gridu pro signál se zadanými parametry. |
> | Action | Microsoft. SignalRService/Signaler/klíče listkey/Action | Zobrazení hodnoty přístupových klíčů signalizace na portálu pro správu nebo prostřednictvím rozhraní API |
> | Action | Microsoft. SignalRService/Signaler/Read | Zobrazení nastavení a konfigurací signalizace na portálu pro správu nebo prostřednictvím rozhraní API |
> | Action | Microsoft. SignalRService/Signaler/RegenerateKey/Action | Změna hodnoty přístupových klíčů signalizace na portálu pro správu nebo prostřednictvím rozhraní API |
> | Action | Microsoft. SignalRService/Signaler/restart/Action | Restartování služby signalizace Azure na portálu pro správu nebo prostřednictvím rozhraní API. Dojde k určitému výpadku. |
> | Action | Microsoft. SignalRService/Signaler/Write | Úprava nastavení a konfigurací signalizace na portálu pro správu nebo prostřednictvím rozhraní API |
> | Action | Microsoft. SignalRService/zrušit registraci/akce | Zruší registraci poskytovatele prostředků Microsoft. SignalRService u předplatného. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Solutions/applicationDefinitions/applicationArtifacts/Read | Uvádí artefakty aplikace definice aplikace. |
> | Action | Microsoft. Solutions/applicationDefinitions/DELETE | Odebere definici aplikace. |
> | Action | Microsoft. Solutions/applicationDefinitions/Read | Načte seznam definic aplikací. |
> | Action | Microsoft. Solutions/applicationDefinitions/Write | Přidat nebo upravit definici aplikace |
> | Action | Microsoft. Solutions/Applications/applicationArtifacts/Read | Seznam artefaktů aplikace. |
> | Action | Microsoft. Solutions/Applications/DELETE | Odebere aplikaci. |
> | Action | Microsoft. Solutions/aplikace/číst | Načte seznam aplikací. |
> | Action | Microsoft. Solutions/Applications/refreshPermissions/Action | Aktualizuje oprávnění aplikace. |
> | Action | Microsoft. Solutions/Applications/updateAccess/Action | Aktualizuje přístup k aplikaci. |
> | Action | Microsoft. Solutions/aplikace/Write | Vytvoří aplikaci. |
> | Action | Microsoft.Solutions/jitRequests/delete | Odebrat JitRequest |
> | Action | Microsoft. Solutions/jitRequests/Read | Načte seznam JitRequests. |
> | Action | Microsoft.Solutions/jitRequests/write | Vytvoří JitRequest. |
> | Action | Microsoft.Solutions/locations/operationStatuses/read | Přečte stav operace pro daný prostředek. |
> | Action | Microsoft. Solutions/Register/Action | Zaregistrujte se do řešení. |
> | Action | Microsoft. Solutions/zrušit registraci/akce | Zruší registraci z řešení. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. SQL/checkNameAvailability/Action | Ověřte, jestli je zadaný název serveru k dispozici pro zřizování po celém světě pro dané předplatné. |
> | Action | Microsoft. SQL/instancePools/DELETE | Odstraní fond instancí. |
> | Action | Microsoft. SQL/instancePools/Read | Načte fond instancí. |
> | Action | Microsoft. SQL/instancePools/použití/čtení | Načte informace o využití fondu instancí. |
> | Action | Microsoft. SQL/instancePools/Write | Vytvoří nebo aktualizuje fond instancí. |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Načíst výsledek operace set zásad auditování objektu BLOB rozšířeného serveru |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Načíst výsledek operace nastavení zásad auditování objektu BLOB serveru |
> | Action | Microsoft. SQL/umístění/možnosti/číst | Získá možnosti pro toto předplatné v daném umístění. |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Získá stav databázové operace. |
> | Action | Microsoft. SQL/Locations/databaseOperationResults/Read | Získá stav databázové operace. |
> | Action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Načte probíhající operace na odstraněném serveru. |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | Načte probíhající operace na odstraněném serveru. |
> | Action | Microsoft.Sql/locations/deletedServers/read | Vrátí seznam odstraněných serverů nebo získá vlastnosti určeného odstraněného serveru. |
> | Action | Microsoft. SQL/Locations/deletedServers/Recovery/Action | Obnovení odstraněného serveru |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Získá asynchronní operaci Azure pro asynchronní operaci elastického fondu. |
> | Action | Microsoft. SQL/Locations/elasticPoolOperationResults/Read | Získá výsledek operace elastického fondu. |
> | Action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Načte probíhající operace na transparentní ochraně šifrování šifrování dat. |
> | Action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Načte probíhající operace na transparentní ochraně šifrování šifrování dat. |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Načíst výsledek operace set zásad auditování objektu BLOB rozšířeného serveru |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Načíst výsledek operace set zásad auditování objektu BLOB rozšířeného serveru |
> | Action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Získá stav operace pravidla brány firewall. |
> | Action | Microsoft. SQL/Locations/firewallRulesOperationResults/Read | Získá stav operace pravidla brány firewall. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | Odstraní skupinu převzetí služeb při selhání existující instance. |
> | Action | Microsoft. SQL/Locations/instanceFailoverGroups/převzetí služeb při selhání/akce | Spustí plánované převzetí služeb při selhání v existující skupině převzetí služeb při selhání. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Spustí vynucené převzetí služeb při selhání existující skupiny instancí. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | Vrátí seznam skupin instancí převzetí služeb při selhání nebo získá vlastnosti pro zadanou skupinu převzetí služeb při selhání. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti nebo značky zadané instance skupiny převzetí služeb při selhání. |
> | Action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Získá stav operace fondu instancí. |
> | Action | Microsoft. SQL/Locations/instancePoolOperationResults/Read | Získá výsledek pro operaci fondu instancí. |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Vrátí podrobnosti o konkrétní operaci asynchronního koncového bodu rozhraní Azure. |
> | Action | Microsoft. SQL/Locations/interfaceEndpointProfileOperationResults/Read | Vrátí podrobné informace o zadané operaci profilu koncového bodu rozhraní. |
> | Action | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Získá stav operace agenta úlohy. |
> | Action | Microsoft. SQL/Locations/jobAgentOperationResults/Read | Získá výsledek operace agenta úlohy. |
> | Action | Microsoft. SQL/Locations/longTermRetentionBackups/Read | Uvádí dlouhodobé zálohy uchovávání pro každou databázi na každém serveru v lokalitě. |
> | Action | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read | Uvádí dlouhodobé zálohy uchovávání pro každou databázi na serveru. |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Odstraní zálohu dlouhodobého uchovávání. |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Uvádí dlouhodobé zálohy uchovávání pro databázi. |
> | Action | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Dokončena operace obnovení spravované databáze |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Načte probíhající operace na transparentní ochraně šifrování spravované instance šifrování dat. |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Načte probíhající operace na transparentní ochraně šifrování spravované instance šifrování dat. |
> | Action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Načte probíhající operace na transparentních klíčích instance spravovaného šifrování dat. |
> | Action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Načte probíhající operace na transparentních klíčích instance spravovaného šifrování dat. |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Načte probíhající operace na transparentní šifrování dat spravované databáze. |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Načte probíhající operace na transparentní šifrování dat spravované databáze. |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Získá výsledek operace připojení privátního koncového bodu. |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Získá výsledek operace připojení privátního koncového bodu. |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Action | Microsoft. SQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Získá výsledek pro operaci proxy připojení privátního koncového bodu. |
> | Action | Microsoft. SQL/umístění/čtení | Získá dostupná umístění pro dané předplatné. |
> | Action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Action | Microsoft. SQL/Locations/serverKeyOperationResults/Read | Načte probíhající operace na transparentních klíčích serveru šifrovacího šifrování dat. |
> | Action | Microsoft. SQL/Locations/syncAgentOperationResults/Read | Načíst výsledek operace prostředků agenta synchronizace |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | Načtení ID synchronizační databáze pro určitou oblast a předplatné |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | Načíst výsledek operace prostředku skupiny synchronizace |
> | Action | Microsoft. SQL/Locations/syncMemberOperationResults/Read | Načíst výsledek operace prostředku člena synchronizace |
> | Action | Microsoft. SQL/umístění/použití/čtení | Získá kolekci metrik využití pro toto předplatné v daném umístění. |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Vrátí podrobnosti zadaného pravidla virtuální sítě. asynchronní operace Azure  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Vrátí podrobnosti o zadané operaci pravidel virtuální sítě.  |
> | Action | Microsoft.Sql/managedInstances/administrators/delete | Odstraní existujícího správce spravované instance. |
> | Action | Microsoft. SQL/managedInstances/Administrators/Read | Načte seznam správců spravované instance. |
> | Action | Microsoft.Sql/managedInstances/administrators/write | Vytvoří nebo aktualizuje správce spravované instance se zadanými parametry. |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Získá krátkodobé zásady uchovávání informací pro spravovanou databázi. |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualizuje krátkodobé zásady uchovávání informací pro spravovanou databázi. |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Popisky citlivosti aktualizace v dávce |
> | Action | Microsoft.Sql/managedInstances/databases/delete | Odstraní existující spravovanou databázi. |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro databáze spravované instance. |
> | Action | Microsoft.Sql/managedInstances/databases/read | Načte existující spravovanou databázi. |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/read | Získá schéma spravované databáze. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Získat sloupec spravované databáze |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Odstraní popisek citlivosti daného sloupce. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Zakázat doporučení citlivosti na daném sloupci |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Povolit doporučení citlivosti na daném sloupci |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Získá popisek citlivosti daného sloupce. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Vytvoří nebo aktualizuje popisek citlivosti daného sloupce. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Získání tabulky spravované databáze |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Načíst seznam zásad detekce hrozeb spravované databáze konfigurovaných pro daný server |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Změna zásad detekce hrozeb databáze pro danou spravovanou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | Načte události zabezpečení spravované databáze. |
> | Action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Načte podrobnosti o transparentní šifrování dat databáze v dané spravované databázi. |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Změna transparentní šifrování dat databáze pro danou spravovanou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Načtení zásad posouzení ohrožení zabezpečení na givendatabase |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Odebrání směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Získat směrný plán pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Změna směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Převede existující výsledek kontroly na formát pro lidské čtení. Pokud už existuje, nic se nestane. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Provést kontrolu databáze posouzení ohrožení zabezpečení. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Vrátí seznam vyhodnocování ohrožení zabezpečení databáze nebo získá záznam o kontrole pro zadané ID vyhledávání. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Změna posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/managedInstances/databases/write | Vytvoří novou databázi nebo aktualizuje stávající databázi. |
> | Action | Microsoft.Sql/managedInstances/delete | Odstraní existující spravovanou instanci. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | Vrátí seznam chráněných šifrovacích serverů nebo získá vlastnosti pro zadané ochrany šifrování serveru. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | Aktualizuje vlastnosti pro zadanou ochranu pomocí šifrování serveru. |
> | Action | Microsoft.Sql/managedInstances/keys/delete | Odstraní existující klíč spravované instance Azure SQL. |
> | Action | Microsoft. SQL/managedInstances/Keys/číst | Vrátí seznam klíčů spravované instance nebo získá vlastnosti pro zadaný klíč spravované instance. |
> | Action | Microsoft.Sql/managedInstances/keys/write | Vytvoří klíč se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný klíč spravované instance. |
> | Action | Microsoft. SQL/managedInstances/metricDefinitions/Read | Získat definice metriky spravované instance |
> | Action | Microsoft. SQL/managedInstances/metriky/číst | Získat metriky spravované instance |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro spravované instance. |
> | Action | Microsoft. SQL/managedInstances/Providers/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro spravované instance |
> | Action | Microsoft.Sql/managedInstances/read | Vrátí seznam spravovaných instancí nebo získá vlastnosti zadané spravované instance. |
> | Action | Microsoft.Sql/managedInstances/recoverableDatabases/read | Vrátí seznam obnovitelných spravovaných databází. |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Získá krátkodobé zásady uchovávání informací pro vyřazenou spravovanou databázi. |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualizuje krátkodobé zásady uchovávání informací pro vyřazenou spravovanou databázi. |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Vrátí seznam obnovitelné vyřazených spravovaných databází. |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Načte seznam zásad detekce hrozeb spravovaného serveru nakonfigurovaných pro daný server. |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Změna zásad zjišťování hrozeb spravovaného serveru pro daný spravovaný server |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | Vytvořit nebo aktualizovat certifikát TDE |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro danou spravovanou instanci |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Načtení zásad posouzení ohrožení zabezpečení v dané spravované instanci |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Změna posouzení ohrožení zabezpečení pro danou spravovanou instanci |
> | Action | Microsoft.Sql/managedInstances/write | Vytvoří spravovanou instanci se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou spravovanou instanci. |
> | Action | Microsoft.Sql/operations/read | Získá dostupné operace REST. |
> | Action | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Určuje, jestli má uživatel povolený souhlas s připojením privátního koncového bodu. |
> | Action | Microsoft. SQL/registr/Action | Zaregistruje předplatné pro poskytovatele prostředků Microsoft SQL Database a povolí vytváření databází Microsoft SQL. |
> | Action | Microsoft. SQL/servery/administratorOperationResults/Read | Přijímá probíhající operace u správců serveru. |
> | Action | Microsoft. SQL/servery/Administrators/DELETE | Odstranit správce serveru |
> | Action | Microsoft. SQL/servery/správci/číst | Načíst podrobnosti o Správci serveru |
> | Action | Microsoft. SQL/servery/správci/zapisovat | Vytvořit nebo aktualizovat Správce serveru |
> | Action | Microsoft. SQL/servery/poradci/číst | Vrátí seznam poradců dostupných pro server. |
> | Action | Microsoft. SQL/servery/poradci/recommendedActions/číst | Vrátí seznam doporučených akcí zadaného Poradce pro server. |
> | Action | Microsoft. SQL/servery/Advisory/recommendedActions/Write | Použití doporučené akce na serveru |
> | Action | Microsoft. SQL/servery/poradci/zapisovat | Aktualizuje stav automatického spuštění služby Advisor na úrovni serveru. |
> | Action | Microsoft. SQL/servery/auditingPolicies/Read | Načte podrobnosti o výchozích zásadách auditování tabulek serveru nakonfigurovaných na daném serveru. |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | Změna výchozího auditování tabulky serveru pro daný server |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | Načíst výsledek operace nastavení zásad auditování objektu BLOB serveru |
> | Action | Microsoft.Sql/servers/auditingSettings/read | Načte podrobnosti o zásadách auditování objektů BLOB serveru nakonfigurovaných na daném serveru. |
> | Action | Microsoft.Sql/servers/auditingSettings/write | Změna auditování objektů BLOB serveru pro daný server |
> | Action | Microsoft. SQL/servery/automaticTuning/Read | Vrátí nastavení automatického ladění pro server. |
> | Action | Microsoft. SQL/servery/automaticTuning/Write | Aktualizuje nastavení automatického ladění pro server a vrátí aktualizované nastavení. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Odstraní existující archivační Trezor záloh. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Tato operace slouží k získání dlouhodobého trezoru pro dlouhodobé uchovávání záloh. Vrátí informace o trezoru zaregistrovanému na tomto serveru. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Tato operace se používá k registraci trezoru dlouhodobého uchovávání záloh na server. |
> | Action | Microsoft. SQL/servery/communicationLinks/DELETE | Odstraní existující odkaz na komunikaci serveru. |
> | Action | Microsoft. SQL/servery/communicationLinks/Read | Vrátí seznam komunikačních propojení určeného serveru. |
> | Action | Microsoft. SQL/servery/communicationLinks/Write | Vytvoří nebo aktualizuje odkaz na komunikaci serveru. |
> | Action | Microsoft. SQL/servery/connectionPolicies/Read | Vrátí seznam zásad připojení k serveru u zadaného serveru. |
> | Action | Microsoft. SQL/servery/connectionPolicies/Write | Vytvoří nebo aktualizuje zásadu připojení serveru. |
> | Action | Microsoft.Sql/servers/databases/advisors/read | Vrátí seznam poradců dostupných pro databázi. |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Vrátí seznam doporučených akcí zadaného Poradce pro databázi. |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Použití doporučené akce v databázi |
> | Action | Microsoft.Sql/servers/databases/advisors/write | Aktualizuje stav automatického spuštění služby Advisor na úrovni databáze. |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | Načíst podrobnosti o zásadách auditování tabulek konfigurovaných pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | Změna zásad auditování v tabulce pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | Načte podrobnosti o zásadách auditování objektů BLOB konfigurovaných pro danou databázi. |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | Změna zásad auditování objektů BLOB pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu objektu BLOB databáze |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | Vrátí nastavení automatického ladění pro databázi. |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje nastavení automatického ladění pro databázi a vrátí aktualizovaná nastavení. |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Získá stav databázové operace. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Vrátí seznam zásad archivace zálohy zadané databáze. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Vytvoří nebo aktualizuje zásadu archivace zálohy databáze. |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Získá krátkodobé zásady uchovávání informací pro databázi. |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Aktualizuje krátkodobé zásady uchovávání informací pro databázi. |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | Načíst podrobnosti o zásadách připojení konfigurovaných pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | Změna zásad připojení pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Popisky citlivosti aktualizace v dávce |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Vrátí seznam zásad pro maskování dat databáze. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Odstraní pravidlo zásad maskování dat pro danou databázi. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Načte Podrobnosti pravidla pro maskování dat nakonfigurovaného pro danou databázi. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Změna pravidla zásad maskování dat pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Změna zásad maskování dat pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Vrátí informace o kroku distribuovaného dotazu dotazu datového skladu pro vybrané ID kroku. |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Vrátí informace o distribučním dotazu datového skladu pro vybrané ID dotazu. |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Načte aktivity uživatelů SQL Data Warehouse instance, která zahrnuje běžící a pozastavené dotazy. |
> | Action | Microsoft.Sql/servers/databases/delete | Odstraní existující databázi. |
> | Action | Microsoft. SQL/servery/databáze/export/akce | Exportovat Azure SQL Database |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Načte podrobnosti o rozšířených zásadách auditování objektů BLOB konfigurovaných pro danou databázi. |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Změna zásad auditování rozšířeného objektu BLOB pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/extensions/read | Získá kolekci rozšíření pro databázi. |
> | Action | Microsoft.Sql/servers/databases/extensions/write | Změna rozšíření pro danou databázi |
> | Action | Microsoft. SQL/servery/databáze/převzetí služeb při selhání/akce | Převzetí služeb při selhání databáze iniciované zákazníkem. |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Načtení zásad geografického zálohování pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Vytvoření nebo aktualizace zásad pro biozálohování databáze |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | Načte probíhající operace importu a exportu. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Načte seznam dostupných oken údržby pro vybranou databázi. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | Získá nastavení systému Windows údržby pro vybranou databázi. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | Nastaví nastavení systému Windows pro správu pro vybranou databázi. |
> | Action | Microsoft. SQL/servery/databáze/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Action | Microsoft. SQL/servery/databáze/metriky/čtení | Návratové metriky pro databáze |
> | Action | Microsoft. SQL/servery/databáze/přesunout/akce | Změňte název existující databáze. |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | Získá stav databázové operace. |
> | Action | Microsoft.Sql/servers/databases/operations/cancel/action | Zruší Azure SQL Database čeká na asynchronní operaci, která ještě není dokončená. |
> | Action | Microsoft.Sql/servers/databases/operations/read | Vrátí seznam operací provedených v databázi. |
> | Action | Microsoft. SQL/servery/databáze/pozastavit/akce | Pozastavení databáze Azure SQL DataWarehouse |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. SQL/servery/databáze/poskytovatelé/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro databáze. |
> | Action | Microsoft. SQL/servery/databáze/poskytovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro databáze |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Vrátí kolekci textů dotazů, které odpovídají zadaným parametrům. |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | Vrátí aktuální hodnoty nastavení úložiště dotazů pro databázi. |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | Aktualizuje nastavení úložiště dotazů pro databázi. |
> | Action | Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti zadané databáze. |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/delete | Nuceně ukončit vztah replikace a s potenciální ztrátou dat |
> | Action | Microsoft. SQL/servery/databáze/replicationLinks/převzetí služeb při selhání/akce | Převzetí služeb při selhání po synchronizaci všech změn z primární databáze, aby se tato databáze relationship\u0027s jako primární a prováděla se vzdálená primární databáze sekundární |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Převzetí služeb při selhání hned s možnou ztrátou dat, provedení této databáze na primárním relationship\u0027s replikace a provedení vzdálené primární databáze do sekundárního počítače |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | Vrátí seznam replikačních propojení nebo získá vlastnosti pro zadané replikační odkazy. |
> | Action | Microsoft. SQL/servery/databáze/replicationLinks/zrušení propojení/akce | Vynucené ukončení vztahu replikace nebo po synchronizaci s partnerem |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizovat režim replikace pro odkaz na synchronní nebo asynchronní režim |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | Vytvoří nový bod obnovení. |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | Odstraní bod obnovení pro databázi. |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | Vrátí body obnovení pro databázi. |
> | Action | Microsoft. SQL/servery/databáze/pokračovat/akce | Obnoví databázi Azure SQL DataWarehouse. |
> | Action | Microsoft.Sql/servers/databases/schemas/read | Získá schéma databáze. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Získá sloupec databáze. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Odstraní popisek citlivosti daného sloupce. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Zakázat doporučení citlivosti na daném sloupci |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Povolit doporučení citlivosti na daném sloupci |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Získá popisek citlivosti daného sloupce. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Vytvoří nebo aktualizuje popisek citlivosti daného sloupce. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | Získat databázovou tabulku. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Načíst seznam doporučení indexu pro databázi |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Použít doporučení indexu |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Načte seznam zásad detekce hrozeb databáze nakonfigurovaných pro daný server. |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Změna zásad detekce hrozeb databáze pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | Získá kolekci metrik zabezpečení databáze. |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | Vypsat popisky citlivosti dané databáze |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Vrátí návrhy o škálování databáze nahoru nebo dolů na základě statistik provádění dotazů za účelem zvýšení výkonu nebo snížení nákladů. |
> | Action | Microsoft. SQL/servery/databáze/SKU/číst | Získá kolekci SKU, které jsou k dispozici pro databázi. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Zrušit synchronizaci skupiny synchronizace |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | Odstraní existující skupinu synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Vrátí seznam schémat databáze centra synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | Vrátí seznam protokolů skupin synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | Vrátí seznam skupin synchronizace nebo získá vlastnosti zadané skupiny synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Aktualizovat schéma databáze centra synchronizace |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu centra synchronizace |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Odstraní stávajícího člena synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Vrátí seznam členů synchronizace nebo získá vlastnosti zadaného člena synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Aktualizovat schéma člena synchronizace |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu člena synchronizace |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Vrátí seznam schémat členů databáze synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Vytvoří člena synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadaného člena synchronizace. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Aktivovat synchronizaci skupiny synchronizace |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | Vytvoří skupinu synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadané skupiny synchronizace. |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | Vrátí text Transact-SQL pro vybrané ID dotazu. |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | Vrátí agregovanou statistiku modulu runtime pro vybraný dotaz ve vybraném časovém období. |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | Vrátí agregovanou statistiku modulu runtime pro vybraný dotaz ve vybraném časovém období. |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Načte probíhající operace na transparentním šifrování dat. |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Načte stav a Podrobnosti funkce zabezpečení transparentního šifrování dat pro danou databázi. |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Změna transparentního stavu šifrování dat |
> | Action | Microsoft. SQL/servery/databáze/upgradeDataWarehouse/Action | Upgrade databáze Azure SQL DataWarehouse |
> | Action | Microsoft.Sql/servers/databases/usages/read | Získá informace o využití Azure SQL Database. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Načtení zásad posouzení ohrožení zabezpečení na givendatabase |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Odebrání směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Získat směrný plán pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Změna směrného plánu pravidla posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/prověřování/export/akce | Převede existující výsledek kontroly na formát pro lidské čtení. Pokud už existuje, nic se nestane. |
> | Action | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/prověřování/initiateScan/Action | Provést kontrolu databáze posouzení ohrožení zabezpečení. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Vrátí seznam vyhodnocování ohrožení zabezpečení databáze nebo získá záznam o kontrole pro zadané ID vyhledávání. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Změna posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentScans/Action | Provést kontrolu databáze posouzení ohrožení zabezpečení. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Načíst výsledek operace provedení kontroly vyhodnocení ohrožení zabezpečení databáze |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Načte podrobnosti posouzení ohrožení zabezpečení nakonfigurovaného pro danou databázi. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Změna posouzení ohrožení zabezpečení pro danou databázi |
> | Action | Microsoft.Sql/servers/databases/write | Vytvoří databázi se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou databázi. |
> | Action | Microsoft.Sql/servers/delete | Odstraní existující server. |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Odstraní existující konfigurace zotavení po havárii pro daný server. |
> | Action | Microsoft. SQL/servery/disasterRecoveryConfiguration/převzetí služeb při selhání/akce | Převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Vynucení převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Získá kolekci konfigurací zotavení po havárii, které zahrnují tento server. |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Změna konfigurace zotavení po havárii serveru |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | Vrátí seznam odhadů elastických fondů již vytvořených pro tento server. |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/write | Vytvoří nový odhad elastického fondu pro seznam poskytnutých databází. |
> | Action | Microsoft. SQL/servery/elasticPools/Advisors/Read | Vrátí seznam poradců dostupných pro elastický fond. |
> | Action | Microsoft. SQL/servery/elasticPools/Advisors/recommendedActions/Read | Vrátí seznam doporučených akcí zadaného Poradce pro elastický fond. |
> | Action | Microsoft. SQL/servery/elasticPools/Advisory/recommendedActions/Write | Použít doporučenou akci pro elastický fond |
> | Action | Microsoft. SQL/servery/elasticPools/Advisors/Write | Aktualizuje stav automatického spuštění služby Advisor na úrovni elastického fondu. |
> | Action | Microsoft. SQL/servery/elasticPools/databáze/čtení | Načte seznam databází pro elastický fond. |
> | Action | Microsoft. SQL/servery/elasticPools/DELETE | Odstranit existující elastický fond |
> | Action | Microsoft. SQL/servery/elasticPools/elasticPoolActivity/Read | Načte aktivity a podrobnosti v daném fondu elastické databáze. |
> | Action | Microsoft. SQL/servery/elasticPools/elasticPoolDatabaseActivity/Read | Načte aktivity a podrobnosti v dané databázi, která je součástí fondu elastické databáze. |
> | Action | Microsoft. SQL/servery/elasticPools/převzetí služeb při selhání/akce | Převzetí služeb při selhání elastického fondu iniciované zákazníkem |
> | Action | Microsoft. SQL/servery/elasticPools/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro fondy elastické databáze |
> | Action | Microsoft. SQL/servery/elasticPools/metriky/číst | Návratové metriky pro fondy elastické databáze |
> | Action | Microsoft. SQL/servery/elasticPools/Operations/Cancel/Action | Zruší nedokončenou asynchronní operaci pro elastický fond Azure SQL, který ještě není hotový. |
> | Action | Microsoft. SQL/servery/elasticPools/Operations/Read | Vrátí seznam operací provedených v elastickém fondu. |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek. |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek. |
> | Action | Microsoft. SQL/servery/elasticPools/Providers/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro fondy elastické databáze |
> | Action | Microsoft. SQL/servery/elasticPools/Read | Načte podrobnosti elastického fondu na daném serveru. |
> | Action | Microsoft. SQL/servery/elasticPools/SKU/Read | Získá kolekci SKU, která je k dispozici pro elastický fond. |
> | Action | Microsoft. SQL/servery/elasticPools/Write | Vytvoří nové nebo změnit vlastnosti existujícího elastického fondu. |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | Vrátí seznam chráněných šifrovacích serverů nebo získá vlastnosti pro zadané ochrany šifrování serveru. |
> | Action | Microsoft. SQL/servery/encryptionProtector/Write | Aktualizuje vlastnosti pro zadanou ochranu pomocí šifrování serveru. |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | Načíst podrobnosti o zásadách auditování rozšířených objektů BLOB serveru nakonfigurovaných na daném serveru |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | Změna auditování rozšířeného objektu BLOB serveru pro daný server |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | Odstraní existující skupinu převzetí služeb při selhání. |
> | Action | Microsoft. SQL/servery/failoverGroups/převzetí služeb při selhání/akce | Provede plánované převzetí služeb při selhání v existující skupině převzetí služeb při selhání. |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Spustí vynucené převzetí služeb při selhání v existující skupině převzetí služeb při selhání |
> | Action | Microsoft.Sql/servers/failoverGroups/read | Vrátí seznam skupin převzetí služeb při selhání nebo získá vlastnosti zadané skupiny převzetí služeb při selhání. |
> | Action | Microsoft.Sql/servers/failoverGroups/write | Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadanou skupinu převzetí služeb při selhání. |
> | Action | Microsoft.Sql/servers/firewallRules/delete | Odstraní existující pravidlo brány firewall serveru. |
> | Action | Microsoft. SQL/servery/firewallRules/Read | Vrátí seznam pravidel brány firewall serveru nebo získá vlastnosti zadaného pravidla brány firewall serveru. |
> | Action | Microsoft. SQL/servery/firewallRules/Write | Vytvoří pravidlo brány firewall serveru se zadanými parametry, aktualizuje vlastnosti zadaného pravidla nebo přepíše všechna stávající pravidla novými pravidly brány firewall serveru. |
> | Action | Microsoft. SQL/servery/import/akce | Vytvoření nové databáze na serveru a nasazení schématu a dat z balíčku DacPac |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | Načte probíhající operace importu a exportu. |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Odstraní zadaný profil koncového bodu rozhraní. |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Vrátí vlastnosti pro zadaný profil koncového bodu rozhraní. |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Vytvoří profil koncového bodu rozhraní se zadanými parametry nebo aktualizuje vlastnosti nebo značky zadaného koncového bodu rozhraní. |
> | Action | Microsoft. SQL/servery/jobAgents/DELETE | Odstraní agenta úloh služby Azure SQL DB. |
> | Action | Microsoft. SQL/servery/jobAgents/Read | Načte agenta úloh služby Azure SQL DB. |
> | Action | Microsoft. SQL/servery/jobAgents/Write | Vytvoří nebo aktualizuje agenta úloh služby Azure SQL DB. |
> | Action | Microsoft.Sql/servers/keys/delete | Odstraní existující klíč serveru. |
> | Action | Microsoft. SQL/servery/klíče/číst | Vrátí seznam klíčů serveru nebo získá vlastnosti pro zadaný klíč serveru. |
> | Action | Microsoft. SQL/servery/klíče/zapisovat | Vytvoří klíč se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný klíč serveru. |
> | Action | Microsoft.Sql/servers/operationResults/read | Načte probíhající operace serveru. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Odstraní existující proxy připojení privátního koncového bodu. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Vrátí seznam proxy serverů připojení privátního koncového bodu nebo získá vlastnosti zadaného proxy serveru připojení privátního koncového bodu. |
> | Action | Microsoft. SQL/servery/privateEndpointConnectionProxies/ověřit/akce | Ověří volání vytvoření připojení privátního koncového bodu z NRP strany. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Vytvoří proxy server připojení privátního koncového bodu se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný proxy privátního koncového bodu. |
> | Action | Microsoft. SQL/servery/privateEndpointConnections/DELETE | Odstraní existující připojení privátního koncového bodu. |
> | Action | Microsoft. SQL/servery/privateEndpointConnections/Read | Vrátí seznam připojení privátního koncového bodu nebo získá vlastnosti pro zadané připojení privátního koncového bodu. |
> | Action | Microsoft. SQL/servery/privateEndpointConnections/Write | Schválí nebo odmítne existující připojení privátního koncového bodu. |
> | Action | Microsoft. SQL/servery/privateEndpointConnectionsApproval/Action | Určuje, jestli má uživatel povolený souhlas s připojením privátního koncového bodu. |
> | Action | Microsoft.Sql/servers/privateLinkResources/read | Získat prostředky privátního propojení pro odpovídající SQL Server |
> | Action | Microsoft. SQL/servery/zprostředkovatelé/Microsoft. Insights/metricDefinitions/Read | Návratové typy metrik, které jsou k dispozici pro servery |
> | Action | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Načtení metrik pro doporučené fondy elastické databáze pro daný server |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | Načtěte doporučení pro fondy elastické databáze, abyste snížili náklady nebo vylepšili výkon na základě historických využití prostředků. |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | Tato operace se používá pro zotavení po havárii živé databáze k obnovení databáze do posledního známého funkčního bodu zálohování. Vrátí informace o poslední dobré záloze, ale doesn\u0027t ve skutečnosti obnoví databázi. |
> | Action | Microsoft. SQL/servery/replicationLinks/Read | Vrátí seznam replikačních propojení nebo získá vlastnosti pro zadané replikační odkazy. |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | Získat seznam databází, které byly na daném serveru vyřazeny, které jsou stále v rámci zásad uchovávání informací. |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Načíst výsledky operace zápisu zásad detekce hrozeb serveru |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/read | Načte seznam zásad detekce hrozeb serveru nakonfigurovaných pro daný server. |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/write | Změna zásad detekce hrozeb serveru pro daný server |
> | Action | Microsoft. SQL/servery/serviceObjectives/Read | Načte seznam cílů na úrovni služby (označovaných také jako úrovně výkonu), které jsou k dispozici na daném serveru. |
> | Action | Microsoft.Sql/servers/syncAgents/delete | Odstraní stávajícího agenta synchronizace. |
> | Action | Microsoft. SQL/servery/syncAgents/generateKey/Action | Generovat registrační klíč agenta synchronizace |
> | Action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Vrátí seznam propojených databází agenta synchronizace. |
> | Action | Microsoft. SQL/servery/syncAgents/Read | Vrátí seznam agentů synchronizace nebo získá vlastnosti zadaného agenta synchronizace. |
> | Action | Microsoft. SQL/servery/syncAgents/Write | Vytvoří agenta synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadaného agenta synchronizace. |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | Vytvořit nebo aktualizovat certifikát TDE |
> | Action | Microsoft. SQL/servery/použití/číst | Vrátí kvótu DTU serveru a aktuální consuption DTU všemi databázemi v rámci serveru. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | Odstraní existující pravidlo Virtual Network. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | Vrátí seznam pravidel virtuální sítě nebo získá vlastnosti zadaného pravidla virtuální sítě. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadané pravidlo virtuální sítě. |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Odebrání posouzení ohrožení zabezpečení pro daný server |
> | Action | Microsoft. SQL/servery/vulnerabilityAssessments/Read | Načtěte zásady posouzení ohrožení zabezpečení na daném serveru. |
> | Action | Microsoft. SQL/servery/vulnerabilityAssessments/Write | Změna posouzení ohrožení zabezpečení pro daný server |
> | Action | Microsoft. SQL/servery/Write | Vytvoří server se zadanými parametry nebo aktualizuje vlastnosti nebo značky pro zadaný server. |
> | Action | Microsoft.Sql/unregister/action | Zruší registraci předplatného pro poskytovatele prostředků Microsoft SQL Database a povolí vytváření databází Microsoft SQL. |
> | Action | Microsoft.Sql/virtualClusters/delete | Odstraní existující virtuální cluster. |
> | Action | Microsoft. SQL/virtualClusters/Read | Vrátí seznam virtuálních clusterů nebo získá vlastnosti pro zadaný virtuální cluster. |
> | Action | Microsoft. SQL/virtualClusters/Write | Aktualizuje značky virtuálního clusteru. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Storage/checknameavailability/Read | Kontroluje, že název účtu je platný a nepoužívá se. |
> | Action | Microsoft. Storage/Locations/checknameavailability/Read | Kontroluje, že název účtu je platný a nepoužívá se. |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Oznamuje Microsoft. Storage, že se odstraňuje virtuální síť nebo podsíť. |
> | Action | Microsoft. Storage/umístění/využití/čtení | Vrátí limit a aktuální počet použití pro prostředky v zadaném předplatném. |
> | Action | Microsoft. Storage/Operations/Read | Cyklické dotazování na stav asynchronní operace. |
> | Action | Microsoft. Storage/registr/Action | Zaregistruje předplatné pro poskytovatele prostředků úložiště a povolí vytváření účtů úložiště. |
> | Action | Microsoft. Storage/SKU/čtení | Zobrazuje seznam SKU podporovaný Microsoft. Storage. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Add/Action | Vrátí výsledek přidání obsahu objektu BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/DELETE | Vrátí výsledek odstranění objektu BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/deleteAutomaticSnapshot/Action | Vrátí výsledek odstranění automatického snímku. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Filter/Action | Vrátí seznam objektů BLOB v rámci účtu se shodným filtrem značek. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Read | Vrátí výsledek čtení značek objektů BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Tags/Write | Vrátí výsledek zápisu značek objektů BLOB. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Write | Vrátí výsledek zápisu objektu BLOB. |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Vymazat právní blokování kontejneru objektů BLOB |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Vrátí výsledek odstranění kontejneru. |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Odstranit zásady neměnnosti kontejneru objektů BLOB |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Extended/Action | Rozšíří zásady neměnnosti kontejneru objektů BLOB. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Lock/Action | Zamknout zásady neměnnosti kontejneru objektů BLOB |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/immutabilityPolicies/Read | Získat zásady neměnnosti kontejneru objektů BLOB |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Vložit zásady neměnnosti kontejneru objektů BLOB |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/Lease/Action | Vrátí výsledek zapůjčení kontejneru objektů BLOB. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí kontejner. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí seznam kontejnerů. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/setLegalHold/Action | Nastavit právní blokování kontejneru objektů BLOB |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/Write | Vrátí výsledek opravy kontejneru objektů BLOB. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Containers/Write | Vrátí výsledek Put kontejneru objektů BLOB. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro službu BLOB Service. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Read |  |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Read | Vrátí vlastnosti nebo statistiky služby BLOB Service. |
> | Action | Microsoft. Storage/storageAccounts/blobServices/Write | Vrátí výsledek vložení vlastností služby BLOB Service. |
> | Action | Microsoft.Storage/storageAccounts/delete | Odstraní existující účet úložiště. |
> | Action | Microsoft. Storage/storageAccounts/převzetí služeb při selhání/akce | Zákazník dokáže řídit převzetí služeb při selhání v případě problémů s dostupností. |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read | Získat vlastnosti souborové služby |
> | Action | Microsoft. Storage/storageAccounts/listAccountSas/Action | Vrátí token SAS účtu pro zadaný účet úložiště. |
> | Action | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Action | Microsoft. Storage/storageAccounts/listServiceSas/Action | Vrátí token SAS služby pro zadaný účet úložiště. |
> | Action | Microsoft. Storage/storageAccounts/managementPolicies/DELETE | Odstranit zásady správy účtu úložiště |
> | Action | Microsoft. Storage/storageAccounts/managementPolicies/Read | Získání zásad účtu správy úložiště |
> | Action | Microsoft. Storage/storageAccounts/managementPolicies/Write | Vložit zásady správy účtu úložiště |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Odstranit proxy připojení privátního koncového bodu |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Vložit proxy privátního koncového bodu připojení |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnections/DELETE | Odstranit připojení privátního koncového bodu |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnections/Read | Získat připojení privátního koncového bodu |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnections/Write | Vložit připojení privátního koncového bodu |
> | Action | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Schválit připojení privátního koncového bodu |
> | Action | Microsoft.Storage/storageAccounts/privateLinkResources/read | Získat StorageAccount groupids |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Vrátí výsledek odstranění fronty. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Add/Action | Vrátí výsledek přidávání zprávy. |
> | Akce dataaction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Vrátí výsledek odstranění zprávy. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Process/Action | Vrátí výsledek zpracování zprávy. |
> | Akce dataaction | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Read | Vrátí zprávu. |
> | Akce dataaction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Vrátí výsledek zápisu zprávy. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | Vrátí výsledek zápisu fronty. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Získat vlastnosti Služba front |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Vrátí vlastnosti nebo statistiky služby Queue. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | Vrátí výsledek nastavení vlastností služby Queue. |
> | Action | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Action | Microsoft. Storage/storageAccounts/RegenerateKey/Action | Znovu vygeneruje přístupové klíče pro zadaný účet úložiště. |
> | Action | Microsoft. Storage/storageAccounts/restoreBlobRanges/Action | Obnovte rozsahy objektů blob do stavu určeného časem. |
> | Action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odvolá všechny klíče delegování uživatelů pro zadaný účet úložiště. |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky účtu úložiště. |
> | Action | Microsoft.Storage/storageAccounts/tableServices/read | Získat vlastnosti Table service |
> | Action | Microsoft.Storage/storageAccounts/write | Vytvoří účet úložiště se zadanými parametry, aktualizuje vlastnosti nebo značky nebo přidá vlastní doménu pro zadaný účet úložiště. |
> | Action | Microsoft. Storage/použití/čtení | Vrátí limit a aktuální počet použití pro prostředky v zadaném předplatném. |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. storagesync/Locations/checkNameAvailability/Action | Kontroluje, jestli je název služby synchronizace úložiště platný a nepoužívá se. |
> | Action | microsoft.storagesync/locations/workflows/operations/read | Získá stav asynchronní operace. |
> | Action | Microsoft. storagesync/Operations/Read | Načte seznam podporovaných operací. |
> | Action | Microsoft. storagesync/Register/Action | Zaregistruje předplatné pro zprostředkovatele synchronizace úložiště. |
> | Action | microsoft.storagesync/storageSyncServices/delete | Odstranit všechny služby synchronizace úložiště |
> | Action | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro služby synchronizace úložiště. |
> | Action | microsoft.storagesync/storageSyncServices/read | Přečtěte si všechny služby synchronizace úložiště |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/delete | Odstranit jakýkoli registrovaný Server |
> | Action | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro registrovaný Server. |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | Číst libovolný registrovaný Server |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/write | Vytvořit nebo aktualizovat jakýkoli registrovaný Server |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Odstranit všechny koncové body cloudu |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Získá stav asynchronní operace zálohování a obnovení. |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Zavolat tuto akci po zálohování |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Zavolat tuto akci po obnovení |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Před zálohováním volejte tuto akci |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Před obnovením volejte tuto akci |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Čtení všech koncových bodů cloudu |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Obnovit prezenční signál |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Vytvoří nebo aktualizuje všechny koncové body cloudu. |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/delete | Odstranění všech skupin synchronizace |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro skupiny synchronizace. |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | Čtení všech skupin synchronizace |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Odstranění libovolných koncových bodů serveru |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro koncové body serveru. |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Čtení libovolných koncových bodů serveru |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Zavolejte tuto akci, pokud chcete soubory znovu navrátit na server. |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Vytvořit nebo aktualizovat libovolné koncové body serveru |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/write | Vytvoření nebo aktualizace skupin synchronizace |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Získá stav asynchronní operace. |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operations/read | Získá stav asynchronní operace. |
> | Action | microsoft.storagesync/storageSyncServices/workflows/read | Čtení pracovních postupů |
> | Action | microsoft.storagesync/storageSyncServices/write | Vytvořit nebo aktualizovat všechny služby synchronizace úložiště |
> | Action | Microsoft. storagesync/zrušit registraci/akce | Zruší registraci předplatného pro zprostředkovatele synchronizace úložiště. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. StorSimple/manažeři/accessControlRecords/DELETE | Odstraní záznamy Access Control. |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/accessControlRecords/Read | Zobrazí nebo získá záznamy Access Control. |
> | Action | Microsoft. StorSimple/manažeři/accessControlRecords/Write | Vytvoření nebo aktualizace záznamů Access Control |
> | Action | Microsoft. StorSimple/manažeři/výstrahy/číst | Zobrazí nebo získá výstrahy. |
> | Action | Microsoft. StorSimple/manažeři/zálohy/číst | Zobrazí nebo získá zálohovací sklad. |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/delete | Odstraní existující nastavení šířky pásma (jenom řady 8000). |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Vypíše výsledky operace. |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/read | Zobrazit seznam nastavení šířky pásma (jenom řada 8000) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/write | Vytvoří nové nebo aktualizuje nastavení šířky pásma (jenom série 8000). |
> | Action | Microsoft. StorSimple/manažeři/certifikáty/zapisovat | Vytvoření nebo aktualizace certifikátů |
> | Action | Microsoft.StorSimple/Managers/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | Action | Microsoft. StorSimple/manažeři/clearAlerts/Action | Vymažte všechny výstrahy spojené se správcem zařízení. |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Seznam podporovaných konfigurací cloudového zařízení |
> | Action | Microsoft. StorSimple/manažeři/configureDevice/Action | Nakonfiguruje zařízení. |
> | Action | Microsoft. StorSimple/manažeři/odstranit | Odstraní Správce zařízení. |
> | Action | Microsoft.StorSimple/Managers/delete | Operace odstranění trezoru odstraní zadaný prostředek Azure typu trezor. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/alertSettings/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/alertSettings/číst | Zobrazí nebo získá nastavení upozornění. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/alertSettings/Write | Vytvořit nebo aktualizovat nastavení výstrah |
> | Action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizovat pro výměnu šifrovacího klíče služby na zařízeních |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/záloha/akce | Pokud chcete vytvořit zálohu na vyžádání všech svazků chráněných zásadou, proveďte ruční zálohování. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/DELETE | Odstraní existující zásady zálohování (jenom řada 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/operationResults/číst | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/číst | Seznam zásad zálohování (jenom řada 8000) |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/Schedules/DELETE | Odstraní existující plány. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/plány/čtení | Výpis plánů |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Vytvoří nové nebo aktualizované plány. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/backupPolicies/Write | Vytvoří nové nebo aktualizuje zásady zálohování (jenom řada 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zálohy/odstranit | Odstraní zálohovací sklad. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zálohy/prvky/klonování/akce | Naklonujte sdílenou složku nebo svazek pomocí elementu zálohování. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zálohy/elementy/operationResults/Read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zálohy/operationResults/Read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zálohy/číst | Zobrazí nebo získá zálohovací sklad. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zálohy/obnovení/akce | Obnovte všechny svazky ze zálohovacího skladu. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Odstraní skupiny plánu zálohování. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Zobrazí nebo získá skupiny plánu zálohování. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Vytvoření nebo aktualizace skupin plánu zálohování |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | Odstraní nastavení protokolu CHAP. |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | Zobrazí nebo získá nastavení protokolu CHAP. |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | Vytvořit nebo aktualizovat nastavení protokolu CHAP |
> | Action | Microsoft. StorSimple/manažeři/zařízení/deaktivovat/akce | Deaktivuje zařízení. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/odstranit | Odstraní zařízení. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/disky/číst | Vypíše nebo získá disky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/stáhnout/akce | Stáhnout aktualizace zařízení. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/převzetí služeb při selhání/akce | Převzetí služeb při selhání zařízení. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/převzetí služeb při selhání/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/failoverTargets/číst | Vypíše nebo získá cíle převzetí služeb při selhání zařízení. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/zálohy/akce | Vybere zálohu souborového serveru. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/odstranit | Odstraní souborové servery. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/a metriky/číst | Vypíše nebo získá metriky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/servery/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/servery/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/servery/číst | Zobrazí nebo získá souborové servery. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené složky/odstranit | Odstraní sdílené složky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/akcie/metriky/čtení | Vypíše nebo získá metriky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/číst | Zobrazí nebo získá sdílené složky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/soubory/sdílené položky/zapisovat | Vytvořit nebo aktualizovat sdílené složky |
> | Action | Microsoft. StorSimple/manažeři/zařízení/servery/zápisy/zapisovat | Vytvořit nebo aktualizovat souborové servery |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Změna stavu napájení řadiče pro skupiny hardwarových součástí |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Vypíše výsledky operace. |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Seznam skupin hardwarových součástí |
> | Action | Microsoft. StorSimple/manažeři/zařízení/instalace/akce | Nainstalujte do zařízení aktualizace. |
> | Action | Microsoft.StorSimple/managers/devices/installUpdates/action | Nainstaluje aktualizace na zařízení (jenom řada 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/záloha/akce | Pořídit zálohu serveru iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Odstraní servery iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Odstraní disky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/disky/metriky/čtení | Vypíše nebo získá metriky. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Zobrazí nebo získá definice metrik. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/disky/číst | Vypíše nebo získá disky. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Vytvořit nebo aktualizovat disky |
> | Action | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/metriky/číst | Vypíše nebo získá metriky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/iscsiservers/číst | Vypíše nebo získá servery iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/write | Vytvoření nebo aktualizace serverů iSCSI |
> | Action | Microsoft. StorSimple/manažeři/zařízení/úlohy/zrušit/akce | Zrušení spuštěné úlohy |
> | Action | Microsoft. StorSimple/manažeři/zařízení/úlohy/operationResults/číst | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/úlohy/číst | Zobrazí nebo získá úlohy. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/listFailoverSets/Action | Vypíše sady převzetí služeb při selhání pro existující zařízení (jenom řada 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/listFailoverTargets/Action | Vypíše cíle převzetí služeb při selhání zařízení (jenom řada 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/metriky/číst | Vypíše nebo získá metriky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/metricsDefinitions/číst | Zobrazí nebo získá definice metrik. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/confirmMigration/Action | Potvrdí úspěšnou migraci a potvrdí ji. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/confirmMigrationStatus/číst | Seznam potvrzení stavu migrace |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/fetchConfirmMigrationStatus/Action | Načtěte stav potvrzení migrace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/fetchMigrationEstimate/Action | Načtěte stav úlohy odhadu migrace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/fetchMigrationStatus/Action | Načtěte stav migrace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/import/akce | Importovat konfigurace zdrojů pro migraci |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/migrationEstimate/číst | Výpis odhadu migrace |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/migrationStatus/číst | Výpis stavu migrace |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/operationResults/číst | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/startMigration/Action | Spustit migraci pomocí konfigurací zdrojů |
> | Action | Microsoft. StorSimple/manažeři/zařízení/migrationSourceConfigurations/startMigrationEstimate/Action | Spusťte úlohu pro odhad doby trvání procesu migrace. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Vypíše výsledky operace. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | Zobrazí nebo získá nastavení sítě. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | Vytvoří nové nebo aktualizuje nastavení sítě. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/operationResults/číst | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/publicEncryptionKey/Action | Vypsat veřejný šifrovací klíč Správce zařízení |
> | Action | Microsoft. StorSimple/manažeři/zařízení/publishSupportPackage/Action | Publikování balíčku pro podporu pro existující zařízení. Balíček podpory StorSimple je snadno použitelný mechanismus, který shromažďuje všechny relevantní protokoly, které vám pomůžou podpora Microsoftu s řešením potíží s problémy s StorSimple zařízeními. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/číst | Zobrazí nebo získá zařízení. |
> | Action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Vyhledat aktualizace v zařízení. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/securitySettings/číst | Zobrazit seznam nastavení zabezpečení |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizuje certifikát vzdálené správy pro zařízení. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aktualizujte nastavení zabezpečení. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | Vytvoří nové nebo aktualizuje nastavení zabezpečení. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/sendTestAlertEmail/Action | Odešlete e-mailem upozornění testu nakonfigurovaným příjemcům e-mailu |
> | Action | Microsoft. StorSimple/manažeři/zařízení/sdílet/číst | Zobrazí nebo získá sdílené složky. |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/timeSettings/číst | Zobrazí nebo získá nastavení času. |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/write | Vytvoří nové nebo aktualizace nastavení času. |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft.StorSimple/managers/devices/updateSummary/read | Zobrazí nebo získá souhrn aktualizace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/DELETE | Odstraní existující kontejnery svazků (jenom řada 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/metriky/číst | Vypsat metriky |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/metricsDefinitions/číst | Seznam definic metrik |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/operationResults/číst | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/číst | Seznam kontejnerů svazků (jenom řada 8000) |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/odstranit | Odstraní existující svazky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/metriky/číst | Vypsat metriky |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/metricsDefinitions/Read | Seznam definic metrik |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/operationResults/Read | Vypíše výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/číst | Vypsat svazky |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/svazky/zapisovat | Vytvoří nové nebo aktualizuje svazky. |
> | Action | Microsoft. StorSimple/manažeři/zařízení/volumeContainers/Write | Vytvoří nové nebo aktualizuje kontejnery svazků (jenom série 8000). |
> | Action | Microsoft. StorSimple/manažeři/zařízení/svazky/číst | Vypsat svazky |
> | Action | Microsoft. StorSimple/manažeři/zařízení/zapisovat | Vytvoření nebo aktualizace zařízení |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | Zobrazí nebo získá nastavení šifrování. |
> | Action | Microsoft.StorSimple/managers/extendedInformation/delete | Odstraní rozšířené informace o trezoru. |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft.StorSimple/managers/extendedInformation/read | Zobrazí nebo získá informace o rozšířeném trezoru. |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft.StorSimple/managers/extendedInformation/write | Vytvoří nebo aktualizuje rozšířené informace o trezoru. |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft. StorSimple/manažeři/funkce/číst | Seznam funkcí |
> | Action | Microsoft. StorSimple/manažeři/servery/číst | Zobrazí nebo získá souborové servery. |
> | Action | Microsoft. StorSimple/manažeři/getEncryptionKey/Action | Získejte šifrovací klíč pro správce zařízení. |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | Vypíše nebo získá servery iSCSI. |
> | Action | Microsoft. StorSimple/manažeři/úlohy/číst | Zobrazí nebo získá úlohy. |
> | Action | Microsoft. StorSimple/manažeři/listActivationKey/Action | Získá aktivační klíč StorSimple Device Manager. |
> | Action | Microsoft. StorSimple/manažeři/listPublicEncryptionKey/Action | Vypíše veřejné šifrovací klíče StorSimple Device Manager. |
> | Action | Microsoft. StorSimple/manažeři/metriky/číst | Vypíše nebo získá metriky. |
> | Action | Microsoft. StorSimple/manažeři/metricsDefinitions/Read | Zobrazí nebo získá definice metrik. |
> | Action | Microsoft. StorSimple/manažeři/migrateClassicToResourceManager/Action | Migrace z modelu Classic na Správce prostředků ze všech správců |
> | Action | Microsoft. StorSimple/manažeři/migrationSourceConfigurations/Read | Seznam konfigurací zdrojů migrace (jenom řada 8000) |
> | Action | Microsoft.StorSimple/managers/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Vytvořte nové cloudové zařízení. |
> | Action | Microsoft. StorSimple/manažeři/číst | Zobrazí nebo získá Správce zařízení. |
> | Action | Microsoft.StorSimple/Managers/read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Action | Microsoft. StorSimple/manažeři/regenerateActivationKey/Action | Znovu vygenerujte aktivační klíč pro existující Device Manager StorSimple. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Odstraní přihlašovací údaje účtu úložiště. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | Zobrazí nebo získá přihlašovací údaje účtu úložiště. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | Vytvořit nebo aktualizovat přihlašovací údaje účtu úložiště |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | Odstraní domény úložiště. |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Zobrazí nebo získá výsledky operace. |
> | Action | Microsoft. StorSimple/manažeři/storageDomains/Read | Vypíše nebo získá domény úložiště. |
> | Action | Microsoft. StorSimple/manažeři/storageDomains/Write | Vytvoření nebo aktualizace domén úložiště |
> | Action | Microsoft. StorSimple/manažeři/zapisovat | Vytvořit nebo aktualizovat Správce zařízení |
> | Action | Microsoft.StorSimple/Managers/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Action | Microsoft.StorSimple/operations/read | Zobrazí nebo získá operace. |
> | Action | Microsoft.StorSimple/register/action | Registrovat poskytovatele Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. StreamAnalytics/umístění/kvóty/číst | Kvóta pro čtení Stream Analytics předplatného |
> | Action | Microsoft. StreamAnalytics/Operations/Read | Čtení Stream Analyticsch operací |
> | Action | Microsoft. StreamAnalytics/Register/Action | Registrovat předplatné u Stream Analyticsho poskytovatele prostředků |
> | Action | Microsoft. StreamAnalytics/streamingjobs/DELETE | Odstranit úlohu Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/DELETE | Odstranit funkci úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Načíst výsledky operace pro funkci Stream Analytics úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Funkce pro čtení Stream Analytics úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Načtení výchozí definice funkce úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/test/Action | Funkce úlohy testovacího Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Funkce úlohy Write Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/DELETE | Odstranění vstupu úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/operationresults/Read | Načíst výsledky operace pro vstup Stream Analytics úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/Read | Číst Stream Analytics vstup úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/Sample/Action | Ukázka Stream Analyticsho vstupu úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Testovací Stream Analytics – vstup úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/Write | Zápis Stream Analytics vstup úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Čtení definic metriky |
> | Action | Microsoft. StreamAnalytics/streamingjobs/operationresults/Read | Načtení výsledků operace Stream Analytics úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/výstupy/odstranit | Odstranit výstup úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/výstupy/operationresults/Read | Načíst výsledky operace pro výstup úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/výstupy/čtení | Načíst výstup úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/výstupy/test/Action | Výstup úlohy testovacího Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/výstupy/Write | Zápis výstupu úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Read | Přečte nastavení diagnostiky. |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/diagnosticSettings/Write | Zapsat nastavení diagnostiky. |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/logDefinitions/Read | Získá dostupné protokoly pro streamingjobs. |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. Insights/metricDefinitions/Read | Získá dostupné metriky pro streamingjobs. |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Read | Načtení Stream Analytics úlohy |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Start/Action | Spustit úlohu Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/stop/Action | Zastavit úlohu Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/transformes/DELETE | Odstranění transformace úlohy Stream Analytics |
> | Action | Microsoft. StreamAnalytics/streamingjobs/transformes/Read | Transformace úlohy Stream Analytics čtení |
> | Action | Microsoft. StreamAnalytics/streamingjobs/transformes/Write | Transformace úlohy Stream Analytics zápisu |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Write | Stream Analytics úloha zápisu |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. Subscription/Cancel/Action | Zruší předplatné. |
> | Action | Microsoft. Subscription/CreateSubscription/Action | Vytvořit předplatné Azure |
> | Action | Microsoft. Subscription/registr/Action | Zaregistruje předplatné u poskytovatele prostředků Microsoft. Subscription. |
> | Action | Microsoft. Subscription/přejmenovat/Action | Přejmenuje předplatné. |
> | Action | Microsoft. Subscription/SubscriptionDefinitions/Read | Získání definice předplatného Azure v rámci skupiny pro správu |
> | Action | Microsoft. Subscription/SubscriptionDefinitions/Write | Vytvoření definice předplatného Azure |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.Support/register/action | Registruje poskytovatele prostředků podpory. |
> | Action | Microsoft.Support/supportTickets/read | Umožňuje získat podrobnosti lístku podpory (včetně stavu, závažnosti, podrobností kontaktu a komunikací) nebo seznam lístků podpory pro všechna předplatná. |
> | Action | Microsoft.Support/supportTickets/write | Umožňuje vytvořit nebo aktualizovat lístek podpory. Můžete vytvořit lístek podpory pro technické problémy, problémy s fakturací nebo kvótami nebo problémy související se správou předplatného. U stávajících lístků podpory můžete aktualizovat závažnost, podrobnosti kontaktu a komunikace. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. TimeSeriesInsights/Environments/accesspolicies/DELETE | Odstraní zásady přístupu. |
> | Action | Microsoft. TimeSeriesInsights/Environment/accesspolicies/Read | Získá vlastnosti zásady přístupu. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Vytvoří novou zásadu přístupu pro prostředí nebo aktualizuje existující zásady přístupu. |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | Odstraní prostředí. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Odstraní zdroj události. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | Získá vlastnosti zdroje události. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/write | Vytvoří nový zdroj události pro prostředí nebo aktualizuje existující zdroj události. |
> | Action | Microsoft.TimeSeriesInsights/environments/read | Získá vlastnosti prostředí. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Odstraní referenční datovou sadu. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Získá vlastnosti referenční sady dat. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Vytvoří novou referenční sadu dat pro prostředí nebo aktualizuje existující referenční datovou sadu. |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | Získejte stav prostředí, stav svých přidružených operací, jako je příchozí přenos dat. |
> | Action | Microsoft.TimeSeriesInsights/environments/write | Vytvoří nové prostředí nebo aktualizuje existující prostředí. |
> | Action | Microsoft.TimeSeriesInsights/register/action | Zaregistruje předplatné pro poskytovatele prostředků Time Series Insights a povolí vytváření Time Series Insights prostředí. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | Odstranit účet |
> | Action | Microsoft. VisualStudio/účet/rozšíření/čtení | Čtení účtu nebo rozšíření |
> | Action | Microsoft.VisualStudio/Account/Project/Read | Čtení účtu nebo projektu |
> | Action | Microsoft. VisualStudio/Account/Project/Write | Nastavit účet/projekt |
> | Action | Microsoft.VisualStudio/Account/Read | Číst účet |
> | Action | Microsoft.VisualStudio/Account/Write | Nastavit účet |
> | Action | Microsoft. VisualStudio/Extension/DELETE | Odstranit rozšíření |
> | Action | Microsoft. VisualStudio/rozšíření/čtení | Načíst rozšíření |
> | Action | Microsoft. VisualStudio/rozšíření/Write | Nastavit rozšíření |
> | Action | Microsoft.VisualStudio/Project/Delete | Odstranit projekt |
> | Action | Microsoft.VisualStudio/Project/Read | Přečíst projekt |
> | Action | Microsoft.VisualStudio/Project/Write | Nastavit projekt |
> | Action | Microsoft. VisualStudio/Register/Action | Registrace předplatného Azure u poskytovatele Microsoft. VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | Získejte účty API Management Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Odstraňte rozhraní API pro účty API Management Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | Získejte rozhraní API pro účty API Management Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | Aktualizujte účty API Management API Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Získejte rozhraní API pro účty API Management Connectionacls. |
> | Action | Microsoft. Web/apimanagementaccounts/API/Connections/confirmconsentcode/Action | Potvrďte souhlasná rozhraní API Code Management Accounts Connections. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Odstraní účty API managementu rozhraní API připojení Connectionacls. |
> | Action | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/Read | Získat účty API managementu připojení rozhraní API Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Aktualizujte účty API managementu. připojení rozhraní API Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | Odstraňte připojení rozhraní API pro účty API Management. |
> | Action | Microsoft. Web/apimanagementaccounts/API/Connections/getconsentlinks/Action | Získejte odkazy na souhlas pro připojení rozhraní API pro účty služby API Management. |
> | Action | Microsoft. Web/apimanagementaccounts/API/Connections/listconnectionkeys/Action | Vypíše seznam klíčů připojení rozhraní API pro účty API pro připojení. |
> | Action | Microsoft. Web/apimanagementaccounts/API/Connections/listsecrets/Action | Vypíše účty rozhraní API pro správu tajných kódů. |
> | Action | Microsoft. Web/apimanagementaccounts/API/Connections/Read | Získejte připojení rozhraní API pro účty API Management. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | Aktualizujte připojení rozhraní API pro účty Management API. |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | Odstraňte rozhraní API pro účty API managementu. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Odstraňte lokalizované definice rozhraní API Management Accounts API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Získejte lokalizované definice rozhraní API pro účty Management Management. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Aktualizovat lokalizované definice rozhraní API Management Accounts API |
> | Action | Microsoft. Web/apimanagementaccounts/API/Read | Získejte rozhraní API pro účty API managementu. |
> | Action | microsoft.web/apimanagementaccounts/apis/write | Aktualizujte rozhraní API pro účty API Management. |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | Získejte účty API Management Connectionacls. |
> | Action | Microsoft. Web/availablestacks/Read | Získejte dostupné zásobníky. |
> | Action | Microsoft.Web/certificates/Delete | Odstraní existující certifikát. |
> | Action | Microsoft. Web/certifikáty/číst | Získá seznam certifikátů. |
> | Action | Microsoft. Web/certifikáty/Write | Přidejte nový certifikát nebo aktualizujte stávající. |
> | Action | microsoft.web/checknameavailability/read | Ověřte, zda je název prostředku k dispozici. |
> | Action | microsoft.web/classicmobileservices/read | Získejte klasický Mobile Services. |
> | Action | Microsoft.Web/connectionGateways/Delete | Odstraní bránu připojení. |
> | Action | Microsoft. Web/connectionGateways/JOIN/Action | Připojí se k bráně připojení. |
> | Action | Microsoft.Web/connectionGateways/ListStatus/Action | Zobrazí seznam stavů brány připojení. |
> | Action | Microsoft. Web/connectionGateways/Move/Action | Přesune bránu připojení. |
> | Action | Microsoft. Web/connectionGateways/Read | Získat seznam bran připojení. |
> | Action | Microsoft. Web/connectionGateways/Write | Vytvoří nebo aktualizuje bránu připojení. |
> | Action | Microsoft. Web/Connections/confirmconsentcode/Action | Potvrďte kód souhlasu připojení. |
> | Action | Microsoft.Web/connections/Delete | Odstraní připojení. |
> | Action | Microsoft. Web/Connections/JOIN/Action | Připojí se k připojení. |
> | Action | microsoft.web/connections/listconsentlinks/action | Vypíše odkazy na vyjádření připojení. |
> | Action | Microsoft. Web/Connections/Move/Action | Přesune připojení. |
> | Action | Microsoft. Web/Connections/Read | Získá seznam připojení. |
> | Action | Microsoft. Web/Connections/Write | Vytvoří nebo aktualizuje připojení. |
> | Action | Microsoft.Web/customApis/Delete | Odstraní vlastní rozhraní API. |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrahuje definici rozhraní API z WSDL. |
> | Action | Microsoft. Web/customApis/JOIN/Action | Připojí se k vlastnímu rozhraní API. |
> | Action | Microsoft.Web/customApis/listWsdlInterfaces/Action | Obsahuje rozhraní WSDL pro vlastní rozhraní API. |
> | Action | Microsoft.Web/customApis/Move/Action | Přesune vlastní rozhraní API. |
> | Action | Microsoft. Web/customApis/Read | Získá seznam vlastního rozhraní API. |
> | Action | Microsoft. Web/customApis/Write | Vytvoří nebo aktualizuje vlastní rozhraní API. |
> | Action | Microsoft.Web/deletedSites/Read | Získat vlastnosti odstraněné webové aplikace |
> | Action | Microsoft. Web/deploymentlocations/Read | Získá umístění nasazení. |
> | Action | Microsoft. Web/geografická umístění/čtení | Získá seznam geografických oblastí. |
> | Action | microsoft.web/hostingenvironments/capacities/read | Získejte kapacity hostujících prostředí. |
> | Action | Microsoft.Web/hostingEnvironments/Delete | Odstranit App Service Environment |
> | Action | microsoft.web/hostingenvironments/detectors/read | Získejte detektory hostujících prostředí. |
> | Action | microsoft.web/hostingenvironments/diagnostics/read | Získejte diagnostiku prostředí hostování. |
> | Action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Získá koncové body sítě pro všechny příchozí závislosti. |
> | Action | Microsoft. Web/hostingEnvironments/JOIN/Action | Připojí se k App Service Environment. |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | Získejte definice metrik prostředí hostování. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Získat definice metrik prostředí hostování s více rolemi fondy rolí. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metrics/read | Získejte prostředí hostování metrik fondů s více rolemi. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Získání vlastností fondu front-endu v App Service Environment |
> | Action | Microsoft. Web/hostingenvironments/multirolepools/SKU/Read | Získejte hostující prostředí fondy rolí. |
> | Action | microsoft.web/hostingenvironments/multirolepools/usages/read | Získejte prostředí hostování více fondů rolí. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Vytvoří nový fond front-endu ve App Service Environment nebo aktualizuje existující. |
> | Action | microsoft.web/hostingenvironments/operations/read | Získejte operace hostování prostředí. |
> | Action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Získá koncové body sítě všech odchozích závislostí. |
> | Action | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/Action | Schválit připojení privátního koncového bodu |
> | Action | Microsoft.Web/hostingEnvironments/Read | Získá vlastnosti App Service Environment. |
> | Action | Microsoft. Web/hostingEnvironments/restart/Action | Restartovat všechny počítače v App Service Environment |
> | Action | microsoft.web/hostingenvironments/resume/action | Obnoví hostující prostředí. |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | Získejte prostředí pro hostování App Service plány. |
> | Action | microsoft.web/hostingenvironments/sites/read | Získat Web Apps hostování prostředí. |
> | Action | microsoft.web/hostingenvironments/suspend/action | Pozastavit hostující prostředí. |
> | Action | microsoft.web/hostingenvironments/usages/read | Získejte využití prostředí hostování. |
> | Action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Získejte prostředí hostování Workerpools definice metrik. |
> | Action | Microsoft. Web/hostingenvironments/workerpools/metriky/čtení | Získejte prostředí hostování Workerpools metrikami. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Read | Získání vlastností fondu pracovních procesů ve App Service Environment |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | Získejte hostující prostředí Workerpools SKU. |
> | Action | microsoft.web/hostingenvironments/workerpools/usages/read | Získejte prostředí hostování Workerpools využití. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Write | Vytvoří nový fond pracovních procesů ve App Service Environment nebo aktualizuje stávající. |
> | Action | Microsoft. Web/hostingEnvironments/Write | Vytvořit nový App Service Environment nebo aktualizovat existující |
> | Action | Microsoft. Web/ishostingenvironmentnameavailable/Read | Získat, pokud je k dispozici název hostitelského prostředí. |
> | Action | Microsoft. Web/ishostnameavailable/Read | Ověřte, zda je název hostitele k dispozici. |
> | Action | Microsoft. Web/isusernameavailable/Read | Ověřte, zda je uživatelské jméno k dispozici. |
> | Action | Microsoft.Web/listSitesAssignedToHostName/Read | Získá názvy webů přiřazených k názvu hostitele. |
> | Action | microsoft.web/locations/apioperations/read | Načte operace rozhraní API umístění. |
> | Action | microsoft.web/locations/connectiongatewayinstallations/read | Získejte umístění pro instalaci brány pro připojení. |
> | Action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Oznámení o odstranění virtuální sítě nebo podsítě pro umístění. |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrahujte definici rozhraní API ze WSDL pro umístění. |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | Vypíše rozhraní WSDL pro umístění. |
> | Action | microsoft.web/locations/managedapis/apioperations/read | Získejte umístění spravovaných operací rozhraní API. |
> | Action | Microsoft. Web/Locations/Inspirujte/JOIN/Action | Připojí spravované rozhraní API. |
> | Action | microsoft.web/locations/managedapis/read | Získat umístění spravovaná rozhraní API. |
> | Action | microsoft.web/operations/read | Získat operace. |
> | Action | microsoft.web/publishingusers/read | Získat uživatele pro publikování. |
> | Action | microsoft.web/publishingusers/write | Aktualizujte uživatele publikování. |
> | Action | Microsoft. Web/doporučení/čtení | Získejte seznam doporučení pro předplatná. |
> | Action | microsoft.web/register/action | Zaregistrujte poskytovatele prostředků Microsoft. Web pro předplatné. |
> | Action | microsoft.web/resourcehealthmetadata/read | Získá metadata Resource Health. |
> | Action | microsoft.web/serverfarms/capabilities/read | Získejte možnosti plánů App Service. |
> | Action | Microsoft.Web/serverfarms/Delete | Odstraní existující plán App Service. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | Odstranit nastavení aplikací pro první stranu App Service plánů |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | Získá nastavení aplikací pro první stranu App Service plánů. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/write | Aktualizuje nastavení aplikací pro první stranu App Service plánů. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Odstranění App Service plánuje přenos oborů názvů Hybrid Connection. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Získá App Service naplánuje přenos oborů názvů Hybrid Connection. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Získá App Service přenáší obory názvů Hybrid Connection Web Apps. |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Získá App Service plány plánů hybridního připojení. |
> | Action | microsoft.web/serverfarms/hybridconnectionrelays/read | Získá App Service Relay naplánuje přenos Hybrid Connection. |
> | Action | microsoft.web/serverfarms/metricdefinitions/read | Získá definice metriky pro plány App Service. |
> | Action | microsoft.web/serverfarms/metrics/read | Získejte metriky plánů App Service. |
> | Action | microsoft.web/serverfarms/operationresults/read | Získejte výsledky operace App Service plánů. |
> | Action | Microsoft.Web/serverfarms/Read | Získat vlastnosti v plánu App Service |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | Restartování všech Web Apps v plánu App Service |
> | Action | microsoft.web/serverfarms/sites/read | Získejte Web Apps plány App Service. |
> | Action | microsoft.web/serverfarms/skus/read | Získejte skladové položky plánů App Service. |
> | Action | microsoft.web/serverfarms/usages/read | Získejte využití plánů App Service. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Aktualizace plánů App Service Virtual Network připojení brány. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | Získá plány App Service Virtual Network připojení. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Odstraní plány App Service Virtual Network trasy připojení. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Získá plány App Service Virtual Network trasy připojení. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aktualizujte plány App Service Virtual Network trasy připojení. |
> | Action | microsoft.web/serverfarms/workers/reboot/action | Restartuje App Service plánuje pracovní procesy. |
> | Action | Microsoft.Web/serverfarms/Write | Vytvoří nový plán App Service nebo aktualizuje stávající. |
> | Action | microsoft.web/sites/analyzecustomhostname/read | Analyzujte vlastní název hostitele. |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | Použít konfiguraci slotu webové aplikace z cílového slotu na aktuální webovou aplikaci |
> | Action | Microsoft. Web/weby/zálohování/akce | Vytvoření nové zálohy webové aplikace |
> | Action | microsoft.web/sites/backup/read | Získat zálohu Web Apps. |
> | Action | microsoft.web/sites/backup/write | Aktualizujte Web Apps zálohu. |
> | Action | microsoft.web/sites/backups/action | Zjistí existující zálohu aplikace, kterou je možné obnovit z objektu BLOB ve službě Azure Storage. |
> | Action | microsoft.web/sites/backups/delete | Odstraňte zálohy Web Apps. |
> | Action | microsoft.web/sites/backups/list/action | Vypíše Web Apps zálohy. |
> | Action | Microsoft. Web/weby/zálohy/čtení | Získat vlastnosti zálohování webové aplikace |
> | Action | microsoft.web/sites/backups/restore/action | Obnovte Web Apps zálohy. |
> | Action | microsoft.web/sites/backups/write | Aktualizace záloh Web Apps. |
> | Action | microsoft.web/sites/config/delete | Odstraní konfiguraci Web Apps. |
> | Action | Microsoft. Web/weby/config/list/Action | Vypíše nastavení citlivá na zabezpečení webové aplikace, jako jsou například přihlašovací údaje pro publikování, nastavení aplikace a připojovací řetězce. |
> | Action | Microsoft.Web/sites/config/Read | Získat nastavení konfigurace webové aplikace |
> | Action | microsoft.web/sites/config/snapshots/read | Získat Web Apps konfigurační snímky. |
> | Action | Microsoft.Web/sites/config/Write | Aktualizovat konfigurační nastavení webové aplikace |
> | Action | microsoft.web/sites/containerlogs/action | Získejte protokoly kontejneru zip pro webovou aplikaci. |
> | Action | Microsoft. Web/weby/containerlogs/stáhnout/akce | Stáhnout Web Apps protokoly kontejnerů. |
> | Action | microsoft.web/sites/continuouswebjobs/delete | Odstraňte Web Apps průběžné webové úlohy. |
> | Action | microsoft.web/sites/continuouswebjobs/read | Získejte Web Apps průběžné webové úlohy. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/Start/Action | Spusťte Web Apps průběžné webové úlohy. |
> | Action | microsoft.web/sites/continuouswebjobs/stop/action | Zastavte Web Apps průběžné webové úlohy. |
> | Action | Microsoft.Web/sites/Delete | Odstranění existující webové aplikace |
> | Action | microsoft.web/sites/deployments/delete | Odstraňte Web Apps nasazení. |
> | Action | microsoft.web/sites/deployments/log/read | Získá protokol nasazení Web Apps. |
> | Action | microsoft.web/sites/deployments/read | Získat Web Apps nasazení. |
> | Action | microsoft.web/sites/deployments/write | Aktualizujte nasazení Web Apps. |
> | Action | microsoft.web/sites/detectors/read | Získat Web Apps detektory. |
> | Action | Microsoft. Web/weby/Diagnostika/analýzy/provedení/akce | Spusťte analýzu diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/analyses/read | Získat analýzu diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/aspnetcore/read | Získejte diagnostiku Web Apps pro ASP.NET Core aplikaci. |
> | Action | microsoft.web/sites/diagnostics/autoheal/read | Získejte diagnostiku Web Apps pro autoretušování. |
> | Action | microsoft.web/sites/diagnostics/deployment/read | Získat Web Apps nasazení diagnostiky. |
> | Action | microsoft.web/sites/diagnostics/deployments/read | Získat Web Apps nasazení diagnostiky. |
> | Action | Microsoft. Web/weby/Diagnostika/detektory/provedení/akce | Spusťte detektor diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/detectors/read | Získat Web Apps detektor diagnostiky |
> | Action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Získat neúspěšné požadavky diagnostiky Web Apps na identifikátor URI. |
> | Action | microsoft.web/sites/diagnostics/frebanalysis/read | Získejte analýzu Web Apps FREB diagnostiky. |
> | Action | microsoft.web/sites/diagnostics/loganalyzer/read | Získat analyzátor protokolů pro diagnostiku Web Apps |
> | Action | microsoft.web/sites/diagnostics/read | Získá kategorie diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/runtimeavailability/read | Získat dostupnost běhového prostředí diagnostiky Web Apps |
> | Action | microsoft.web/sites/diagnostics/servicehealth/read | Získat Service Health diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Získat analýzu procesoru serveru diagnostiky Web Apps |
> | Action | microsoft.web/sites/diagnostics/sitecrashes/read | Získat selhání serveru diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitelatency/read | Získejte latenci serveru diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Získat Web Apps analýzy paměti pro server diagnostiky |
> | Action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Získat aktualizaci nastavení pro restartování serveru diagnostiky Web Apps |
> | Action | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Získat Web Apps iniciované uživatelem restartování webu |
> | Action | microsoft.web/sites/diagnostics/siteswap/read | Získejte prohození Web Apps diagnostiky webu. |
> | Action | microsoft.web/sites/diagnostics/threadcount/read | Získá počet vláken diagnostiky Web Apps. |
> | Action | microsoft.web/sites/diagnostics/workeravailability/read | Získejte diagnostiku Web Apps Workeravailability. |
> | Action | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Získejte recyklaci pracovního procesu diagnostiky Web Apps. |
> | Action | microsoft.web/sites/domainownershipidentifiers/read | Získá Web Apps identifikátory vlastnictví domény. |
> | Action | microsoft.web/sites/domainownershipidentifiers/write | Aktualizuje Web Apps identifikátory vlastnictví domény. |
> | Action | microsoft.web/sites/extensions/delete | Odstraní Web Apps rozšíření webů. |
> | Action | microsoft.web/sites/extensions/read | Získá Web Apps rozšíření webů. |
> | Action | microsoft.web/sites/extensions/write | Aktualizuje rozšíření Web Apps lokality. |
> | Action | microsoft.web/sites/functions/action | Funkce Web Apps. |
> | Action | microsoft.web/sites/functions/delete | Odstraní funkce Web Apps. |
> | Action | microsoft.web/sites/functions/listsecrets/action | Výpis tajných kódů Web Apps Functions. |
> | Action | microsoft.web/sites/functions/masterkey/read | Získat Web Apps Functions masterkey. |
> | Action | microsoft.web/sites/functions/read | Získá funkce Web Apps. |
> | Action | microsoft.web/sites/functions/token/read | Získat token Web Apps Functions |
> | Action | microsoft.web/sites/functions/write | Aktualizujte Web Apps funkce. |
> | Action | microsoft.web/sites/hostnamebindings/delete | Delete Web Apps Hostname Bindings. |
> | Action | microsoft.web/sites/hostnamebindings/read | Get Web Apps Hostname Bindings. |
> | Action | microsoft.web/sites/hostnamebindings/write | Update Web Apps Hostname Bindings. |
> | Action | microsoft.web/sites/hostruntime/functions/keys/read | Získá Web Apps klíče funkcí Hostruntime. |
> | Action | Microsoft.Web/sites/hostruntime/host/_master/read | Získání hlavního klíče Function App pro operace správy |
> | Action | Microsoft.Web/sites/hostruntime/host/action | Proveďte Function App akci za běhu, jako jsou triggery synchronizace, přidejte funkce, volejte funkce, odstraňte funkce atd. |
> | Action | microsoft.web/sites/hostruntime/host/read | Get Web Apps Hostruntime Host. |
> | Action | microsoft.web/sites/hybridconnection/delete | Odstraní Web Apps hybridní připojení. |
> | Action | microsoft.web/sites/hybridconnection/read | Získá Web Apps hybridní připojení. |
> | Action | microsoft.web/sites/hybridconnection/write | Aktualizuje Web Apps hybridní připojení. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Odstranit Web Apps přenáší obory názvů Hybrid Connection. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/klíče listkey/Action | Vypíše klíče Web Apps přenosů oborů názvů Hybrid Connection. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Získá Web Apps přenosných oborů názvů Hybrid Connection. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Aktualizujte Web Apps přenáší obory názvů Hybrid Connection. |
> | Action | microsoft.web/sites/hybridconnectionrelays/read | Získá Web Apps Relay pro hybridní připojení. |
> | Action | microsoft.web/sites/instances/deployments/delete | Odstraní nasazení Web Apps instancí. |
> | Action | microsoft.web/sites/instances/deployments/read | Získá nasazení instancí Web Apps. |
> | Action | microsoft.web/sites/instances/extensions/log/read | Načte protokol rozšíření instancí Web Apps. |
> | Action | microsoft.web/sites/instances/extensions/processes/read | Získá procesy rozšíření instancí Web Apps. |
> | Action | microsoft.web/sites/instances/extensions/read | Získá rozšíření instancí Web Apps. |
> | Action | microsoft.web/sites/instances/processes/delete | Odstraní procesy Web Apps instance. |
> | Action | microsoft.web/sites/instances/processes/modules/read | Načte instance Web Apps zpracuje moduly. |
> | Action | microsoft.web/sites/instances/processes/read | Získá procesy Web Apps instance. |
> | Action | microsoft.web/sites/instances/processes/threads/read | Načte instance Web Apps zpracuje vlákna. |
> | Action | microsoft.web/sites/instances/read | Získá instance Web Apps. |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Vypíše Web Apps stav triggeru funkce synchronizace. |
> | Action | microsoft.web/sites/metricdefinitions/read | Získat Web Apps definice metriky. |
> | Action | microsoft.web/sites/metrics/read | Získat Web Apps metriky. |
> | Action | microsoft.web/sites/metricsdefinitions/read | Získá definice metrik Web Apps. |
> | Action | Microsoft. Web/weby/migratemysql/Action | Migrujte Web Apps MySql. |
> | Action | microsoft.web/sites/migratemysql/read | Získejte Web Apps migrovat MySql. |
> | Action | microsoft.web/sites/networktrace/action | Web Apps trasování sítě. |
> | Action | microsoft.web/sites/networktraces/operationresults/read | Získá Web Apps výsledky operace trasování sítě. |
> | Action | microsoft.web/sites/newpassword/action | Nové_heslo Web Apps. |
> | Action | microsoft.web/sites/operationresults/read | Získá výsledky Web Apps operace. |
> | Action | microsoft.web/sites/operations/read | Získat Web Apps operace. |
> | Action | microsoft.web/sites/perfcounters/read | Získá Web Apps čítače výkonu. |
> | Action | microsoft.web/sites/premieraddons/delete | Odstraňte Web Apps doplňky Premier. |
> | Action | microsoft.web/sites/premieraddons/read | Získejte Web Apps doplňky Premier. |
> | Action | microsoft.web/sites/premieraddons/write | Aktualizujte Web Apps doplňky Premier. |
> | Action | microsoft.web/sites/privateaccess/read | Získat data kolem povolení přístupu k privátnímu webu a autorizovaných virtuálních sítí, které mají přístup k webu. |
> | Action | Microsoft. Web/weby/PrivateEndpointConnectionsApproval/Action | Schválit připojení privátního koncového bodu |
> | Action | microsoft.web/sites/processes/modules/read | Získá moduly Web Apps procesy. |
> | Action | microsoft.web/sites/processes/read | Získat Web Apps procesy. |
> | Action | microsoft.web/sites/processes/threads/read | Získá vlákna Web Apps procesů. |
> | Action | microsoft.web/sites/publiccertificates/delete | Odstranit Web Apps veřejné certifikáty. |
> | Action | microsoft.web/sites/publiccertificates/read | Získat Web Apps veřejné certifikáty. |
> | Action | microsoft.web/sites/publiccertificates/write | Aktualizujte Web Apps veřejné certifikáty. |
> | Action | Microsoft. Web/weby/publikování/akce | Publikování webové aplikace |
> | Action | Microsoft. Web/weby/publishxml/Action | Získat XML profil publikování pro webovou aplikaci |
> | Action | microsoft.web/sites/publishxml/read | Získat Web Apps publikování XML. |
> | Action | Microsoft.Web/sites/Read | Získat vlastnosti webové aplikace |
> | Action | microsoft.web/sites/recommendationhistory/read | Získejte historii doporučení Web Apps. |
> | Action | Microsoft. Web/Sites/Recommendations/Disable/Action | Zakažte Web Apps doporučení. |
> | Action | Microsoft. Web/weby/doporučení/čtení | Získejte seznam doporučení pro webovou aplikaci. |
> | Action | microsoft.web/sites/recover/action | Obnovit Web Apps. |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | Resetovat konfiguraci webové aplikace |
> | Action | microsoft.web/sites/resourcehealthmetadata/read | Získá metadata Web Apps Resource Health. |
> | Action | Microsoft. Web/weby/restartovat/akce | Restartování webové aplikace |
> | Action | microsoft.web/sites/restore/read | Získat Web Apps obnovení. |
> | Action | microsoft.web/sites/restore/write | Obnovit Web Apps. |
> | Action | microsoft.web/sites/restorefrombackupblob/action | Obnovte webovou aplikaci z objektu BLOB zálohy. |
> | Action | Microsoft. Web/weby/restorefromdeletedapp/Action | Obnovit Web Apps z odstraněné aplikace |
> | Action | microsoft.web/sites/restoresnapshot/action | Obnovte snímky Web Apps. |
> | Action | microsoft.web/sites/siteextensions/delete | Odstraní Web Apps rozšíření webů. |
> | Action | microsoft.web/sites/siteextensions/read | Získá Web Apps rozšíření webů. |
> | Action | microsoft.web/sites/siteextensions/write | Aktualizuje rozšíření Web Apps lokality. |
> | Action | Microsoft. Web/weby/sloty/analyzecustomhostname/Read | Získat Web Apps sloty analyzují vlastní název hostitele. |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | Použijte konfiguraci slotu webové aplikace z cílového slotu na aktuální slot. |
> | Action | Microsoft. Web/weby/sloty/zálohování/akce | Vytvořte novou zálohu slotu webové aplikace. |
> | Action | microsoft.web/sites/slots/backup/read | Získat zálohu pro Web Apps sloty. |
> | Action | microsoft.web/sites/slots/backup/write | Aktualizuje zálohu Web Appsch slotů. |
> | Action | Microsoft. Web/weby/sloty/zálohy/akce | Objevte zálohy Web Appsch slotů. |
> | Action | microsoft.web/sites/slots/backups/delete | Odstraňte zálohy Web Appsch slotů. |
> | Action | microsoft.web/sites/slots/backups/list/action | Vypíše zálohy Web Appsch slotů. |
> | Action | Microsoft. Web/weby/sloty/zálohy/čtení | Získat vlastnosti zálohování slotů webové aplikace |
> | Action | Microsoft. Web/weby/sloty/zálohy/obnovení/akce | Obnovte zálohy Web Appsch slotů. |
> | Action | microsoft.web/sites/slots/config/delete | Odstraní konfiguraci Web Apps slotů. |
> | Action | Microsoft. Web/weby/sloty/config/list/Action | Vypíše nastavení zabezpečení oblasti webové aplikace, jako je například přihlašovací údaje pro publikování, nastavení aplikace a připojovací řetězce. |
> | Action | Microsoft.Web/sites/slots/config/Read | Získat konfigurační nastavení slotu webové aplikace |
> | Action | Microsoft.Web/sites/slots/config/Write | Aktualizovat konfigurační nastavení slotu webové aplikace |
> | Action | microsoft.web/sites/slots/containerlogs/action | Získejte protokoly kontejneru zip pro slot webové aplikace. |
> | Action | Microsoft. Web/weby/sloty/containerlogs/stáhnout/akce | Stáhnout protokoly kontejneru Web Apps slotů. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | Odstranění Web Appsch slotů nepřetržité webové úlohy. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/read | Získejte průběžné webové úlohy pro Web Apps sloty. |
> | Action | Microsoft. Web/weby/sloty/continuouswebjobs/Start/Action | Spustí Web Apps sloty nepřetržité webové úlohy. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/stop/action | Zastavení Web Appsch slotů nepřetržité webové úlohy. |
> | Action | Microsoft.Web/sites/slots/Delete | Odstranit existující slot webové aplikace |
> | Action | microsoft.web/sites/slots/deployments/delete | Odstraňte nasazení Web Apps slotů. |
> | Action | microsoft.web/sites/slots/deployments/log/read | Načte protokol nasazení Web Apps slotů. |
> | Action | microsoft.web/sites/slots/deployments/read | Získejte nasazení Web Apps slotů. |
> | Action | microsoft.web/sites/slots/deployments/write | Aktualizujte nasazení Web Apps slotů. |
> | Action | microsoft.web/sites/slots/detectors/read | Získat Web Apps detektory slotů |
> | Action | Microsoft. Web/weby/sloty/Diagnostika/analýzy/provedení/akce | Spusťte analýzu diagnostiky Web Appsho slotu. |
> | Action | Microsoft. Web/weby/sloty/Diagnostika/analýzy/čtení | Získat analýzu diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Získat diagnostiku Web Apps slotů pro aplikaci ASP.NET Core. |
> | Action | microsoft.web/sites/slots/diagnostics/autoheal/read | Získat diagnostiku Web Apps sloty pro autoretušování. |
> | Action | Microsoft. Web/weby/sloty/Diagnostika/nasazení/čtení | Získá nasazení diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/deployments/read | Získejte nasazení diagnostiky Web Apps slotů. |
> | Action | Microsoft. Web/weby/sloty/Diagnostika/detektory/spuštění/akce | Spusťte detektor diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/read | Získá detektor diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Získejte diagnostiku Web Apps FREBch slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Získejte analyzátor protokolů diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/read | Získat diagnostiku Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Získat dostupnost běhového prostředí diagnostiky Web Apps slotů |
> | Action | microsoft.web/sites/slots/diagnostics/servicehealth/read | Získat Service Health diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Získat analýzu procesoru serveru diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Získat selhání lokality diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/sitelatency/read | Získejte latenci serveru diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Získat analýzu paměti pro diagnostiku Web Apps slotů |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Získat aktualizaci nastavení pro spuštění webu diagnostiky Web Apps slotů |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Vyvolala se inicializace uživatele na webu získat diagnostiku Web Apps slotů. |
> | Action | microsoft.web/sites/slots/diagnostics/siteswap/read | Získat lokalitu diagnostiky Web Apps slotů |
> | Action | microsoft.web/sites/slots/diagnostics/threadcount/read | Získá počet vláken diagnostiky Web Apps slotů. |
> | Action | Microsoft. Web/weby/sloty/Diagnostika/workeravailability/Read | Získejte diagnostiku Web Apps slotů Workeravailability. |
> | Action | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Získejte recyklaci pracovního procesu diagnostiky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/domainownershipidentifiers/read | Získá identifikátory vlastnictví domény Web Apps slotů. |
> | Action | Microsoft. Web/weby/sloty/funkce/listsecrets/Action | Výpis tajných klíčů Web Appsch funkcí slotů. |
> | Action | microsoft.web/sites/slots/functions/read | Získejte funkce pro Web Apps sloty. |
> | Action | microsoft.web/sites/slots/hostnamebindings/delete | Odstraní vazby názvů hostitelů Web Apps slotů. |
> | Action | microsoft.web/sites/slots/hostnamebindings/read | Získejte vazby názvů hostitelů Web Apps slotů. |
> | Action | microsoft.web/sites/slots/hostnamebindings/write | Aktualizujte vazby Web Apps slotů názvů hostitelů. |
> | Action | microsoft.web/sites/slots/hybridconnection/delete | Odstraní hybridní připojení Web Apps slotů. |
> | Action | microsoft.web/sites/slots/hybridconnection/read | Získejte hybridní připojení k Web Apps sloty. |
> | Action | microsoft.web/sites/slots/hybridconnection/write | Aktualizuje hybridní připojení Web Appsových slotů. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Slouží k odstranění přenosných oborů názvů hybridního připojení Web Apps slotů. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Aktualizuje přenos oborů názvů Hybrid Connection Web Apps slotů. |
> | Action | microsoft.web/sites/slots/hybridconnectionrelays/read | Získá přenosy hybridního připojení pro Web Apps sloty. |
> | Action | microsoft.web/sites/slots/instances/deployments/read | Získá nasazení instancí Web Apps slotů. |
> | Action | microsoft.web/sites/slots/instances/processes/delete | Odstraní procesy instancí Web Appsových slotů. |
> | Action | microsoft.web/sites/slots/instances/processes/read | Získá procesy instancí Web Apps slotů. |
> | Action | microsoft.web/sites/slots/instances/read | Získá instance Web Apps slotů. |
> | Action | microsoft.web/sites/slots/metricdefinitions/read | Získá definice metriky pro Web Apps sloty. |
> | Action | Microsoft. Web/weby/sloty/metriky/čtení | Získá metriky Web Apps slotů. |
> | Action | microsoft.web/sites/slots/migratemysql/read | Získejte Web Apps sloty pro migraci MySql. |
> | Action | microsoft.web/sites/slots/networktrace/action | Síťové trasování Web Apps sloty. |
> | Action | microsoft.web/sites/slots/networktraces/operationresults/read | Získat výsledky operace trasování sítě Web Appsových slotů |
> | Action | microsoft.web/sites/slots/newpassword/action | Nové_heslo Web Apps sloty. |
> | Action | microsoft.web/sites/slots/operationresults/read | Načte výsledky operace Web Apps sloty. |
> | Action | microsoft.web/sites/slots/operations/read | Získá operace Web Apps slotů. |
> | Action | microsoft.web/sites/slots/perfcounters/read | Získá čítače výkonu pro Web Apps sloty. |
> | Action | microsoft.web/sites/slots/phplogging/read | Získejte Web Apps sloty Phplogging. |
> | Action | microsoft.web/sites/slots/premieraddons/delete | Odstraňte doplňky Web Apps sloty Premier. |
> | Action | microsoft.web/sites/slots/premieraddons/read | Získejte doplňky pro Web Apps sloty Premier. |
> | Action | microsoft.web/sites/slots/premieraddons/write | Aktualizace Web Apps slotů Premier Doplňky |
> | Action | microsoft.web/sites/slots/processes/read | Získá procesy Web Apps slotů. |
> | Action | microsoft.web/sites/slots/publiccertificates/delete | Odstranit veřejné certifikáty pro Web Apps sloty |
> | Action | microsoft.web/sites/slots/publiccertificates/read | Získejte veřejné certifikáty pro Web Apps sloty. |
> | Action | microsoft.web/sites/slots/publiccertificates/write | Vytvořte nebo aktualizujte veřejné certifikáty pro Web Apps sloty. |
> | Action | Microsoft. Web/weby/sloty/publikování/akce | Publikování pozice webové aplikace |
> | Action | Microsoft. Web/weby/sloty/publishxml/Action | Získat XML profilu publikování pro slot webové aplikace |
> | Action | Microsoft. Web/weby/sloty/číst | Získá vlastnosti slotu nasazení webové aplikace. |
> | Action | microsoft.web/sites/slots/recover/action | Obnovte Web Apps sloty. |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Resetovat konfiguraci slotu webové aplikace |
> | Action | microsoft.web/sites/slots/resourcehealthmetadata/read | Získá Resource Health metadata Web Apps sloty. |
> | Action | Microsoft. Web/weby/sloty/restartovat/akce | Restartovat slot webové aplikace |
> | Action | microsoft.web/sites/slots/restore/read | Získat Web Apps obnovení slotů. |
> | Action | microsoft.web/sites/slots/restore/write | Obnovte Web Apps sloty. |
> | Action | microsoft.web/sites/slots/restorefrombackupblob/action | Obnovte Web Apps slot z objektu BLOB zálohy. |
> | Action | Microsoft. Web/weby/sloty/restorefromdeletedapp/Action | Obnovte sloty webové aplikace z odstraněné aplikace. |
> | Action | Microsoft. Web/weby/sloty/restoresnapshot/Action | Obnovte snímky Web Apps sloty. |
> | Action | microsoft.web/sites/slots/siteextensions/delete | Odstraní rozšíření lokality Web Appsho slotu. |
> | Action | microsoft.web/sites/slots/siteextensions/read | Získá rozšíření lokality pro Web Apps sloty. |
> | Action | microsoft.web/sites/slots/siteextensions/write | Aktualizuje rozšíření lokality Web Appsho slotu. |
> | Action | Microsoft. Web/weby/sloty/slotsdiffs/Action | Získání rozdílů v konfiguraci mezi webovou aplikací a sloty |
> | Action | Microsoft. Web/weby/sloty/slotsswap/Action | Prohození slotů nasazení webové aplikace |
> | Action | microsoft.web/sites/slots/snapshots/read | Získá snímky Web Apps sloty. |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Delete | Odstranit nastavení konfigurace správy zdrojového kódu v pozici webové aplikace |
> | Action | Microsoft. Web/weby/sloty/sourcecontrols/Read | Získat nastavení konfigurace správy zdrojového kódu pro pozici webové aplikace |
> | Action | Microsoft. Web/weby/sloty/sourcecontrols/Write | Aktualizovat konfigurační nastavení správy zdrojových kódů v pozici webové aplikace |
> | Action | Microsoft. Web/weby/sloty/spustit/akce | Spustit slot webové aplikace |
> | Action | Microsoft. Web/weby/sloty/zastavení/akce | Zastavit slot webové aplikace |
> | Action | microsoft.web/sites/slots/sync/action | Synchronizuje Web Apps sloty. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | Odstraní Web Apps sloty aktivované WebJobs. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/read | Získejte Web Apps sloty aktivované WebJobs. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/run/action | Spusťte Web Apps sloty aktivované WebJobs. |
> | Action | microsoft.web/sites/slots/usages/read | Získat Web Apps využití slotů. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Odstraní Web Apps sloty Virtual Network připojení. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Aktualizujte Web Apps sloty Virtual Network připojení brány. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | Získat Virtual Network připojení Web Apps sloty. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | Aktualizační sloty Web Apps Virtual Network připojení. |
> | Action | microsoft.web/sites/slots/webjobs/read | Získejte WebJobs pro Web Apps sloty. |
> | Action | Microsoft. Web/weby/sloty/zápis | Vytvořit nový slot webové aplikace nebo aktualizovat existující pozici |
> | Action | Microsoft. Web/weby/slotsdiffs/Action | Získání rozdílů v konfiguraci mezi webovou aplikací a sloty |
> | Action | Microsoft. Web/weby/slotsswap/Action | Prohození slotů nasazení webové aplikace |
> | Action | microsoft.web/sites/snapshots/read | Získat Web Apps snímky. |
> | Action | Microsoft.Web/sites/sourcecontrols/Delete | Odstranit konfigurační nastavení správy zdrojů webové aplikace |
> | Action | Microsoft. Web/Sites/sourcecontrols/Read | Získat konfigurační nastavení správy zdrojů webové aplikace |
> | Action | Microsoft. Web/weby/sourcecontrols/Write | Aktualizovat konfigurační nastavení správy zdrojů webové aplikace |
> | Action | Microsoft. Web/Sites/Start/Action | Spuštění webové aplikace |
> | Action | Microsoft. Web/weby/zastavení/akce | Zastavení webové aplikace |
> | Action | microsoft.web/sites/sync/action | Synchronizace Web Apps. |
> | Action | microsoft.web/sites/syncfunctiontriggers/action | Aktivační události funkce Sync pro Web Apps. |
> | Action | microsoft.web/sites/triggeredwebjobs/delete | Odstraní Web Apps aktivované webové úlohy. |
> | Action | microsoft.web/sites/triggeredwebjobs/history/read | Získat Web Apps aktivované historii webových úloh |
> | Action | microsoft.web/sites/triggeredwebjobs/read | Získejte Web Apps aktivované webové úlohy. |
> | Action | microsoft.web/sites/triggeredwebjobs/run/action | Spusťte Web Apps aktivované webové úlohy. |
> | Action | microsoft.web/sites/usages/read | Získat Web Apps využití. |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | Odstraní připojení Web Apps Virtual Network. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/read | Získá Web Apps Virtual Network připojení brány. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/write | Aktualizuje brány Web Apps Virtual Network připojení. |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | Získá Web Apps Virtual Network připojení. |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | Aktualizace Web Apps Virtual Network připojení. |
> | Action | microsoft.web/sites/webjobs/read | Získat Web Apps WebJobs. |
> | Action | Microsoft.Web/sites/Write | Vytvořte novou webovou aplikaci nebo aktualizujte některou z existujících. |
> | Action | Microsoft. Web/SKU/Read | Získat SKU. |
> | Action | microsoft.web/sourcecontrols/read | Získat zdrojové ovládací prvky. |
> | Action | microsoft.web/sourcecontrols/write | Aktualizuje ovládací prvky zdroje. |
> | Action | microsoft.web/unregister/action | Zrušte registraci poskytovatele prostředků Microsoft. Web pro předplatné. |
> | Action | Microsoft. Web/ověření/akce | Oproti. |
> | Action | Microsoft. Web/verifyhostingenvironmentvnet/Action | Ověřte virtuální síť hostujícího prostředí. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Action | Microsoft. monitor zátěže byl/komponenty/číst | Načte součásti pro daný prostředek. |
> | Action | Microsoft. monitor zátěže byl/componentsSummary/Read | Získá souhrn komponent. |
> | Action | Microsoft. monitor zátěže byl/monitorInstances/Read | Načte instance monitorování pro daný prostředek. |
> | Action | Microsoft. monitor zátěže byl/monitorInstancesSummary/Read | Získá souhrn instancí monitorování. |
> | Action | Microsoft. monitor zátěže byl/Monitors/Read | Získá monitorování pro daný prostředek. |
> | Action | Microsoft. monitor zátěže byl/Monitors/Write | Konfigurace monitorování pro prostředek |
> | Action | Microsoft. monitor zátěže byl/notificationSettings/Read | Získá nastavení oznámení pro daný prostředek. |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurovat nastavení oznámení pro prostředek |
> | Action | Microsoft. monitor zátěže byl/Operations/Read | Načte podporované operace. |

## <a name="next-steps"></a>Další kroky

- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Předdefinované role pro prostředky Azure](built-in-roles.md)
