---
title: Operace poskytovatele prostředků Azure Resource Manageru | Dokumentace Microsoftu
description: Zobrazit seznam operací dostupných pro poskytovatele prostředků Microsoft Azure Resource Manageru
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fb72de7e9a61c874d4449a5b9c0422c787a7bdc0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728444"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operace poskytovatele prostředků Azure Resource Manageru

Tento článek obsahuje seznam operací dostupných pro každý poskytovatel prostředků Azure Resource Manageru. Tyto operace lze použít v [vlastní role](custom-roles.md) k poskytování granulární [řízení přístupu na základě role (RBAC)](overview.md) k prostředkům v Azure. Operace řetězce mají tento formát: `{Company}.{ProviderName}/{resourceType}/{action}`

Operace poskytovatele prostředků se vždy vyvíjejí. Chcete-li získat nejnovější operace, použijte [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) nebo [seznam operací az provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AAD/domainServices/delete | Odstranit doménu služby |
> | Akce | Microsoft.AAD/domainServices/oucontainer/delete | Odstranění kontejneru organizační jednotky |
> | Akce | Microsoft.AAD/domainServices/oucontainer/read | Kontejnery organizační jednotky pro čtení |
> | Akce | Microsoft.AAD/domainServices/oucontainer/write | Zápis kontejneru organizační jednotky |
> | Akce | Microsoft.AAD/domainServices/read | Čtení Domain Services |
> | Akce | Microsoft.AAD/domainServices/write | Zápis Domain Services |
> | Akce | Microsoft.AAD/locations/operationresults/read |  |
> | Akce | Microsoft.AAD/Operations/read |  |
> | Akce | Microsoft.AAD/register/action | Registrace služby domény |
> | Akce | Microsoft.AAD/unregister/action | Zrušit registraci služby domény |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.aadiam/diagnosticsettings/DELETE | Odstranění nastavení diagnostiky |
> | Akce | Microsoft.aadiam/diagnosticsettings/Read | Čtení nastavení diagnostiky |
> | Akce | Microsoft.aadiam/diagnosticsettings/Write | Zápis nastavení diagnostiky |
> | Akce | Microsoft.aadiam/diagnosticsettingscategories/Read | Čtení kategorie nastavení diagnostiky |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Addons/operations/read | Získá podporované operace poskytovatele prostředků. |
> | Akce | Microsoft.Addons/register/action | Zadané předplatné zaregistrovat Microsoft.Addons |
> | Akce | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Uvádí aktuální informace o plánu podpory pro zadané předplatné. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Odstraní plán zadaný podpory Canonical |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/read | Získáte stav plánu zadané podpory Canonical. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/write | Přidá zadaný typ plán podpory Canonical. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ADHybridHealthService/addsservices/action | Vytvořte novou doménovou strukturu pro příslušného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Získá všechny servery pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/alerts/read | Získá podrobnosti upozornění pro doménové struktury jako alertid výstrahy vyvolané datum, poslední zjištěnou, upozornění popis výstrahy, poslední aktualizované, upozornění úrovně, výstrahy stavu, výstrah, řešení potíží s odkazy atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/configuration/read | Získá konfiguraci služby pro doménovou strukturu. Příklad – název doménové struktury, funkční úroveň domény pojmenování role FSMO hlavního serveru, role FSMO hlavního serveru schémat atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/delete | Odstraní službu a na servery spolu s daty o stavu. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Načte podrobnosti domény a servery pro doménovou strukturu. Příklad – stav, aktivní výstrahy, vyřešené výstrahy, vlastnosti, jako je úroveň funkčnosti domény doménové struktury, hlavní server infrastruktury, primárního řadiče domény, hlavního serveru RID atd.  |
> | Akce | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Získá nastavení předvolby uživatele pro doménovou strukturu.<br>Příklad – MetricCounterName jako ldapsuccessfulbinds ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Nastavení pro další grafy uživatelského rozhraní. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Získá doménové struktury souhrnu dané doménové struktury, jako je název doménové struktury, počet domén v rámci této doménové struktuře, počet lokalitami a lokalitami atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Získá seznam podporovaných metrik pro určitou službu.<br>Pro příklad uzamčení extranetu účet, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu AD FS atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu atd ATQ vlákna celkový počet pro ADDomainService.<br>Spuštění profilu latence, navázat připojení TCP, Insights agenta nesdílených bajtů, statistiky exportu do služby Azure AD pro službu ADSync. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | S ohledem služby, toto rozhraní API získá informace o metrikách.<br>Například toto rozhraní API slouží k získání informací o související s: Extranetu uzamčení účtu, celkový počet neúspěšných požadavků nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu ADFederation atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu, ATQ vlákna celkový atd ADDomain služby.<br>Spuštění profilu, čekací doba připojení protokolu TCP, Insights agenta nesdílených bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Toto rozhraní API získá seznam všech připojili ADDomainServices pro klienta premium. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/read | Získá podrobnosti o službě pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Získá podrobnosti o replikaci pro všechny servery pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Získá počet řadičů domény a jejich chyby replikace, pokud existuje. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Získá dokončení seznamu řadiče domény s podrobnostmi o replikaci pro danou doménovou strukturu. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Přidání instance serveru do služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Během registrace serveru ADDomainService toto rozhraní api je volána k získání přihlašovacích údajů pro připojení nové servery. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Odstraní serveru pro určitou službu a tenanta. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/write | Vytvoří nebo aktualizuje ADDomainService instanci pro příslušného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/action | Konfigurace aktualizací klienta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/read | Přečte konfiguraci klienta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/write | Vytvoří konfiguraci Tenanta. |
> | Akce | Microsoft.ADHybridHealthService/logs/contents/read | Získá obsah instalace agenta a záznamy o registraci uložená v objektu blob. |
> | Akce | Microsoft.ADHybridHealthService/logs/read | Získá protokoly registrace a instalace agenta pro příslušného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/operations/read | Získá seznam operací podporovaný systémem. |
> | Akce | Microsoft.ADHybridHealthService/register/action | Zaregistrovat poskytovatele prostředků ADHybrid stavu služby a povolí vytváření prostředků ADHybrid Health Service. |
> | Akce | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Získá seznam dostupných oblastí, používá DevOps pro podporu incidentů zákazníka. |
> | Akce | Microsoft.ADHybridHealthService/reports/badpassword/read | Získá seznam chybná zadání hesla pro všechny uživatele v Active Directory Federation Service. |
> | Akce | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Získá identifikátor URI SAS objektů Blob obsahující stav a konečné výsledek zařazených do fronty úlohu sestavy nově frekvence chybné uživatelské jméno/heslo se pokusí za ID uživatele na IP adresu za den pro daného Tenanta. |
> | Akce | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Získá seznam DevOps schválených tenantů. Obvykle se používá pro zákaznickou podporu. |
> | Akce | Microsoft.ADHybridHealthService/reports/isdevops/read | Získá hodnotu označující, zda je tenant DevOps souhlas nebo ne. |
> | Akce | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Aktualizace userid(objectid) pro tenanta vybrané dev ops. |
> | Akce | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Získá vybrané nasazení pro daného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Zadané id tenanta získá umístění úložiště tenanta. |
> | Akce | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Získá geografické umístění, ze kterého budou data přístup. |
> | Akce | Microsoft.ADHybridHealthService/services/action | Aktualizace instance služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrah pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrah pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Název dané funkce ověří, pokud služba obsahuje všechno, co je potřeba tuto funkci používat. |
> | Akce | Microsoft.ADHybridHealthService/services/delete | Odstraní instanci služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/services/exporterrors/read | Získá chyby exportu pro danou synchronizační služby. |
> | Akce | Microsoft.ADHybridHealthService/services/exportstatus/read | Získá stav exportu pro určitou službu. |
> | Akce | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Získá zpětnou vazbu upozornění pro určitou službu a server. |
> | Akce | Microsoft.ADHybridHealthService/services/metricmetadata/read | Získá seznam podporovaných metrik pro určitou službu.<br>Pro příklad uzamčení extranetu účet, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu AD FS atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu atd ATQ vlákna celkový počet pro ADDomainService.<br>Spuštění profilu latence, navázat připojení TCP, Insights agenta nesdílených bajtů, statistiky exportu do služby Azure AD pro službu ADSync. |
> | Akce | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Toto rozhraní API s ohledem služby, získá průměr metriky pro určitou službu.<br>Například toto rozhraní API slouží k získání informací o související s: Extranetu uzamčení účtu, celkový počet neúspěšných požadavků nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu ADFederation atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu, ATQ vlákna celkový atd ADDomain služby.<br>Spuštění profilu, čekací doba připojení protokolu TCP, Insights agenta nesdílených bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/metrics/groups/read | S ohledem služby, toto rozhraní API získá informace o metrikách.<br>Například toto rozhraní API slouží k získání informací o související s: Extranetu uzamčení účtu, celkový počet neúspěšných požadavků nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu ADFederation atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu, ATQ vlákna celkový atd ADDomain služby.<br>Spuštění profilu, čekací doba připojení protokolu TCP, Insights agenta nesdílených bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Toto rozhraní API s ohledem služby, získá agregovaná zobrazení pro metriky pro určitou službu.<br>Například toto rozhraní API slouží k získání informací o související s: Extranetu uzamčení účtu, celkový počet neúspěšných požadavků nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu ADFederation atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu, ATQ vlákna celkový atd ADDomain služby.<br>Spuštění profilu, čekací doba připojení protokolu TCP, Insights agenta nesdílených bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Přidat nebo aktualizovat konfiguraci monitorování pro služby. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Získá konfigurace monitorování pro určitou službu. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Přidání nebo aktualizace konfigurací monitorování pro služby. |
> | Akce | Microsoft.ADHybridHealthService/services/premiumcheck/read | Toto rozhraní API získá seznam všech služeb připojení pro klienta premium. |
> | Akce | Microsoft.ADHybridHealthService/services/read | Načte instance služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/services/reports/details/read | Získá sestavu prvních 50 uživatelů s chybami chybných zadání hesla z posledních 7 dní |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/action | Vytvoří instanci serveru ve službě. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Přečte výstrahy pro server. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Během registrace serveru toto rozhraní api je volána k získání přihlašovacích údajů pro připojení nové servery. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Toto rozhraní API pro daný server, získá seznam datových typů, které se nahrávají servery a čas posledního pro každý nahraný. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/delete | Odstraní instanci serveru ve službě. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Získá podrobnosti o chybě exportu synchronizace pro určitou službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | S ohledem služby, toto rozhraní API získá informace o metrikách.<br>Například toto rozhraní API slouží k získání informací o související s: Extranetu uzamčení účtu, celkový počet neúspěšných požadavků nevyřízené žádosti o tokeny (Proxy), za sekundu žádostí o Token pro službu ADFederation atd.<br>NTLM ověření za sekundu, vazby protokolu LDAP úspěšný/s, čas vytvoření vazby protokolu LDAP, aktivní vlákna LDAP, Kerberos ověření za sekundu, ATQ vlákna celkový atd ADDomain služby.<br>Spuštění profilu, čekací doba připojení protokolu TCP, Insights agenta nesdílených bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/read | Načte instanci serveru ve službě. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Získá konfiguraci služby pro daného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Získá stav přidávání na seznam povolených funkcí pro daného tenanta. |
> | Akce | Microsoft.ADHybridHealthService/services/write | Vytvoří instanci služby v tenantovi. |
> | Akce | Microsoft.ADHybridHealthService/unregister/action | Zruší registraci předplatného u poskytovatele prostředků služby ADHybrid stavu. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Advisor/configurations/read | Získání konfigurace |
> | Akce | Microsoft.Advisor/configurations/write | Vytvoření/aktualizace konfigurace |
> | Akce | Microsoft.Advisor/generateRecommendations/action | Vygeneruje doporučení |
> | Akce | Microsoft.Advisor/generateRecommendations/read | Získá generovat stav doporučení |
> | Akce | Microsoft.Advisor/operations/read | Získá operace pro Microsoft Advisor |
> | Akce | Microsoft.Advisor/recommendations/available/action | Nové doporučení je k dispozici v nástroji Microsoft advisor |
> | Akce | Microsoft.Advisor/recommendations/read | Doporučení pro čtení |
> | Akce | Microsoft.Advisor/recommendations/suppressions/delete | Odstraní potlačení |
> | Akce | Microsoft.Advisor/recommendations/suppressions/read | Získá potlačení |
> | Akce | Microsoft.Advisor/recommendations/suppressions/write | Vytvoření/aktualizace potlačení |
> | Akce | Microsoft.Advisor/register/action | Zaregistruje předplatné u Advisoru Microsoft |
> | Akce | Microsoft.Advisor/suppressions/delete | Odstraní potlačení |
> | Akce | Microsoft.Advisor/suppressions/read | Získá potlačení |
> | Akce | Microsoft.Advisor/suppressions/write | Vytvoření/aktualizace potlačení |
> | Akce | Microsoft.Advisor/unregister/action | Zruší registraci předplatného pro Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AlertsManagement/actionRules/read | Získáte všechny akce pravidla pro vstupní filtry. |
> | Akce | Microsoft.AlertsManagement/actionRules/write | Vytvořit nebo aktualizovat pravidlo akce v daném předplatném |
> | Akce | Microsoft.AlertsManagement/alerts/changestate/action | Změňte stav výstrahy. |
> | Akce | Microsoft.AlertsManagement/alerts/delete/action | Odstraňte pravidlo akce v daném předplatném. |
> | Akce | Microsoft.AlertsManagement/alerts/read | Získání všech výstrah pro vstupní filtry. |
> | Akce | Microsoft.AlertsManagement/alertsList/read | Získání všech výstrah pro vstupní filtry napříč předplatnými |
> | Akce | Microsoft.AlertsManagement/alertsSummary/read | Získejte přehled výstrah |
> | Akce | Microsoft.AlertsManagement/alertsSummaryList/read | Získejte přehled výstrah napříč předplatnými |
> | Akce | Microsoft.AlertsManagement/Operations/read | Přečte Zadaná operace |
> | Akce | Microsoft.AlertsManagement/smartGroups/changestate/action | Změnit stav inteligentní skupiny |
> | Akce | Microsoft.AlertsManagement/smartGroups/read | Získat všechny inteligentní skupiny pro vstupní filtry |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Kontroluje, že Analysis Server je uvedený název platný a nepoužívá. |
> | Akce | Microsoft.AnalysisServices/locations/operationresults/read | Načte informace o výsledku zadané operace. |
> | Akce | Microsoft.AnalysisServices/locations/operationstatuses/read | Načte informace o stavu zadané operace. |
> | Akce | Microsoft.AnalysisServices/operations/read | Načte informace o operacích |
> | Akce | Microsoft.AnalysisServices/register/action | Zaregistruje poskytovatele prostředků Analysis Services. |
> | Akce | Microsoft.AnalysisServices/servers/delete | Odstraní Analysis Server. |
> | Akce | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Vypíše stav brány přidružené k serveru. |
> | Akce | Microsoft.AnalysisServices/servers/read | Načte informace o určeném Analysis serveru. |
> | Akce | Microsoft.AnalysisServices/servers/resume/action | Obnoví Analysis Server. |
> | Akce | Microsoft.AnalysisServices/servers/skus/read | Načíst dostupné informace o SKU pro server |
> | Akce | Microsoft.AnalysisServices/servers/suspend/action | Pozastaví Analysis Server. |
> | Akce | Microsoft.AnalysisServices/servers/write | Vytvoří nebo aktualizuje zadaný Analysis Server. |
> | Akce | Microsoft.AnalysisServices/skus/read | Načte informace o SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ApiManagement/checkNameAvailability/read | Ověří, zda za předpokladu, že název služby je k dispozici |
> | Akce | Microsoft.ApiManagement/operations/read | Přečíst všechny operace rozhraní API dostupné pro prostředek Microsoft.ApiManagement |
> | Akce | Microsoft.ApiManagement/register/action | Zaregistrovat předplatné u poskytovatele prostředků Microsoft.ApiManagement |
> | Akce | Microsoft.ApiManagement/reports/read | Získání sestav agregované podle časová období, geografické oblasti, vývojáře, produkty, rozhraní API, operace, předplatné a byRequest. |
> | Akce | Microsoft.ApiManagement/service/apis/delete | Odebrat existující rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/delete | Odeberte existující Diagnostika |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/read | Získání seznamu diagnostiky nebo načtení podrobností diagnostiky |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/write | Přidání nových diagnostických nebo aktualizovat existující diagnostické informace |
> | Akce | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Odebere existující přílohy |
> | Akce | Microsoft.ApiManagement/service/apis/issues/attachments/read | Podrobnosti příloh získat přílohy problém nebo problém získá API Management |
> | Akce | Microsoft.ApiManagement/service/apis/issues/attachments/write | Přidat přílohu problém rozhraní api |
> | Akce | Microsoft.ApiManagement/service/apis/issues/comments/delete | Odebere existující komentáře |
> | Akce | Microsoft.ApiManagement/service/apis/issues/comments/read | Získá komentáře nebo získá API Management problém komentář podrobnosti o problému |
> | Akce | Microsoft.ApiManagement/service/apis/issues/comments/write | Přidat komentáře k problému rozhraní api |
> | Akce | Microsoft.ApiManagement/service/apis/issues/delete | Odebere existující problém |
> | Akce | Microsoft.ApiManagement/service/apis/issues/read | Získat problémy, které jsou přidružené k rozhraní API nebo správu rozhraní API získá podrobnosti o problému |
> | Akce | Microsoft.ApiManagement/service/apis/issues/write | Přidat rozhraní api problém nebo aktualizovat problém rozhraní api |
> | Akce | Microsoft.ApiManagement/service/apis/operations/delete | Odeberte stávající operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policies/delete | Odebrat konfiguraci zásad ze zásad operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policies/read | Získat zásady pro operaci rozhraní API nebo získat podrobnosti o konfiguraci zásad pro operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policies/write | Podrobnosti o sadě zásad konfigurace pro operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policy/delete | Operace odebrání konfigurace zásad |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policy/read | Získat podrobnosti o konfiguraci zásad pro operaci |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policy/write | Podrobnosti o sadě zásad konfigurace pro operaci |
> | Akce | Microsoft.ApiManagement/service/apis/operations/read | Získání seznamu existující operace rozhraní API nebo získat podrobnosti o operaci rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odstranit přiřazení existující značky s existující operace |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/read | Získat značky, které jsou spojené s podrobnostmi o operaci nebo získání značek |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/write | Přidružit existující operace existující značky |
> | Akce | Microsoft.ApiManagement/service/apis/operations/write | Vytvořit novou operaci rozhraní API nebo aktualizovat stávající operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operationsByTags/read | Získat seznam přidružení operaci/značky |
> | Akce | Microsoft.ApiManagement/service/apis/policies/delete | Odebrat konfiguraci zásad ze zásad rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policies/read | Získat zásady pro rozhraní API nebo získat podrobnosti zásad konfigurace pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policies/write | Podrobnosti o sadě zásad konfigurace pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policy/delete | Odebrání konfigurace zásad rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policy/read | Získat podrobnosti o konfiguraci zásad pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policy/write | Podrobnosti o sadě zásad konfigurace pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/products/read | Získat všechny produkty, které je součástí rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/read | Získat seznam všech registrovaných rozhraní API nebo získat podrobné informace o rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/releases/delete | Odebere všechny verze rozhraní API nebo odebrat rozhraní API verze |
> | Akce | Microsoft.ApiManagement/service/apis/releases/read | Získání verzí pro rozhraní API nebo získat podrobné informace o verzi rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/releases/write | Nová verze rozhraní API umožňuje vytvořit nebo aktualizovat stávající verze rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/revisions/delete | Odebere všechny revize rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/revisions/read | Získat revize, které patří do rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/delete | Odebere existující schéma |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/document/read | Získat dokument popisující schématu |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/document/write | Aktualizovat dokument popisující schématu |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/read | Získá všechna schémata pro dané rozhraní API nebo získá schémata používá rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/write | Nastaví schémata používá rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Odebrat značku popis z rozhraní API. |
> | Akce | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Získat značky, popisy v oboru popis rozhraní API nebo získání značek v oboru rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Vytvořit nebo změnit značky popis v oboru rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tags/delete | Odeberte stávající přidružení rozhraní API/značky |
> | Akce | Microsoft.ApiManagement/service/apis/tags/read | Získat všechna rozhraní API/značky přidružení rozhraní API nebo získat podrobnosti o rozhraní API/značky přidružení |
> | Akce | Microsoft.ApiManagement/service/apis/tags/write | Přidat nové přidružení rozhraní API/značky |
> | Akce | Microsoft.ApiManagement/service/apis/write | Nové rozhraní API umožňuje vytvořit nebo aktualizovat existující podrobnosti rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apisByTags/read | Získat seznam přidružení rozhraní API/značky |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/delete | Odeberte existující VersionSet |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/read | Získat seznam verzí skupiny entit nebo získá podrobnosti VersionSet |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/versions/read | Získat seznam entit verze |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/write | Vytvořit nový VersionSet nebo aktualizovat existující VersionSet podrobnosti |
> | Akce | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje Microsoft.ApiManagement prostředky spuštěné ve virtuální síti k výběru aktualizované nastavení sítě. |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/delete | Odstranění existujícího autorizačního serveru |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/read | Načíst seznam autorizací serverů nebo získat podrobnosti o autorizačního serveru |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/write | Vytvořit nové autorizační server nebo aktualizovat podrobnosti existujícího autorizačního serveru |
> | Akce | Microsoft.ApiManagement/service/backends/delete | Odeberte stávající back-endu |
> | Akce | Microsoft.ApiManagement/service/backends/read | Získání seznamu back-EndY nebo získat podrobnosti o back-endu |
> | Akce | Microsoft.ApiManagement/service/backends/reconnect/action | Vytvořit žádost o opětovné připojení |
> | Akce | Microsoft.ApiManagement/service/backends/write | Přidat nový back-end nebo aktualizovat existující podrobnosti back-endu |
> | Akce | Microsoft.ApiManagement/service/backup/action | Zálohování služby API Management do zadaného kontejneru v uživatelem zadaný účet úložiště |
> | Akce | Microsoft.ApiManagement/service/certificates/delete | Odeberte existující certifikát |
> | Akce | Microsoft.ApiManagement/service/certificates/read | Získat seznam certifikátů nebo získat podrobnosti o certifikátu |
> | Akce | Microsoft.ApiManagement/service/certificates/write | Přidat nový certifikát |
> | Akce | Microsoft.ApiManagement/service/delete | Odstranění instance služby API Management |
> | Akce | Microsoft.ApiManagement/service/diagnostics/delete | Odeberte existující Diagnostika |
> | Akce | Microsoft.ApiManagement/service/diagnostics/read | Získání seznamu diagnostiky nebo načtení podrobností diagnostiky |
> | Akce | Microsoft.ApiManagement/service/diagnostics/write | Přidání nových diagnostických nebo aktualizovat existující diagnostické informace |
> | Akce | Microsoft.ApiManagement/service/getssotoken/action | Získá token jednotného přihlašování, který lze použít k přihlášení do rozhraní API správy služby starší verze portálu jako správce |
> | Akce | Microsoft.ApiManagement/service/groups/delete | Odeberte existující skupinu |
> | Akce | Microsoft.ApiManagement/service/groups/read | Získání seznamu skupin nebo získá podrobnosti skupiny |
> | Akce | Microsoft.ApiManagement/service/groups/users/delete | Odebrat existujícího uživatele ze stávající skupiny |
> | Akce | Microsoft.ApiManagement/service/groups/users/read | Získání seznamu skupin uživatelů |
> | Akce | Microsoft.ApiManagement/service/groups/users/write | Přidat existující uživatele do stávající skupiny |
> | Akce | Microsoft.ApiManagement/service/groups/write | Vytvořit novou skupinu nebo aktualizovat existující podrobnosti skupiny |
> | Akce | Microsoft.ApiManagement/service/identityProviders/delete | Odeberte existující zprostředkovatele Identity |
> | Akce | Microsoft.ApiManagement/service/identityProviders/read | Získání seznamu zprostředkovatelů Identity a načtení podrobností zprostředkovatele Identity |
> | Akce | Microsoft.ApiManagement/service/identityProviders/write | Vytvoření nového zprostředkovatele Identity nebo aktualizovat podrobnosti existujícího poskytovatele Identity |
> | Akce | Microsoft.ApiManagement/service/locations/networkstatus/read | Získá stav přístupu síťových prostředků, na kterých závisí služba v umístění. |
> | Akce | Microsoft.ApiManagement/service/loggers/delete | Odeberte existující protokolovací nástroj |
> | Akce | Microsoft.ApiManagement/service/loggers/read | Získání seznamu protokolovacích nástrojů nebo získat podrobnosti o protokolovací nástroj |
> | Akce | Microsoft.ApiManagement/service/loggers/write | Přidat nový protokolovací nástroj nebo aktualizovat existující podrobnosti protokolovací nástroj |
> | Akce | Microsoft.ApiManagement/service/managedeployments/action | Změna skladové položky a jednotek, přidat nebo odebrat místní nasazení služby API Management |
> | Akce | Microsoft.ApiManagement/service/networkstatus/read | Získá stav přístupu síťových prostředků, na kterých závisí služba. |
> | Akce | Microsoft.ApiManagement/service/notifications/action | Odešle oznámení pro zadaného uživatele |
> | Akce | Microsoft.ApiManagement/service/notifications/read | Získá všechny API Management vydavatele oznámení nebo získat API Management vydavatele podrobnosti o oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Odebere existující přidružené k oznámení e-mailu |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Získejte e-mailové příjemce přidružené k rozhraní API Management vydavatele oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Vytvoření nového příjemce e-mailových oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Odebere uživatele přidružené k příjemců oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Získat příjemce uživatelé přidružení k oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Přidání uživatele do příjemců oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/write | Vytvoření nebo aktualizace API Management vydavatele oznámení |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/delete | Odebrat poskytovatele stávající OpenID Connect |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/read | Získání seznamu poskytovatele OpenID Connect nebo načtení podrobností poskytovatele OpenID Connect |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/write | Vytvoření nového poskytovatele OpenID Connect nebo aktualizovat podrobnosti o stávající připojení poskytovatele OpenID |
> | Akce | Microsoft.ApiManagement/service/operationresults/read | Získá aktuální stav dlouho běžící operace |
> | Akce | Microsoft.ApiManagement/service/policies/delete | Konfigurace zásad odebrat ze zásady Tenanta |
> | Akce | Microsoft.ApiManagement/service/policies/read | Získání zásad klienta nebo získat podrobnosti konfigurace zásad pro Tenanta |
> | Akce | Microsoft.ApiManagement/service/policies/write | Podrobnosti o sadě zásad konfigurace pro Tenanta |
> | Akce | Microsoft.ApiManagement/service/policySnippets/read | Získat všechny fragmenty kódu zásady |
> | Akce | Microsoft.ApiManagement/service/portalsettings/read | Získat registrační nastavení na portálu nebo získat nastavení portálu přihlášení nebo získání nastavení delegování pro portál |
> | Akce | Microsoft.ApiManagement/service/portalsettings/write | Aktualizovat nastavení registrace nebo nastavení aktualizace zaregistrovat nebo přihlásit k aktualizaci nastavení nebo nastavení aktualizace Sign In nebo nastavení delegování aktualizace nebo nastavení delegování aktualizace |
> | Akce | Microsoft.ApiManagement/service/products/apis/delete | Odebrat existující rozhraní API z existujícího produktu |
> | Akce | Microsoft.ApiManagement/service/products/apis/read | Získat seznam rozhraní API přidat do existujícího produktu |
> | Akce | Microsoft.ApiManagement/service/products/apis/write | Přidat existující rozhraní API do existujícího produktu |
> | Akce | Microsoft.ApiManagement/service/products/delete | Odeberte existující produkt |
> | Akce | Microsoft.ApiManagement/service/products/groups/delete | Odstranit přiřazení existující skupinu pro vývojáře s existujícím produktem |
> | Akce | Microsoft.ApiManagement/service/products/groups/read | Získat seznam skupin pro vývojáře spojené s produktem |
> | Akce | Microsoft.ApiManagement/service/products/groups/write | Přidružení stávající skupiny pro vývojáře s existujícím produktem |
> | Akce | Microsoft.ApiManagement/service/products/policies/delete | Konfigurace zásad odebrat ze zásady produktu |
> | Akce | Microsoft.ApiManagement/service/products/policies/read | Získání zásad pro produkt nebo získat podrobnosti konfigurace zásad pro produkt |
> | Akce | Microsoft.ApiManagement/service/products/policies/write | Podrobnosti o sadě zásad konfigurace pro produkt |
> | Akce | Microsoft.ApiManagement/service/products/policy/delete | Odebrat konfiguraci zásad z existujícího produktu |
> | Akce | Microsoft.ApiManagement/service/products/policy/read | Získání zásad konfigurace existujícím produktem |
> | Akce | Microsoft.ApiManagement/service/products/policy/write | Nastavení zásad konfigurace pro stávající produkt |
> | Akce | Microsoft.ApiManagement/service/products/read | Získat seznam produktů nebo získat podrobnosti o produktu |
> | Akce | Microsoft.ApiManagement/service/products/subscriptions/read | Získejte seznam předplatných produktu |
> | Akce | Microsoft.ApiManagement/service/products/tags/delete | Odstranit přiřazení existující značky s existujícím produktem |
> | Akce | Microsoft.ApiManagement/service/products/tags/read | Získat značky, které jsou spojené s podrobnostmi o produktu nebo získání značek |
> | Akce | Microsoft.ApiManagement/service/products/tags/write | Přidružení existující značky s existujícím produktem |
> | Akce | Microsoft.ApiManagement/service/products/write | Vytvořit nový produkt nebo aktualizovat existující podrobnosti o produktu |
> | Akce | Microsoft.ApiManagement/service/productsByTags/read | Získat seznam přidružení produktu a značky |
> | Akce | Microsoft.ApiManagement/service/properties/delete | Odebere existující vlastnost |
> | Akce | Microsoft.ApiManagement/service/properties/read | Získá seznam všech vlastností nebo získá podrobnosti o zadané vlastnosti |
> | Akce | Microsoft.ApiManagement/service/properties/write | Vytvoří novou vlastnost nebo aktualizuje hodnotu zadané vlastnosti |
> | Akce | Microsoft.ApiManagement/service/quotas/periods/read | Získání hodnoty čítače kvóty pro období |
> | Akce | Microsoft.ApiManagement/service/quotas/periods/write | Nastavit aktuální hodnotu čítače kvóty |
> | Akce | Microsoft.ApiManagement/service/quotas/read | Získání hodnot pro kvótu |
> | Akce | Microsoft.ApiManagement/service/quotas/write | Nastavit aktuální hodnotu čítače kvóty |
> | Akce | Microsoft.ApiManagement/service/read | Čtení metadat pro instanci služby API Management |
> | Akce | Microsoft.ApiManagement/service/reports/read | Získáte sestavu agregované podle časových období nebo v sestavě Get agregované podle zeměpisné oblasti nebo sestavu Get agregované podle vývojáře.<br>nebo získat sestavu agregované podle produktů.<br>nebo získat sestavu agregované podle rozhraní API nebo získat sestavu agregované podle operace nebo získat sestavu agregované podle předplatného.<br>nebo získání žádosti o data pro generování sestav |
> | Akce | Microsoft.ApiManagement/service/restore/action | Obnovení ze zadaného kontejneru v účtu úložiště zadanou uživatelem služby API Management |
> | Akce | Microsoft.ApiManagement/service/subscriptions/delete | Odstraňte odběr. Tato operace slouží k odstranění předplatného |
> | Akce | Microsoft.ApiManagement/service/subscriptions/read | Získejte seznam předplatných produktu nebo získat podrobnosti o předplatné produktu |
> | Akce | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Znovu vygenerovat primární klíč předplatného |
> | Akce | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Znovu vygenerovat sekundární klíč předplatného |
> | Akce | Microsoft.ApiManagement/service/subscriptions/write | Přihlášení k odběru stávajícího uživatele do existujícího produktu nebo aktualizovat existující podrobnosti o předplatném. Tato operace slouží k prodloužení předplatného |
> | Akce | Microsoft.ApiManagement/service/tagResources/read | Získání seznamu značek přidružených zdrojů. |
> | Akce | Microsoft.ApiManagement/service/tags/delete | Odeberte existující značky |
> | Akce | Microsoft.ApiManagement/service/tags/read | Získání seznamu značek a získat tak podrobné údaje značky |
> | Akce | Microsoft.ApiManagement/service/tags/write | Přidat novou značku nebo aktualizovat existující podrobnosti značky |
> | Akce | Microsoft.ApiManagement/service/templates/delete | Resetovat výchozí rozhraní API správy e-mailová šablona |
> | Akce | Microsoft.ApiManagement/service/templates/read | Získá všechny e-mailové šablony nebo správu rozhraní API získá e-mailu Podrobnosti šablony |
> | Akce | Microsoft.ApiManagement/service/templates/write | Vytvořit nebo aktualizovat rozhraní API správy e-mailové šablony nebo aktualizuje API Management e-mailová šablona |
> | Akce | Microsoft.ApiManagement/service/tenant/delete | Odebrání konfigurace zásad pro klienta |
> | Akce | Microsoft.ApiManagement/service/tenant/deploy/action | Spustí úlohu nasazení provést změny konfigurace v databázi z větve zadaného git. |
> | Akce | Microsoft.ApiManagement/service/tenant/operationResults/read | Získání seznamu výsledky operace nebo získat výsledek operace s konkrétní |
> | Akce | Microsoft.ApiManagement/service/tenant/read | Získání zásad konfigurace pro tenanta nebo získání tenanta podrobné informace o přístupu |
> | Akce | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Znovu vygenerovat primární přístupový klíč |
> | Akce | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Znovu vygenerovat sekundární přístupový klíč |
> | Akce | Microsoft.ApiManagement/service/tenant/save/action | Vytvoří potvrzení se snímek konfigurace pro zadané větve v úložišti |
> | Akce | Microsoft.ApiManagement/service/tenant/syncState/read | Získat stav poslední synchronizace git |
> | Akce | Microsoft.ApiManagement/service/tenant/validate/action | Ověří změny z větve zadaného git |
> | Akce | Microsoft.ApiManagement/service/tenant/write | Nastavení zásad konfigurace pro tenanta nebo podrobné informace o aktualizaci klienta přístup informace |
> | Akce | Microsoft.ApiManagement/service/updatecertificate/action | Nahrát certifikát SSL pro službu API Management |
> | Akce | Microsoft.ApiManagement/service/updatehostname/action | Instalační program, aktualizovat nebo odebrat vlastní názvy domén pro službu API Management |
> | Akce | Microsoft.ApiManagement/service/users/action | Registrace nového uživatele |
> | Akce | Microsoft.ApiManagement/service/users/applications/attachments/delete | Odebere přílohu |
> | Akce | Microsoft.ApiManagement/service/users/applications/attachments/read | Získá aplikaci přílohy nebo získá přílohy |
> | Akce | Microsoft.ApiManagement/service/users/applications/attachments/write | Přidat přílohu k aplikaci |
> | Akce | Microsoft.ApiManagement/service/users/applications/delete | Odebere existující aplikace |
> | Akce | Microsoft.ApiManagement/service/users/applications/read | Získání seznamu všech uživatelských aplikací nebo podrobnosti o aplikaci získá API Management |
> | Akce | Microsoft.ApiManagement/service/users/applications/write | Registruje aplikaci API Management nebo aktualizace podrobnosti o aplikaci |
> | Akce | Microsoft.ApiManagement/service/users/delete | Odebrat uživatelský účet |
> | Akce | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Generovat adresu URL jednotného přihlašování. Adresa URL slouží pro přístup k portálu pro správu |
> | Akce | Microsoft.ApiManagement/service/users/groups/read | Získání seznamu skupin uživatelů. |
> | Akce | Microsoft.ApiManagement/service/users/keys/read | Získá seznam klíčů uživatelů |
> | Akce | Microsoft.ApiManagement/service/users/read | Získání seznamu registrovaných uživatelů nebo získat podrobnosti o účtu uživatele |
> | Akce | Microsoft.ApiManagement/service/users/subscriptions/read | Získejte seznam předplatných uživatele |
> | Akce | Microsoft.ApiManagement/service/users/token/action | Získat token přístupový token pro uživatele |
> | Akce | Microsoft.ApiManagement/service/users/write | Registrovat nové uživatele nebo aktualizovat podrobnosti účtu stávajícího uživatele |
> | Akce | Microsoft.ApiManagement/service/write | Vytvořit novou instanci služby API Management |
> | Akce | Microsoft.ApiManagement/unregister/action | Zrušit registraci předplatného u poskytovatele prostředků Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Authorization/checkAccess/action | Zkontroluje, jestli má volající autorizaci provést určitou akci. |
> | Akce | Microsoft.Authorization/classicAdministrators/delete | Umožňuje odebrat správce z předplatného. |
> | Akce | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Získá stavy operace správce daného předplatného. |
> | Akce | Microsoft.Authorization/classicAdministrators/read | Umožňuje načíst správce předplatného. |
> | Akce | Microsoft.Authorization/classicAdministrators/write | Umožňuje přidat nebo změnit správce předplatného. |
> | Akce | Microsoft.Authorization/denyAssignments/delete | Odstraní přiřazení zamítnutí v zadaném oboru. |
> | Akce | Microsoft.Authorization/denyAssignments/read | Získání informací o přiřazení zamítnutí |
> | Akce | Microsoft.Authorization/denyAssignments/write | Vytvoří přiřazení zamítnutí v zadaném oboru. |
> | Akce | Microsoft.Authorization/elevateAccess/action | Udělí volajícímu přístup Správce uživatelských přístupů v oboru tenanta. |
> | Akce | Microsoft.Authorization/locks/delete | Umožňuje odstranit zámky v zadaném oboru. |
> | Akce | Microsoft.Authorization/locks/read | Umožňuje načíst zámky v zadaném oboru. |
> | Akce | Microsoft.Authorization/locks/write | Umožňuje přidat zámky v zadaném oboru. |
> | Akce | Microsoft.Authorization/operations/read | Získá seznam operací. |
> | Akce | Microsoft.Authorization/permissions/read | Umožňuje vypsat seznam všech oprávnění, která má volající v daném oboru. |
> | Akce | Microsoft.Authorization/policyAssignments/delete | Odstranění přiřazení zásady v zadaném oboru |
> | Akce | Microsoft.Authorization/policyAssignments/read | Získání informací o přiřazení zásady |
> | Akce | Microsoft.Authorization/policyAssignments/write | Vytvoření přiřazení zásady v zadaném oboru |
> | Akce | Microsoft.Authorization/policyDefinitions/delete | Odstranění definice zásady |
> | Akce | Microsoft.Authorization/policyDefinitions/read | Získání informací o definici zásady |
> | Akce | Microsoft.Authorization/policyDefinitions/write | Vytvoření vlastní definice zásady |
> | Akce | Microsoft.Authorization/policySetDefinitions/delete | Odstranění definice sady zásad |
> | Akce | Microsoft.Authorization/policySetDefinitions/read | Získá informace o definici sady zásad. |
> | Akce | Microsoft.Authorization/policySetDefinitions/write | Vytvoření vlastní definice sady zásad |
> | Akce | Microsoft.Authorization/providerOperations/read | Získejte operace pro všechny poskytovatele prostředků, které je možné použít v definicích rolí. |
> | Akce | Microsoft.Authorization/roleAssignments/delete | Umožňuje odstranit přiřazení role v zadaném oboru. |
> | Akce | Microsoft.Authorization/roleAssignments/read | Umožňuje načíst informace o přiřazení role. |
> | Akce | Microsoft.Authorization/roleAssignments/write | Umožňuje vytvořit přiřazení role v zadaném oboru. |
> | Akce | Microsoft.Authorization/roleDefinitions/delete | Odstraňte zadané definice vlastních rolí. |
> | Akce | Microsoft.Authorization/roleDefinitions/read | Umožňuje načíst informace o definici role. |
> | Akce | Microsoft.Authorization/roleDefinitions/write | Odstraňte nebo aktualizujte definice vlastních rolí s určenými oprávněními a přiřaditelnými obory. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Přečtěte si informace o registraci Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapíše požadavek na opětovné vygenerování klíčů Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/certificates/delete | Odstraní prostředek certifikátu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/certificates/getCount/action | Načte počet certifikátů |
> | Akce | Microsoft.Automation/automationAccounts/certificates/read | Načte prostředek certifikátu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/certificates/write | Vytvoří nebo aktualizuje prostředek certifikátu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/read | Přečte kompilace Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/read | Přečte kompilace Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapíše kompilace Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapíše kompilace Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/configurations/content/read | Přečte konfiguraci mediálního obsahu |
> | Akce | Microsoft.Automation/automationAccounts/configurations/delete | Odstraní obsah Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/configurations/getCount/action | Načte počet obsahu Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/configurations/read | Získá obsah Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/configurations/write | Zapíše obsah Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/connections/delete | Odstraní prostředek připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connections/getCount/action | Načte počet připojení |
> | Akce | Microsoft.Automation/automationAccounts/connections/read | Načte prostředek připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connections/write | Vytvoří nebo aktualizuje prostředek připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connectionTypes/delete | Odstraní prostředek typu připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connectionTypes/read | Získá prostředek typu připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connectionTypes/write | Vytvoří prostředek typu připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/credentials/delete | Odstraní prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/credentials/getCount/action | Načte počet přihlašovacích údajů |
> | Akce | Microsoft.Automation/automationAccounts/credentials/read | Načte prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/credentials/write | Vytvoří nebo aktualizuje prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/delete | Odstraní účet Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Odstraní prostředky Hybrid Runbook Worker |
> | Akce | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
> | Akce | Microsoft.Automation/automationAccounts/jobs/output/read | Získá výstup úlohy |
> | Akce | Microsoft.Automation/automationAccounts/jobs/read | Načte úlohu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví běh úlohy Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Načte obsah runbooku Azure Automation v době provádění úlohy |
> | Akce | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobs/streams/read | Načte stream úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobs/streams/read | Načte stream úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/delete | Odstraní plán úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/read | Získá plán úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Získá pracovní prostor propojený s účtem služby automation |
> | Akce | Microsoft.Automation/automationAccounts/listKeys/action | Načte klíče pro účet služby automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/activities/read | Získá aktivity služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/delete | Odstraní modul prostředí Powershell pro Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/getCount/action | Získá počet modulů prostředí Powershell v rámci účtu Automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/read | Získá modul prostředí Powershell pro Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/write | Vytvoří nebo aktualizuje modul prostředí Powershell pro Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Odstraní konfiguraci uzlu Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Přečte obsah konfigurace uzlu DSC Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Přečte konfiguraci uzlu Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapíše konfigurace uzlu DSC Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/nodecounts/read | Přečte souhrnný počet uzlů pro zadaný typ. |
> | Akce | Microsoft.Automation/automationAccounts/nodes/delete | Odstraní uzlů Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/nodes/read | Načte uzly DSC Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Přečte obsah zprávy o Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/nodes/reports/read | Přečte sestav Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/nodes/write | Vytvoří nebo aktualizuje uzlů Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Získá TypeFields služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/python2Packages/delete | Odstraní balíček Azure Automation Python 2 |
> | Akce | Microsoft.Automation/automationAccounts/python2Packages/read | Získá balíček s Azure Automation Python 2 |
> | Akce | Microsoft.Automation/automationAccounts/python2Packages/write | Vytvoří nebo aktualizuje balíček Azure Automation Python 2 |
> | Akce | Microsoft.Automation/automationAccounts/python3Packages/delete | Odstraní balíček Azure Automation Python 3 |
> | Akce | Microsoft.Automation/automationAccounts/python3Packages/read | Získá balíček s Azure Automation Python 3 |
> | Akce | Microsoft.Automation/automationAccounts/python3Packages/write | Vytvoří nebo aktualizuje balíček Azure Automation Python 3 |
> | Akce | Microsoft.Automation/automationAccounts/read | Získá účet Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/content/read | Načte obsah runbooku Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/delete | Odstraní runbook služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Vytvoří obsah konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Načte výsledky operace koncept runbooku Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/read | Načte koncept runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Načte testovací úlohu konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Obnoví testovací úlohu konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Zastaví testovací úlohu konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Pozastaví testovací úlohu konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Vytvoří testovací úlohu konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Vrátit zpět úpravy konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Získá počet sad runbook Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publikuje koncept runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/write | Vytvoří nebo aktualizuje runbook služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/schedules/delete | Odstraní prostředek plánování Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/schedules/getCount/action | Získá počet plánů služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/schedules/read | Načte prostředek plánování Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/schedules/write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Odstraní konfiguraci aktualizace softwaru služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Získá konfiguraci aktualizace softwaru služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Vytvoří nebo aktualizuje konfigurace aktualizace softwaru Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/statistics/read | Získá statistiku služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Získání počítače s Azure Automation. aktualizace nasazení |
> | Akce | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Načte úlohu Azure Automation. aktualizace správu oprav |
> | Akce | Microsoft.Automation/automationAccounts/usages/read | Získá využití služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/variables/delete | Odstraní variabilní prostředek Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/variables/read | Přečte variabilní prostředek Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/variables/write | Vytvoří nebo aktualizuje variabilní prostředek Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/watchers/delete | Odstranit úlohu sledovacího procesu služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/watchers/read | Získá úlohu sledovacího procesu služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/watchers/start/action | Spustit úlohu sledovacího procesu služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/watchers/stop/action | Zastavit úlohu sledovacího procesu služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/streams/read | Získá datový proud úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Odstranit některé akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Získá akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Vytvoření akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/write | Vytvoří úlohu sledovacího procesu služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/action | Vygeneruje URI pro webhook služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/delete | Odstraní webhook služby Azure Automation  |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/read | Přečte webhook služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/write | Vytvoří nebo aktualizuje webhook služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/write | Vytvoří nebo aktualizuje účet Azure Automation |
> | Akce | Microsoft.Automation/operations/read | Získá dostupné operace pro prostředky Azure Automation. |
> | Akce | Microsoft.Automation/register/action | Zaregistruje předplatné do Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Odstranit prostředek B2C Directory |
> | Akce | Microsoft.AzureActiveDirectory/b2cDirectories/read | Zobrazit prostředek B2C Directory |
> | Akce | Microsoft.AzureActiveDirectory/b2cDirectories/write | Vytvořit nebo aktualizovat prostředek B2C Directory |
> | Akce | Microsoft.AzureActiveDirectory/operations/read | Přečte všechny operace rozhraní API dostupné pro poskytovatele prostředků Microsoft.AzureActiveDirectory. |
> | Akce | Microsoft.AzureActiveDirectory/register/action | Zaregistruje předplatné poskytovatele prostředků Microsoft.AzureActiveDirectory. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AzureStack/Operations/read | Získá vlastnosti operace poskytovatele prostředků |
> | Akce | Microsoft.AzureStack/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft.AzureStack |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Odstraní předplatné zákazníka Azure Stack |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/read | Získá vlastnosti předplatnému Azure Stack zákazníka |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/write | Vytvoří nebo aktualizuje předplatnému Azure Stack zákazníka |
> | Akce | Microsoft.AzureStack/registrations/delete | Odstraní registrace Azure Stack |
> | Akce | Microsoft.AzureStack/registrations/getActivationKey/action | Získá nejnovější klíč aktivace služby Azure Stack |
> | Akce | Microsoft.AzureStack/registrations/products/listDetails/action | Načte podrobnosti o produkt poskytovaný Azure Stack Marketplace rozšířené |
> | Akce | Microsoft.AzureStack/registrations/products/read | Získá vlastnosti o produkt poskytovaný Azure Stack Marketplace |
> | Akce | Microsoft.AzureStack/registrations/read | Získá vlastnosti registrace Azure Stack |
> | Akce | Microsoft.AzureStack/registrations/write | Vytvoří nebo aktualizuje registrace Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Batch/batchAccounts/applications/delete | Odstranění aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/read | Seznam aplikací, nebo získá vlastnosti aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktivuje balíčku aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/delete | Odstraní balíček aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/read | Získá vlastnosti balíčku aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/write | Vytvoří nový balíček aplikace nebo aktualizuje stávající balíček aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/write | Vytvoří novou aplikaci nebo aktualizuje existující aplikace |
> | Akce | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Získá výsledky dlouho běžící operace s certifikátem na účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Zruší Neúspěšné odstranění certifikát v účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/certificates/delete | Odstraní certifikát z účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/certificates/read | Obsahuje seznam certifikátů na účtu Batch nebo načte vlastnosti certifikátu |
> | Akce | Microsoft.Batch/batchAccounts/certificates/write | Vytvoří nový certifikát v účtu Batch nebo aktualizuje existující certifikát |
> | Akce | Microsoft.Batch/batchAccounts/delete | Odstraní účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/listkeys/action | Zobrazí přístupové klíče pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/operationResults/read | Získá výsledky dlouho běžící operace účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/poolOperationResults/read | Získá výsledky dlouho běžící operace fondu na účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/delete | Odstraní fond z účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Zakáže Automatické škálování pro účet fondu služby Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/read | Obsahuje seznam fondů na účet Batch nebo načte vlastnosti fondu |
> | Akce | Microsoft.Batch/batchAccounts/pools/stopResize/action | Zastaví s probíhající Změna velikosti operace na účtu fondu služby Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Upgrade operačního systému účtu fondu služby Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/write | Vytvoří nový fond na účtu Batch nebo aktualizuje existující fond |
> | Akce | Microsoft.Batch/batchAccounts/read | Obsahuje seznam účtů Batch nebo načte vlastnosti účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/regeneratekeys/action | Obnoví přístupové klíče pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje přístupových klíčů pro účet úložiště automaticky nakonfigurované pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/write | Vytvoří nový účet Batch nebo aktualizuje existující účet Batch |
> | Akce | Microsoft.Batch/locations/checkNameAvailability/action | Ověří, že název účtu je platný a nepoužívá se. |
> | Akce | Microsoft.Batch/locations/quotas/read | Získá kvóty služby Batch zadaného předplatného v zadané oblasti Azure |
> | Akce | Microsoft.Batch/operations/read | Zobrazit seznam operací dostupných na poskytovatele Microsoft.Batch prostředků |
> | Akce | Microsoft.Batch/register/action | Zaregistruje předplatné u poskytovatele prostředků služby Batch a povolí vytvoření účtů služby Batch |
> | Akce | Microsoft.Batch/unregister/action | Zruší registraci předplatného u poskytovatele prostředků služby Batch, brání vytváření účtů služby Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Billing/billingPeriods/read | Zobrazí seznam dostupných fakturační období |
> | Akce | Microsoft.Billing/invoices/read | K dispozici seznamy faktury |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.BingMaps/mapApis/Delete | Operace odstranění |
> | Akce | Microsoft.BingMaps/mapApis/listSecrets/action | Uvádí tajné klíče. |
> | Akce | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Přečíst autorizační token jednotného přihlašování pro prostředek |
> | Akce | Microsoft.BingMaps/mapApis/Read | Operace čtení |
> | Akce | Microsoft.BingMaps/mapApis/regenerateKey/action | Znovu vygeneruje klíč. |
> | Akce | Microsoft.BingMaps/mapApis/Write | Operace zápisu |
> | Akce | Microsoft.BingMaps/Operations/read | Popis operace. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Blueprint/blueprintAssignments/delete | Odstraní všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprintAssignments/read | Přečte všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprintAssignments/write | Vytvoří nebo aktualizuje všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprints/artifacts/delete | Odstraní všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprints/artifacts/read | Přečte všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprints/artifacts/write | Vytvoří nebo aktualizuje všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprints/delete | Odstraní všechny podrobné plány. |
> | Akce | Microsoft.Blueprint/blueprints/read | Přečte všechny podrobné plány. |
> | Akce | Microsoft.Blueprint/blueprints/versions/artifacts/read | Přečte všechny artefakty podrobného plánu. |
> | Akce | Microsoft.Blueprint/blueprints/versions/delete | Odstraní všechny podrobné plány. |
> | Akce | Microsoft.Blueprint/blueprints/versions/read | Přečte všechny podrobné plány. |
> | Akce | Microsoft.Blueprint/blueprints/versions/write | Vytvoří nebo aktualizuje všechny podrobné plány. |
> | Akce | Microsoft.Blueprint/blueprints/write | Vytvoří nebo aktualizuje všechny podrobné plány. |
> | Akce | Microsoft.Blueprint/register/action | Zaregistruje poskytovatele prostředků podrobných plánů Azure |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.BotService/botServices/channels/delete | Odstranit Bot Service |
> | Akce | Microsoft.BotService/botServices/channels/read | Přečtěte si Bot Service |
> | Akce | Microsoft.BotService/botServices/channels/write | Zápis Bot Service |
> | Akce | Microsoft.BotService/botServices/connections/delete | Odstranit Bot Service |
> | Akce | Microsoft.BotService/botServices/connections/read | Přečtěte si Bot Service |
> | Akce | Microsoft.BotService/botServices/connections/write | Zápis Bot Service |
> | Akce | Microsoft.BotService/botServices/delete | Odstranit Bot Service |
> | Akce | Microsoft.BotService/botServices/read | Přečtěte si Bot Service |
> | Akce | Microsoft.BotService/botServices/write | Zápis Bot Service |
> | Akce | Microsoft.BotService/locations/operationresults/read | Umožňuje načíst stav asynchronní operace |
> | Akce | Microsoft.BotService/Operations/read | Přečíst operace pro všechny typy prostředků |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Cache/checknameavailability/action | Kontroluje, jestli je název k dispozici pro použití s novou mezipamětí Azure pro Redis |
> | Akce | Microsoft.Cache/locations/operationresults/read | Získá výsledek dlouho spuštěné operace, pro kterou se klientovi dříve vrátila hlavička Location. |
> | Akce | Microsoft.Cache/operations/read | Vypíše operace, které podporuje poskytovatel Microsoft.Cache. |
> | Akce | Microsoft.Cache/redis/delete | Odstranit celou mezipaměť Azure Redis |
> | Akce | Microsoft.Cache/redis/export/action | Export dat Redis v určeném formátu do objektů blob úložiště označených prefixem |
> | Akce | Microsoft.Cache/redis/firewallRules/delete | Odstranit pravidla firewallu protokolu IP mezipaměti Azure Redis |
> | Akce | Microsoft.Cache/redis/firewallRules/read | Získat pravidla firewallu protokolu IP mezipaměti Azure Redis |
> | Akce | Microsoft.Cache/redis/firewallRules/write | Upravit pravidla firewallu protokolu IP mezipaměti Azure Redis |
> | Akce | Microsoft.Cache/redis/forceReboot/action | Vynuťte restartování instance mezipaměti, což ale může mít za následek ztrátu dat. |
> | Akce | Microsoft.Cache/redis/import/action | Import dat určeného formátu z víc objektů blob do Redis |
> | Akce | Microsoft.Cache/redis/linkedservers/delete | Odstranit propojený Server z Azure mezipaměti pro Redis |
> | Akce | Microsoft.Cache/redis/linkedservers/read | Získáte propojené servery přidružené mezipaměti Azure pro Redis. |
> | Akce | Microsoft.Cache/redis/linkedservers/write | Přidat propojený Server do Azure Cache pro Redis |
> | Akce | Microsoft.Cache/redis/listKeys/action | Zobrazení hodnoty mezipaměti Azure pro přístupové klíče služby Redis na portálu management portal |
> | Akce | Microsoft.Cache/redis/listUpgradeNotifications/read | Vypíše seznam nejnovějších upozornění na upgrady pro klienta mezipaměti. |
> | Akce | Microsoft.Cache/redis/metricDefinitions/read | Získá dostupné metriky pro Redis pro Azure Cache |
> | Akce | Microsoft.Cache/redis/patchSchedules/delete | Odstranit plán oprav mezipaměti Azure Redis |
> | Akce | Microsoft.Cache/redis/patchSchedules/read | Získá plán oprav mezipaměti Azure pro Redis |
> | Akce | Microsoft.Cache/redis/patchSchedules/write | Upraví plán oprav mezipaměti Azure pro Redis |
> | Akce | Microsoft.Cache/redis/read | Zobrazení mezipaměti Azure pro nastavení a konfiguraci Redis na portálu pro správu |
> | Akce | Microsoft.Cache/redis/recommendations/read | Čtení z mezipaměti Azure Redis doporučení |
> | Akce | Microsoft.Cache/redis/regenerateKey/action | Změňte hodnotu z mezipaměti Azure přístupové klíče služby Redis na portálu management portal |
> | Akce | Microsoft.Cache/redis/start/action | Spuštění instance mezipaměti |
> | Akce | Microsoft.Cache/redis/stop/action | Zastavení instance mezipaměti |
> | Akce | Microsoft.Cache/redis/write | Upravit ukládání do mezipaměti Azure Redis pro nastavení a konfigurace v portálu pro správu |
> | Akce | Microsoft.Cache/register/action | Zaregistruje poskytovatele prostředků Microsoft.Cache u předplatného. |
> | Akce | Microsoft.Cache/unregister/action | Zruší registraci poskytovatele prostředků Microsoft.Cache u předplatného. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Capacity/register/action | Zaregistrovat poskytovatele prostředků kapacity a povolí vytváření prostředků kapacity. |
> | Akce | Microsoft.Capacity/reservationorders/action | Aktualizovat všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/delete | Odstranit všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/read | Číst všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/reservations/action | Aktualizovat všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/reservations/delete | Odstranit všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/reservations/read | Číst všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/reservations/revisions/read | Číst všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/reservations/write | Vytvořte všechny rezervace |
> | Akce | Microsoft.Capacity/reservationorders/write | Vytvořte všechny rezervace |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Akce | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Akce | Microsoft.Cdn/edgenodes/delete |  |
> | Akce | Microsoft.Cdn/edgenodes/read |  |
> | Akce | Microsoft.Cdn/edgenodes/write |  |
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
> | Akce | Microsoft.Cdn/register/action | Zaregistruje předplatné u poskytovatele prostředků CDN a povolí vytváření profilů CDN. |
> | Akce | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Odstranění existujícího certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Získat seznam certifikátů |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Přidat nový certifikát nebo aktualizaci existující |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Delete | Odstranit existující AppServiceCertificate |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Read | Získání seznamu objednávky certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Opakujte existující objednávky certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Obnovit existující objednávky certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Znovu poslat e-mailu certifikát |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Znovu poslat e-mailů požadavek na jinou e-mailovou adresu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Načíst zapečetění lokality pro vydaný certifikát App Service |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Získání seznamu akce certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Načíst historii e-mailu certifikát |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Ověřit vlastnictví domény |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Write | Přidat nové objednávky certifikátu nebo aktualizaci existující |
> | Akce | Microsoft.CertificateRegistration/operations/Read | Vypsat všechny operace registrace certifikátu app service |
> | Akce | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Instanční objekt služby zřizování pro instanční objekt služby aplikace |
> | Akce | Microsoft.CertificateRegistration/register/action | Registrace poskytovatele prostředků Certificates společnosti Microsoft pro předplatné |
> | Akce | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Ověřit certifikát nákupní objektu bez odeslání |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicCompute/capabilities/read | Umožňuje zobrazit schopnosti. |
> | Akce | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Umožňuje zkontrolovat dostupnost zadaného názvu domény. |
> | Akce | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Zjistí dostupnost zadaného názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/active/write | Slouží k nastavení názvu aktivní domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Umožňuje zobrazit skupinu dostupnosti pro prostředek. |
> | Akce | Microsoft.ClassicCompute/domainNames/capabilities/read | Ukazuje možnosti názvu domény |
> | Akce | Microsoft.ClassicCompute/domainNames/delete | Umožňuje odebrat názvy domén pro prostředky. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Umožňuje zobrazit sloty pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Získat roli ve slotu nasazení názvu domény |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Získat instanci role pro roli ve slotu nasazení názvu domény |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Získá stav slotu nasazení |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Přidá stav slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Získat upgradovací doménu pro slot nasazení v názvu domény |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Aktualizovat upgradovací doménu pro slot nasazení v názvu domény |
> | Akce | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Umožňuje vytvořit nebo aktualizovat slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/delete | Odebere rozšíření názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Přečte stav operace pro rozšíření názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/read | Vrátí rozšíření názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/write | Přidá rozšíření názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Umožňuje odebrat interní nástroj pro vyrovnávání zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Přečte stav operace pro interní služby Load Balancer názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Umožňuje načíst interní nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Umožňuje vytvořit nový interní nástroj pro vyrovnávání zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Přečte stav operace pro sady koncových bodů s vyrovnaným zatížením u názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Získá sady koncových bodů s vyrovnáváním zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Přidá sadu koncových bodů s vyrovnáváním zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Získá stav operace pro název domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Přečte stav operace pro rozšíření názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/read | Umožňuje načíst názvy domén pro prostředky. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Umožňuje odstranit používané certifikáty služeb. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Přečte stav operace pro certifikáty služeb názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Umožňuje načíst používané certifikáty služeb. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Umožňuje přidat nebo změnit používané certifikáty služeb. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Přeruší migraci slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Potvrdí migraci slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/delete | Umožňuje odstranit zadaný slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Přečte stav operace pro sloty názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Připraví migraci slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/read | Umožňuje zobrazit sloty pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Odebere odkaz na rozšíření u role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Přečte stav operace pro reference na rozšíření rolí slotů názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Vrátí odkaz na rozšíření u role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Přidá nebo upraví odkaz na rozšíření u role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Získá definici metriky role pro název domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Získá metriku role pro název domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Získá stav operace pro roli slotu názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Umožňuje načíst nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Umožňuje přidat nebo změnit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Umožňuje načíst definice metrik. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/read | Umožňuje načíst roli slotu pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Stáhne soubor Připojení ke vzdálené ploše pro instanci role v roli slotu názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Získá stav operace pro instanci role v roli slotu názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Umožňuje načíst instanci role. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Znovu sestaví instanci role. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Umožňuje obnovit instanci role z image. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Umožňuje restartovat instance role. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Získá skladovou položku role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/write | Přidá roli slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/start/action | Umožňuje spustit slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Umožňuje změnit stav slotu pro nasazení na Zastavený. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Umožňuje změnit stav slotu pro nasazení na Spuštěný. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/stop/action | Umožňuje pozastavit slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Umožňuje upgradovat doménu. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Ověří migraci slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/write | Umožňuje vytvořit nebo aktualizovat slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/swap/action | Umožňuje přepnout přípravný slot na produkční. |
> | Akce | Microsoft.ClassicCompute/domainNames/write | Umožňuje přidat nebo změnit názvy domén pro prostředky. |
> | Akce | Microsoft.ClassicCompute/moveSubscriptionResources/action | Přesune všechny klasické prostředky do jiného předplatného. |
> | Akce | Microsoft.ClassicCompute/operatingSystemFamilies/read | Vypíše seznam řad hostovaných operačních systémů, které jsou dostupné v Microsoft Azure, a taky vypíše verze operačních systémů dostupné pro každou řadu. |
> | Akce | Microsoft.ClassicCompute/operatingSystems/read | Vypíše seznam verzí hostovaných operačních systémů, které jsou aktuálně dostupné v Microsoft Azure. |
> | Akce | Microsoft.ClassicCompute/operations/read | Získá seznam operací. |
> | Akce | Microsoft.ClassicCompute/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.ClassicCompute/quotas/read | Umožňuje načíst kvótu pro předplatné. |
> | Akce | Microsoft.ClassicCompute/register/action | Registrovat pro klasické výpočetní prostředky |
> | Akce | Microsoft.ClassicCompute/resourceTypes/skus/read | Načte seznam skladových položek pro podporované typy prostředků. |
> | Akce | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Ověří dostupnost klasické operace přesunu u předplatného. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě spojenou s virtuálním počítačem. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupiny zabezpečení sítě spojené s virtuálními počítači. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě spojenou s virtuálním počítačem. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě spojenou s virtuálním počítačem. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Umožňuje získat seznam možných asynchronních operací. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Umožňuje připojit datový disk k virtuálnímu počítači. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/capture/action | Zachytí virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/delete | Umožňuje odebrat virtuální počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Umožňuje odpojit datový disk od virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Získá nastavení diagnostiky virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/disks/read | Načte seznam datových disků |
> | Akce | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Umožňuje stáhnout soubor RDP pro virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Přečte stav operace pro rozšíření virtuálních počítačů. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/read | Umožňuje načíst rozšíření virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/write | Umožňuje vložit rozšíření virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Získá definici metriky virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/metrics/read | Umožňuje načíst metriky. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě spojenou se síťovým rozhraním. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupiny zabezpečení sítě spojené s virtuálními počítači. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě spojenou se síťovým rozhraním. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě spojenou se síťovým rozhraním. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Přečte stav operace pro virtuální počítače |
> | Akce | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Provede údržbu na virtuálním počítači. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Umožňuje načíst nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Umožňuje přidat nebo změnit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Umožňuje načíst definice metrik. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/read | Umožňuje načíst seznam virtuálních počítačů. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Znovu nasadí virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/restart/action | Umožňuje restartovat virtuální počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Umožňuje vypnout virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/start/action | Umožňuje spustit virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/stop/action | Umožňuje zastavit virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/write | Umožňuje přidat nebo upravit virtuální počítače. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Získá stav operace vzájemného propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Odstraní peering vzájemných propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Získá stav operace peeringu vzájemných propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Získá peering vzájemných propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Přidá peering vzájemných propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Získá vzájemná propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Přidá vzájemná propojení Express Route. |
> | Akce | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Umožňuje načíst seznam podporovaných zařízení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky skupiny zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky, tuto operaci doplňuje poskytovatel prostředků insights skupiny zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Načte události pro skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/read | Načte skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Odstraní pravidlo zabezpečení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Přečte stav operace pro pravidla zabezpečení skupiny u zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Načte pravidlo zabezpečení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Přidá nebo aktualizuje pravidlo zabezpečení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/write | Přidá novou skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/operations/read | Získá klasické síťové operace. |
> | Akce | Microsoft.ClassicNetwork/quotas/read | Umožňuje načíst kvótu pro předplatné. |
> | Akce | Microsoft.ClassicNetwork/register/action | Registrace pro klasickou síť |
> | Akce | Microsoft.ClassicNetwork/reservedIps/delete | Umožňuje odstranit vyhrazenou IP adresu. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/join/action | Připojit se k rezervované IP adrese |
> | Akce | Microsoft.ClassicNetwork/reservedIps/link/action | Odkaz na rezervovanou IP adresu |
> | Akce | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Přečte stav operace pro rezervované IP adresy. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/read | Umožňuje načíst vyhrazené IP adresy. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/write | Umožňuje přidat novou vyhrazenou IP adresu. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Zruší migraci virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Umožňuje zobrazit schopnosti. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Zkontroluje dostupnost dané IP adresy ve virtuální síti. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Potvrdí migraci virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/delete | Umožňuje odstranit virtuální síť. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Umožňuje zrušit odvolání klientského certifikátu. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Umožňuje načíst odvolané klientské certifikáty. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Umožňuje odvolat klientský certifikát. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Umožňuje odstranit klientský certifikát brány virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Umožňuje stáhnout certifikát podle kryptografického otisku. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Vypíše balíček certifikátů bran virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Umožňuje vyhledat klientské kořenové certifikáty. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Umožňuje nahrát nový klientský kořenový certifikát. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Umožňuje připojit lokalitu k připojení brány lokality. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Umožňuje odpojit lokalitu od připojení brány lokality. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Umožňuje načíst seznam připojení. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Umožňuje otestovat připojení lokality k bráně lokality. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Umožňuje odstranit bránu virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Umožňuje stáhnout skript pro konfiguraci zařízení. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Umožňuje stáhnout diagnostiku brány. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Umožňuje načíst klíč služby okruhů. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Umožňuje vypsat balíček bran virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Přečte stav operace pro brány virtuálních sítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Umožňuje načíst balíček bran virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Umožňuje načíst brány virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Umožňuje spustit diagnostiku pro bránu virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Umožňuje zastavit diagnostiku pro bránu virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Umožňuje přidat bránu virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/join/action | Připojí se k virtuální síti. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Přečte stav operace pro virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Vytvoří partnerskou virtuální síť s jinou virtuální sítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Připraví migraci virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/read | Umožňuje načíst virtuální síť. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Odstraní proxy server partnerského vztahu vzdálené virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Načte proxy server partnerského vztahu vzdálené virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Přidá nebo aktualizuje proxy server partnerského vztahu vzdálené virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě spojenou s podsítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupiny zabezpečení sítě spojenou podsítě virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě spojenou s podsítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě spojenou s podsítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Ověří migraci virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Načte partnerský vztah virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/write | Umožňuje přidat novou virtuální síť. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicStorage/capabilities/read | Umožňuje zobrazit schopnosti. |
> | Akce | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Umožňuje zkontrolovat dostupnost účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Zjistí dostupnost účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/disks/read | Vrátí disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/images/operationstatuses/read | Získá stav operace image. |
> | Akce | Microsoft.ClassicStorage/images/read | Vrátí image. |
> | Akce | Microsoft.ClassicStorage/operations/read | Získá klasické operace úložiště. |
> | Akce | Microsoft.ClassicStorage/osImages/read | Vrátí image operačního systému. |
> | Akce | Microsoft.ClassicStorage/osPlatformImages/read | Získá image platformy operačního systému. |
> | Akce | Microsoft.ClassicStorage/publicImages/read | Načte veřejnou image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/quotas/read | Umožňuje načíst kvótu pro předplatné. |
> | Akce | Microsoft.ClassicStorage/register/action | Umožňuje registraci v klasickém úložišti. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Přeruší migraci účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Potvrdí migraci účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/delete | Umožňuje odstranit účet úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/delete | Odstraní daný disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/read | Vrátí disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/write | Přidá disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/delete | Odstraní image účtu daného úložiště. (Zastaralé. Použití "Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Vrátí stav operace image účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí image účtu storage. (Zastaralé. Použití "Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Akce | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Odstraní danou image operačního systému pro účet Storage. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/read | Vrátí image operačního systému pro účet Storage. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/write | Přidá image operačního systému daného účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Připraví migraci účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Umožňuje znovu vytvořit existující přístupové klíče pro účet úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Umožňuje načíst nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Umožňuje přidat nebo změnit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Umožňuje načíst definice metrik. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Umožňuje načíst metriky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/read | Umožňuje načíst dostupné služby. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Ověří migraci účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Odstraní zadanou image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Získá stav operace image daného virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Vrátí image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Přidá zadanou image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/write | Umožňuje přidat nový účet úložiště. |
> | Akce | Microsoft.ClassicStorage/vmImages/read | Vypíše image virtuálních počítačů. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CognitiveServices/accounts/delete | Odstraní účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/accounts/listKeys/action | Zobrazit seznam klíčů |
> | Akce | Microsoft.CognitiveServices/accounts/read | Načte účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/accounts/regenerateKey/action | Znovu vygenerovat klíč |
> | Akce | Microsoft.CognitiveServices/accounts/skus/read | Načte dostupné skladové položky (SKU) pro existující prostředek. |
> | Akce | Microsoft.CognitiveServices/accounts/usages/read | Získá využití kvóty pro existující prostředek. |
> | Akce | Microsoft.CognitiveServices/accounts/write | Zapíše účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Přečte dostupná SKU pro předplatné. |
> | Akce | Microsoft.CognitiveServices/Operations/read | Vypíše všechny dostupné operace. |
> | Akce | Microsoft.CognitiveServices/register/action | Zaregistruje předplatné do služeb Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Commerce/RateCard/read | Vrátí nabízejí data, metadata prostředku/měřičů a účtovat sazby platné pro dané předplatné. |
> | Akce | Microsoft.Commerce/UsageAggregates/read | Načte předplatné Microsoft Azure consumption. Výsledek obsahuje agreguje data o využití, předplatné a prostředků související informace pro konkrétní časový rozsah. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Compute/availabilitySets/delete | Umožňuje odstranit sadu dostupnosti. |
> | Akce | Microsoft.Compute/availabilitySets/read | Umožňuje získat vlastnosti sady dostupnosti. |
> | Akce | Microsoft.Compute/availabilitySets/vmSizes/read | Umožňuje zobrazit seznam dostupných velikostí pro vytváření nebo aktualizaci virtuálního počítače v sadě dostupnosti. |
> | Akce | Microsoft.Compute/availabilitySets/write | Umožňuje vytvořit novou nebo aktualizovat stávající sadu dostupnosti. |
> | Akce | Microsoft.Compute/disks/beginGetAccess/action | Získat identifikátor URI SAS disku pro přístup k objektu blob |
> | Akce | Microsoft.Compute/disks/delete | Odstraní disky. |
> | Akce | Microsoft.Compute/disks/endGetAccess/action | Odvolá identifikátor URI SAS disku |
> | Akce | Microsoft.Compute/disks/read | Získá vlastnosti disku. |
> | Akce | Microsoft.Compute/disks/write | Vytvoří nový disk nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/galleries/delete | Odstraní galerii. |
> | Akce | Microsoft.Compute/galleries/images/delete | Odstraní image galerie. |
> | Akce | Microsoft.Compute/galleries/images/read | Získá vlastnosti image galerie. |
> | Akce | Microsoft.Compute/galleries/images/versions/delete | Odstraní verzi image galerie. |
> | Akce | Microsoft.Compute/galleries/images/versions/read | Získá vlastnosti verze image galerie. |
> | Akce | Microsoft.Compute/galleries/images/versions/write | Vytvoří novou verzi image galerie nebo aktualizuje nějakou existující. |
> | Akce | Microsoft.Compute/galleries/images/write | Vytvoří novou image galerie nebo aktualizuje nějakou existující. |
> | Akce | Microsoft.Compute/galleries/read | Získá vlastnosti galerie. |
> | Akce | Microsoft.Compute/galleries/write | Vytvoří novou galerii nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/images/delete | Odstraní image. |
> | Akce | Microsoft.Compute/images/read | Získá vlastnosti image. |
> | Akce | Microsoft.Compute/images/write | Vytvoří novou image nebo aktualizuje nějakou existující. |
> | Akce | Microsoft.Compute/locations/capsOperations/read | Umožňuje získat stav asynchronní operace Caps. |
> | Akce | Microsoft.Compute/locations/diskOperations/read | Umožňuje získat stav asynchronní operace disku |
> | Akce | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Pokud si chcete zobrazit celkový počet požadavků podle časových intervalů a snadněji tak diagnostikovat omezování, vytvořte protokoly. |
> | Akce | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Pokud si chcete zobrazit agregace omezených požadavků seskupených podle ResourceName, OperationName nebo použitých zásad omezování, vytvořte protokoly. |
> | Akce | Microsoft.Compute/locations/operations/read | Umožňuje získat stav asynchronní operace. |
> | Akce | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Získá vlastnosti nabídky image platformy. |
> | Akce | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Získá vlastnosti skladové položky image platformy. |
> | Akce | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Získá vlastnosti verze image platformy. |
> | Akce | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Získá vlastnosti typu VMExtension. |
> | Akce | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Získá vlastnosti verze VMExtension. |
> | Akce | Microsoft.Compute/locations/publishers/read | Získá vlastnosti vydavatele. |
> | Akce | Microsoft.Compute/locations/runCommands/read | Seznam dostupných spouštěcích příkazů v umístění |
> | Akce | Microsoft.Compute/locations/usages/read | Umožňuje získat limity služby a aktuální využívaná množství výpočetních prostředků předplatného v daném umístění. |
> | Akce | Microsoft.Compute/locations/vmSizes/read | Umožňuje zobrazit seznam dostupných velikostí virtuálních počítačů v daném umístění. |
> | Akce | Microsoft.Compute/operations/read | Umožňuje zobrazit seznam operací dostupných na zprostředkovateli prostředků Microsoft.Compute. |
> | Akce | Microsoft.Compute/register/action | Umožňuje registrovat předplatné zprostředkovatele prostředků Microsoft.Compute. |
> | Akce | Microsoft.Compute/restorePointCollections/delete | Odstraní kolekci bodů obnovení a v ní obsažené body obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/read | Získá vlastnosti kolekce bodů obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/delete | Odstraní bod obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/read | Získá vlastnosti bodu obnovení. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Získá vlastnosti bodu obnovení spolu s identifikátory URI SAS objektů blob. |
> | Akce | Microsoft.Compute/restorePointCollections/restorePoints/write | Vytvoří nový bod obnovení |
> | Akce | Microsoft.Compute/restorePointCollections/write | Vytvoří novou kolekci bodů obnovení nebo aktualizuje nějakou, která už existuje. |
> | Akce | Microsoft.Compute/sharedVMImages/delete | Odstraní SharedVMImage. |
> | Akce | Microsoft.Compute/sharedVMImages/read | Získá vlastnosti ShareVMImage. |
> | Akce | Microsoft.Compute/sharedVMImages/versions/delete | Odstraní SharedVMImageVersion. |
> | Akce | Microsoft.Compute/sharedVMImages/versions/read | Získá vlastnosti ShareVMImageVersion. |
> | Akce | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replikuje SharedVMImageVersion do cílových oblastí. |
> | Akce | Microsoft.Compute/sharedVMImages/versions/write | Vytvoří novou verzi SharedVMImageVersion nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/sharedVMImages/write | Vytvoří novou image SharedVMImage nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/skus/read | Získá seznam skladových položek Microsoft.Compute dostupných pro vaše předplatné. |
> | Akce | Microsoft.Compute/snapshots/beginGetAccess/action | Získat identifikátor URI SAS snímku pro přístup k objektu blob |
> | Akce | Microsoft.Compute/snapshots/delete | Odstranit snímek |
> | Akce | Microsoft.Compute/snapshots/endGetAccess/action | Odvolá identifikátor URI SAS snímku |
> | Akce | Microsoft.Compute/snapshots/read | Získat vlastnosti snímku |
> | Akce | Microsoft.Compute/snapshots/write | Vytvoří nový snímek nebo aktualizuje stávající. |
> | Akce | Microsoft.Compute/virtualMachines/capture/action | Umožňuje zachytit virtuální počítač zkopírováním virtuálních pevných disků a vygenerováním šablony, kterou je možné použít k vytvoření podobných virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Převede disky virtuálního počítače založené na objektech blob na spravované disky. |
> | Akce | Microsoft.Compute/virtualMachines/deallocate/action | Umožňuje vypnout virtuální počítač a uvolnit výpočetní prostředky. |
> | Akce | Microsoft.Compute/virtualMachines/delete | Umožňuje odstranit virtuální počítač. |
> | Akce | Microsoft.Compute/virtualMachines/extensions/delete | Umožňuje odstranit rozšíření virtuálního počítače. |
> | Akce | Microsoft.Compute/virtualMachines/extensions/read | Umožňuje získat vlastnosti rozšíření virtuálního počítače. |
> | Akce | Microsoft.Compute/virtualMachines/extensions/write | Umožňuje vytvořit nové nebo aktualizovat stávající rozšíření virtuálního počítače. |
> | Akce | Microsoft.Compute/virtualMachines/generalize/action | Umožňuje nastavit stav virtuálního počítače na Zobecněno a připravit virtuální počítač na zachycení. |
> | Akce | Microsoft.Compute/virtualMachines/instanceView/read | Umožňuje získat podrobný stav runtime virtuálního počítače a jeho prostředků. |
> | Hodnotu DataAction | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
> | Hodnotu DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Přihlášení k virtuálnímu počítači s oprávněními správce ve Windows nebo kořenovými oprávněními na Linuxu |
> | Akce | Microsoft.Compute/virtualMachines/performMaintenance/action | Provádí operace údržby ve virtuálním počítači. |
> | Akce | Microsoft.Compute/virtualMachines/powerOff/action | Umožňuje vypnout virtuální počítač. Všimněte si, že virtuální počítač se bude dál účtovat. |
> | Akce | Microsoft.Compute/virtualMachines/read | Umožňuje získat vlastnosti virtuálního počítače. |
> | Akce | Microsoft.Compute/virtualMachines/redeploy/action | Znovu nasadí virtuální počítač. |
> | Akce | Microsoft.Compute/virtualMachines/reimage/action | Obnoví virtuální počítač, který používá rozdílový disk, z image. |
> | Akce | Microsoft.Compute/virtualMachines/restart/action | Umožňuje restartovat virtuální počítač. |
> | Akce | Microsoft.Compute/virtualMachines/runCommand/action | Spustí na virtuálním počítači předem definovaný skript. |
> | Akce | Microsoft.Compute/virtualMachines/start/action | Umožňuje spustit virtuální počítač. |
> | Akce | Microsoft.Compute/virtualMachines/vmSizes/read | Umožňuje zobrazit seznam dostupných velikostí, na které je možné virtuální počítač aktualizovat. |
> | Akce | Microsoft.Compute/virtualMachines/write | Umožňuje vytvořit nový nebo aktualizovat stávající virtuální počítač. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Umožňuje vypnout instance škálovací sady virtuálních počítačů a uvolnit výpočetní prostředky, které tyto instance zabíraly.  |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/delete | Umožňuje odstranit škálovací sadu virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/delete/action | Umožňuje odstranit instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Odstraní rozšíření škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Načte vlastnosti rozšíření škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Vytvoří nové rozšíření škálovací sady virtuálních počítačů nebo aktualizuje existující rozšíření. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Umožňuje ručně projít aktualizační domény platformy ve škálovací sadě virtuálních počítačů Service Fabric, aby se dokončily zablokované čekající aktualizace. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Umožňuje získat zobrazení instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Umožňuje ručně aktualizovat instance na poslední model škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Získá vlastnosti všech síťových rozhraní škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Zahájí upgrade se zajištěním provozu, který přesune všechny instance škálovacích sad virtuálních počítačů na nejnovější dostupnou verzi operačního systému image platformy. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Získat historii upgradů operačního systému pro škálovací sadu virtuálních počítačů |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Provede plánovanou údržbu instancí škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Umožňuje vypnout instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Získá vlastnosti všech veřejných IP adres škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/read | Umožňuje získat vlastnosti škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Znovu nasadí instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Umožňuje obnovit instance škálovací sady virtuálních počítačů z image. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Obnoví z image všechny disky (disk s operačním systémem a datové disky) pro instance škálovací sady virtuálních počítačů.  |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/restart/action | Umožňuje restartovat instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Zruší upgrade škálovací sady virtuálních počítačů se zajištěním provozu. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Získá nejnovější stav upgradu škálovací sady virtuálních počítačů se zajištěním provozu. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/scale/action | Ověřte, že existující škálovací sada virtuálních počítačů může horizontálně snížit nebo navýšit kapacitu na zadaný počet instancí. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/skus/read | Umožňuje zobrazit seznam platných SKU pro existující škálovací sadu virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/start/action | Umožňuje spustit instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Umožňuje vypnout a uvolnit výpočetní prostředky pro virtuální počítač ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Umožňuje odstranit konkrétní virtuální počítač ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Umožňuje načíst zobrazení instance virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Získá vlastnosti veřejné IP adresy vytvořené pomocí Škálovací sady virtuálních počítačů. Škálovací sada virtuálních počítačů můžete vytvořit nanejvýš jednu veřejnou IP adresu jednu konfiguraci protokolu IP (privátní IP adresa) |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Získá vlastnosti jednoho nebo všech konfigurací protokolu IP síťového rozhraní vytvořená pomocí Škálovací sady virtuálních počítačů. Konfigurace protokolu IP představují privátní IP adresy |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Získá vlastnosti jednoho nebo všech síťových rozhraní virtuálního počítače vytvořených pomocí škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Provede plánovanou údržbu instance virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Umožňuje vypnout instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Umožňuje načíst vlastnosti virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Znovu nasadí instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Obnoví instanci virtuálního počítače ve škálovací sadě virtuálních počítačů z image. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Obnoví z image všechny disky (disk s operačním systémem a datové disky) pro instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Umožňuje restartovat instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Spustí předem definovaný skript v instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Umožňuje spustit instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Umožňuje aktualizovat vlastnosti virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/write | Umožňuje vytvořit novou nebo aktualizovat stávající škálovací sadu virtuálních počítačů. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Consumption/balances/read | Seznam využití souhrnu pro fakturační období pro skupinu pro správu. |
> | Akce | Microsoft.Consumption/budgets/read | Seznam rozpočty předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/budgets/write | Vytvoří, aktualizovat a odstraňovat rozpočty předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/marketplaces/read | Zobrazí podrobnosti o prostředku využití webu marketplace pro rozsah předplatných EA a vytvořené přímo na webu. |
> | Akce | Microsoft.Consumption/operations/read | Seznam všech podporovaných operací přes Microsoft.Consumption poskytovatele prostředků. |
> | Akce | Microsoft.Consumption/pricesheets/read | Seznam dat Pricesheets pro předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/reservationDetails/read | Zobrazí podrobnosti o využití pro rezervované instance podle pořadí nebo správu skupin rezervace. Podrobnosti o data jsou na instanci za den, úroveň. |
> | Akce | Microsoft.Consumption/reservationRecommendations/read | Seznam jedné nebo sdílené doporučení pro rezervované instance pro předplatné. |
> | Akce | Microsoft.Consumption/reservationSummaries/read | Zobrazí souhrn pro rezervované instance podle pořadí nebo správu skupin rezervace využití. Souhrnná data je buď na úrovni denní nebo měsíční. |
> | Akce | Microsoft.Consumption/reservationTransactions/read | Seznam historie transakcí pro rezervované instance podle skupiny pro správu. |
> | Akce | Microsoft.Consumption/tenants/register/action | Zaregistrujte akci pro obor Microsoft.Consumption klientem. |
> | Akce | Microsoft.Consumption/terms/read | Seznam podmínek pro předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/usageDetails/read | Zobrazí podrobnosti o využití pro daný obor předplatného EA a vytvořené přímo na webu. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Získá protokoly pro konkrétní kontejner. |
> | Akce | Microsoft.ContainerInstance/containerGroups/delete | Odstraní konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky skupiny kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky skupiny kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/read | Získá všechny skupiny kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/restart/action | Restartuje konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/start/action | Spustí konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/containerGroups/stop/action | Zastaví konkrétní skupinu kontejnerů. Výpočty se uvolní prostředky a fakturace se zastaví. |
> | Akce | Microsoft.ContainerInstance/containerGroups/write | Vytvoří nebo aktualizuje konkrétní skupinu kontejnerů. |
> | Akce | Microsoft.ContainerInstance/register/action | Zaregistruje předplatné pro zprostředkovatele prostředku instance kontejneru a povolí vytvoření skupin kontejnerů. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontroluje, zda je k dispozici pro použití název registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Upozorní Microsoft.ContainerRegistry, že se odstraňuje virtuální síť nebo podsíť |
> | Akce | Microsoft.ContainerRegistry/locations/operationResults/read | Získá výsledek asynchronní operace |
> | Akce | Microsoft.ContainerRegistry/operations/read | Uvádí všechny dostupné operace REST API služby Azure Container Registry |
> | Akce | Microsoft.ContainerRegistry/register/action | Zaregistruje předplatné u poskytovatele prostředků registru kontejneru a povolí vytváření registry kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/builds/cancel/action | Zruší existující sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Získá odkaz ke stažení protokolů o sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/builds/read | Načte vlastnosti zadaného sestavení nebo vypíše všechna sestavení pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/builds/write | Aktualizuje sestavení pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/delete | Odstraní úlohu sestavení z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Je uveden seznam vlastností ovládacího prvku zdroje pro úlohu sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/read | Získá vlastnosti úlohy zadaného sestavení nebo vypíše všechny úlohy buildu pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Odstraní krok sestavení úlohu sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Určuje argumenty sestavení, které kroku sestavení, včetně tajných kódů argumentů. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Získá vlastnosti kroku zadaného sestavení nebo jsou uvedené všechny kroky sestavení pro úlohu zadané sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Vytvoří nebo aktualizuje kroku sestavení pro úlohu sestavení se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/buildTasks/write | Vytvoří nebo aktualizuje úlohu sestavení pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/delete | Odstraní registru kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Odstraní filtr event grid z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Získá vlastnosti filtru mřížky zadanou událost nebo obsahuje seznam všech filtrů event grid pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Vytvoří nebo aktualizuje filtr event grid pro registr kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Získá umístění nahrávání pro uživatele, aby byla možné nahrávat zdroji. |
> | Akce | Microsoft.ContainerRegistry/registries/importImage/action | Importovat Image do registru kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Získání zdroje nahrát umístění adresy url pro službu container registry. |
> | Akce | Microsoft.ContainerRegistry/registries/listCredentials/action | Seznam přihlašovacích údajů pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/listPolicies/read | Seznam zásad pro zadaný registr kontejneru |
> | Akce | Microsoft.ContainerRegistry/registries/listUsages/read | Seznam využití kvóty pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/operationStatuses/read | Získá stav asynchronní operace registru |
> | Akce | Microsoft.ContainerRegistry/registries/pull/read | O přijetí změn nebo získat Image z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/push/write | Vložit nebo zapíšete Image do registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/quarantineRead/read | O přijetí změn nebo získat v karanténě imagí z registru kontejneru |
> | Akce | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Zápis nebo upravit stav karantény v karanténě imagí |
> | Akce | Microsoft.ContainerRegistry/registries/queueBuild/action | Vytvoří nové sestavení na základě parametrů žádosti a přidejte ji do fronty sestavení. |
> | Akce | Microsoft.ContainerRegistry/registries/read | Získá vlastnosti zadaný registr kontejneru nebo vypíše všechny registry kontejnerů v zadané skupině prostředků nebo předplatného. |
> | Akce | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Znovu vygeneruje jednu přihlašovací údaje pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/delete | Odstraní replikaci z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Získá stav asynchronní operace replikace |
> | Akce | Microsoft.ContainerRegistry/registries/replications/read | Načte vlastnosti zadaného replikace nebo vypíše všechny replikace pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/write | Vytvoří nebo aktualizuje replikace pro službu container registry se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/cancel/action | Zrušte existující spuštění. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Získá protokolu SAS URL pro spuštění. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/read | Získá vlastnosti spustit proti registru kontejneru nebo seznam spuštění. |
> | Akce | Microsoft.ContainerRegistry/registries/runs/write | Aktualizace spuštění. |
> | Akce | Microsoft.ContainerRegistry/registries/scheduleRun/action | Naplánujte spustit proti registru kontejnerů. |
> | Akce | Microsoft.ContainerRegistry/registries/sign/write | Vyžádanou metadata obsahu vztahu důvěryhodnosti pro službu container registry. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/delete | Odstraní úlohu pro službu container registry. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Vypsat všechny podrobnosti úlohy pro službu container registry. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/read | Získá úkol pro registr kontejneru nebo seznam všech úloh. |
> | Akce | Microsoft.ContainerRegistry/registries/tasks/write | Vytvoří nebo aktualizuje úlohu pro službu container registry. |
> | Akce | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualizace zásad pro zadaný registr kontejneru |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/delete | Odstraní webhook z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Získá konfiguraci identifikátor URI služby a vlastní hlavičky pro webhook. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Seznamy nedávné události pro zadaný webhooku. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Získá stav asynchronní operace webhooku |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Spustí příkaz ping událost k odeslání do webhooku. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/read | Načte vlastnosti zadaného webhook nebo vypíše všechny webhooky pro zadaný registr kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/write | Vytvoří nebo aktualizuje webhook registru kontejneru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/write | Vytvoří nebo aktualizuje registr kontejneru se zadanými parametry. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerService/containerServices/delete | Odstraní službu kontejneru. |
> | Akce | Microsoft.ContainerService/containerServices/read | Získat službu kontejneru |
> | Akce | Microsoft.ContainerService/containerServices/write | Vytvoří novou službu kontejneru nebo aktualizuje stávající. |
> | Akce | Microsoft.ContainerService/locations/operationresults/read | Umožňuje získat stav výsledku asynchronní operace. |
> | Akce | Microsoft.ContainerService/locations/operations/read | Umožňuje získat stav asynchronní operace. |
> | Akce | Microsoft.ContainerService/locations/orchestrators/read | Vypíše podporované orchestrátory. |
> | Akce | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Získat profil přístupu spravovaného clusteru podle názvu role pomocí přihlašovacích údajů seznamu |
> | Akce | Microsoft.ContainerService/managedClusters/accessProfiles/read | Získat profil přístupu spravovaného clusteru podle názvu role |
> | Akce | Microsoft.ContainerService/managedClusters/delete | Odstraní spravovaný cluster. |
> | Akce | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Vypsat přihlašovací údaje správce spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Vypsat přihlašovací údaje uživatele spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/read | Získat spravovaný cluster |
> | Akce | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Získá profil upgradu clusteru. |
> | Akce | Microsoft.ContainerService/managedClusters/write | Vytvoří nový spravovaný cluster nebo aktualizuje stávající. |
> | Akce | Microsoft.ContainerService/openShiftClusters/delete | Odstranění clusteru Open Shift |
> | Akce | Microsoft.ContainerService/openShiftClusters/read | Získat Cluster Open Shift |
> | Akce | Microsoft.ContainerService/openShiftClusters/write | Vytvoří nový Cluster Open Shift nebo aktualizuje nějakou existující |
> | Akce | Microsoft.ContainerService/openShiftManagedClusters/delete | Odstranění clusteru spravované Open Shift |
> | Akce | Microsoft.ContainerService/openShiftManagedClusters/read | Získejte spravovaný Cluster Open Shift |
> | Akce | Microsoft.ContainerService/openShiftManagedClusters/write | Vytvoří nové Open Shift spravovat Cluster nebo aktualizuje nějakou existující |
> | Akce | Microsoft.ContainerService/operations/read | Vypíše seznam operací dostupných v poskytovateli prostředků Microsoft.ContainerService. |
> | Akce | Microsoft.ContainerService/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft.ContainerService. |
> | Akce | Microsoft.ContainerService/unregister/action | Zruší registraci předplatného poskytovatele prostředků Microsoft.ContainerService. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContentModerator/applications/delete | Operace odstranění |
> | Akce | Microsoft.ContentModerator/applications/listSecrets/action | Uvést tajné klíče |
> | Akce | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Přečtěte si jednotné přihlašování pro tokeny |
> | Akce | Microsoft.ContentModerator/applications/read | Operace čtení |
> | Akce | Microsoft.ContentModerator/applications/write | Operace zápisu |
> | Akce | Microsoft.ContentModerator/applications/write | Operace zápisu |
> | Akce | Microsoft.ContentModerator/listCommunicationPreference/action | Uvést předvolby komunikace |
> | Akce | Microsoft.ContentModerator/operations/read | operace čtení |
> | Akce | Microsoft.ContentModerator/updateCommunicationPreference/action | Aktualizovat předvolby komunikace |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CostManagement/dimensions/read | Seznam všech podporovaných dimenze oborem. |
> | Akce | Microsoft.CostManagement/query/action | Dotazování dat využití oborem. |
> | Akce | Microsoft.CostManagement/query/read | Dotazování dat využití oborem. |
> | Akce | Microsoft.CostManagement/reports/action | Plán sestavy o využití dat oborem. |
> | Akce | Microsoft.CostManagement/reports/read | Plán sestavy o využití dat oborem. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CustomerInsights/hubs/adobemetadata/action | Vytvořit nebo aktualizovat libovolný zákazník Azure Insights Adobe metadat |
> | Akce | Microsoft.CustomerInsights/hubs/adobemetadata/read | Čtení všech zákazníků Azure Insights Adobe metadat |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Odstranění všech zákazníků Azure Insights sdílený přístupový podpis zásad |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Přečíst jakýkoli Azure Customer Insights sdílené zásady přístupu podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Znova vygenerovat primární klíč Azure zákazníka Insights zásada sdíleného přístupu. podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Znova vygenerovat sekundární klíč Azure zákazníka Insights zásada sdíleného přístupu. podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Vytvořit nebo aktualizovat všechny zásady zákazníků Azure Insights sdílený přístupový podpis |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivovat libovolný Insights Connector zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivovat libovolný Insights Connector zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/delete | Odstranit všechny zákazníky Azure Insights Connector |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Získat stav modulu runtime libovolný Insights Connector zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktivovat žádnému mapování zákazníků Azure Insights Connector |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Odstranit všechny zákazníků Azure Insights Connector mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Načíst výsledek operace žádné mapování konektoru přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Číst všechny zákazníků Azure Insights Connector mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Vytvořit nebo aktualizovat libovolný zákazník Azure Insights konektor mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/operations/read | Načíst výsledek operace libovolný Insights Connector zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/read | Číst všechny zákazníky Azure Insights Connector |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Vytvořit nebo aktualizovat k bráně jakékoli zákazníků Azure Insights Connector ověřování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/update/action | Aktualizovat všechny zákazníky Azure Insights Connector |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/write | Vytvořit nebo aktualizovat libovolný Insights Connector zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/crmmetadata/action | Vytvořit nebo aktualizovat libovolný zákazník Azure Insights Crm metadat |
> | Akce | Microsoft.CustomerInsights/hubs/crmmetadata/read | Čtení všech zákazníků Azure Insights Crm metadat |
> | Akce | Microsoft.CustomerInsights/hubs/delete | Odstranit libovolný Insights Centrum zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/gdpr/delete | Odstranit všechny zákazníky Azure Insights Gdpr |
> | Akce | Microsoft.CustomerInsights/hubs/gdpr/read | Číst libovolný Insights Gdpr zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/gdpr/write | Vytvořit nebo aktualizovat libovolný Insights Gdpr zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Získejte kredity fakturace centra přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Získejte přehledy zákazníků Azure, Centrum historie fakturace |
> | Akce | Microsoft.CustomerInsights/hubs/images/delete | Odstranit všechny Image zákazníků Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/images/read | Číst libovolný Insights obrázek zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/images/write | Vytvořit nebo aktualizovat libovolný Insights Image zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/delete | Odstranit všechny azure Insights interakce se zákazníky |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/operations/read | Přečíst výsledky operace interakci přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/read | Přečtěte si zásahu zákazníka v systému Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Navrhnout vztahů propojení pro všechny zákazníky Azure Insights interakce |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/write | Vytvořit nebo aktualizovat libovolný Insights interakce zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/delete | Odstranit všechny zákazníky Azure Insights klíčový ukazatel výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/operations/read | Načíst výsledek operace všechny zákazníky Azure Insights klíčové ukazatele výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/read | Číst všechny zákazníky Azure Insights klíčový ukazatel výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Znovu zpracovat všechny zákazníky Azure Insights klíčových ukazatelů výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/write | Vytvořit nebo aktualizovat libovolný zákazník Azure Insights klíčový ukazatel výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/links/delete | Odstranit všechny odkazy přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/links/operations/read | Načíst výsledek operace žádné odkazy přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/links/read | Čtení všech zákazníků Azure Insights odkazů |
> | Akce | Microsoft.CustomerInsights/hubs/links/write | Vytvořit nebo aktualizovat všechny odkazy zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/msemetadata/action | Vytvořit nebo aktualizovat libovolný zákazník Azure Insights Mse metadat |
> | Akce | Microsoft.CustomerInsights/hubs/msemetadata/read | Čtení všech zákazníků Azure Insights Mse metadat |
> | Akce | Microsoft.CustomerInsights/hubs/operationresults/read | Načíst výsledky operace centra zákazníka v systému Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/delete | Odstranit libovolný Insights Predikcí zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/operations/read | Přečtěte si některý z výsledků operace Predikcí přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/read | Čtení všech zákazníků Azure Insights Predikcí |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/write | Vytvořit nebo aktualizovat všechny zákazníky Azure Predikcí |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Odstranit všechny zákazníky Azure Insights prediktivní shoda zásady |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Načíst výsledek operace libovolný zákazník Azure Insights prediktivní shoda zásady |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Číst všechny zákazníky Azure Insights prediktivní shoda zásady |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Vytvořit nebo aktualizovat všechny zákazníky Azure Insights prediktivní shoda zásady |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/delete | Odstranit žádný zákazník Azure Insights profil |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/operations/read | Přečtěte si některý z výsledků operace profil přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/read | Čtení všech zákazníků Azure Insights profilu |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/write | Žádný zákazník Azure Insights profil zápisu |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro prostředek |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro prostředek |
> | Akce | Microsoft.CustomerInsights/hubs/read | Přečtěte si každý zákazník Azure Insights rozbočovač |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Odstranit všechny odkazy vztah zákazníků Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Načíst výsledek operace žádné odkazy vztah přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Čtení všech zákazníků Azure Insights vztah odkazů |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Vytvořit nebo aktualizovat odkazy vztah zákazníků Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/delete | Odstraňte všechny zákazníky Azure Insights relace |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/operations/read | Načíst výsledek operace libovolný Insights vztahů se zákazníky Azure |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/read | Číst všechny vztahy zákazníků Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/write | Vytvořit nebo aktualizovat libovolný Insights relace zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Odstranit přiřazení zákazníků Azure Insights Rbac |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Přečíst výsledky operace přiřazení Rbac přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/read | Přečtěte si jakémukoliv Svalování zákazníků Azure Insights Rbac |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/write | Vytvořit nebo aktualizovat libovolný zákazník Azure Insights Rbac přiřazení |
> | Akce | Microsoft.CustomerInsights/hubs/roles/read | Číst všechny role Rbac zákazníků Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Vytvořit nebo aktualizovat veškerá Metadata zákazníků Azure Insights SalesForce |
> | Akce | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Čtení všech zákazníků Azure Insights SalesForce metadat |
> | Akce | Microsoft.CustomerInsights/hubs/segments/delete | Odstranit všechny segmenty zákazníků Azure Insights |
> | Akce | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmenty všech zákazníků Azure Insight dynamické správy |
> | Akce | Microsoft.CustomerInsights/hubs/segments/read | Číst libovolný Insights segmentů zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/segments/static/action | Správa segmenty všechny statické přehledy zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/segments/write | Vytvořit nebo aktualizovat libovolný Insights segmentů zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Odstranit libovolný Insights SqlConnectionStrings zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Číst libovolný Insights SqlConnectionStrings zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Vytvořit nebo aktualizovat libovolný Insights SqlConnectionStrings zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generování navrhnout typ schématu ze vzorových dat |
> | Akce | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Spravovat nastavení centra Azure Customer Insights |
> | Akce | Microsoft.CustomerInsights/hubs/views/delete | Odstranit všechna zobrazení App Insights zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/views/read | Čtení všech zobrazeních App Insights zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/views/write | Vytvořit nebo aktualizovat všechna zobrazení App Insights zákazníka v systému Azure |
> | Akce | Microsoft.CustomerInsights/hubs/widgettypes/read | Číst všechny typy widgetu App Insights zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/hubs/write | Vytvořit nebo aktualizovat libovolný Insights centra zákazníků Azure |
> | Akce | Microsoft.CustomerInsights/operations/read | Přečtěte si zákazník Azure Insights Api Metadatas |
> | Akce | Microsoft.CustomerInsights/register/action | Zaregistruje předplatné u poskytovatele prostředků Customer Insights a povolí vytváření prostředků Customer Insights |
> | Akce | Microsoft.CustomerInsights/unregister/action | Zruší registraci předplatného u poskytovatele prostředků Customer Insights |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Umožňuje zarezervovat vyzvednutí zásilky, která se má vrátit. |
> | Akce | Microsoft.DataBox/jobs/cancel/action | Zruší probíhající objednávku. |
> | Akce | Microsoft.DataBox/jobs/delete | Odstranit objednávky |
> | Akce | Microsoft.DataBox/jobs/listCredentials/action | Vypíše nešifrované přihlašovací údaje související s objednávkou. |
> | Akce | Microsoft.DataBox/jobs/read | Vypsat nebo získat objednávky |
> | Akce | Microsoft.DataBox/jobs/write | Vytvořit nebo aktualizovat objednávky |
> | Akce | Microsoft.DataBox/locations/availableSkus/action | Tato metoda vrací seznam dostupných skladových položek. |
> | Akce | Microsoft.DataBox/locations/validateAddress/action | Ověří dodací adresu a nabídne alternativní adresy, pokud jsou nějaké k dispozici. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Obsahuje seznam nebo získá upozornění |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Obsahuje seznam nebo získá upozornění |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Odstraní plány šířky pásma |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Obsahuje seznam nebo získá plány šířky pásma |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Obsahuje seznam nebo získá plány šířky pásma |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Vytvoří nebo aktualizuje plány šířky pásma |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Odstraní Data Box hraničních zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Stáhnout aktualizace v zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Načte prostředek rozšířené informace |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/write | Vytvoří nebo aktualizuje prostředek rozšířené informace |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalace aktualizací na zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Obsahuje seznam nebo získá úlohy |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Obsahuje seznam nebo získá nastavení síťového zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Obsahuje seznam nebo získá Data Box hraničních zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Obsahuje seznam nebo získá Data Box hraničních zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Obsahuje seznam nebo získá Data Box hraničních zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Odstraní ArmApiRes_roles |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Obsahuje seznam nebo získá ArmApiRes_roles |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Obsahuje seznam nebo získá ArmApiRes_roles |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Vytvoří nebo aktualizuje ArmApiRes_roles |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Vyhledat aktualizace |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aktualizovat nastavení zabezpečení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Odstraní sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Obsahuje seznam nebo získá sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Obsahuje seznam nebo získá sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | ArmApiDesc_action_refresh_shares |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Vytvoří nebo aktualizuje sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Odstraní přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Obsahuje seznam nebo získá přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Obsahuje seznam nebo získá přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Vytvoří nebo aktualizuje přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Obsahuje seznam nebo získá aktualizace souhrnu |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Nahrání certifikátu pro registraci zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Odstraní uživatele sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Obsahuje seznam nebo získá uživatele sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Obsahuje seznam nebo získá uživatele sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Vytvoří nebo aktualizuje uživatele sdílené složky |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Vytvoří nebo aktualizuje Data Box hraničních zařízení |
> | Akce | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Vytvoří nebo aktualizuje Data Box hraničních zařízení |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Databricks/register/action | Provede registraci v Databricks. |
> | Akce | Microsoft.Databricks/workspaces/delete | Odebere pracovní prostor Databricks. |
> | Akce | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Nastaví dostupná nastavení diagnostiky pro pracovní prostor Databricks. |
> | Akce | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Umožňuje přidat nebo změnit nastavení diagnostiky. |
> | Akce | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné definice protokolu pro pracovní prostor Databricks. |
> | Akce | Microsoft.Databricks/workspaces/read | Načte seznam pracovních prostorů Databricks. |
> | Akce | Microsoft.Databricks/workspaces/write | Vytvoří pracovní prostor Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataCatalog/catalogs/delete | Odstraní katalogu. |
> | Akce | Microsoft.DataCatalog/catalogs/read | Získání vlastností pro katalogu nebo katalozích v rámci předplatného nebo skupiny prostředků. |
> | Akce | Microsoft.DataCatalog/catalogs/write | Katalog vytvoří nebo aktualizuje značek a vlastnosti pro katalog. |
> | Akce | Microsoft.DataCatalog/checkNameAvailability/action | Zkontroluje dostupnost názvu katalogu pro tenanta. |
> | Akce | Microsoft.DataCatalog/operations/read | Zobrazit seznam operací dostupných na zprostředkovateli prostředků Microsoft.DataCatalog. |
> | Akce | Microsoft.DataCatalog/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft.DataCatalog. |
> | Akce | Microsoft.DataCatalog/unregister/action | Zruší registraci předplatného od zprostředkovatele prostředků Microsoft.DataCatalog. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataFactory/datafactories/activitywindows/read | Přečte aktivity Windows ve službě Data Factory se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Přečte aktivity Windows pro aktivity kanálů pomocí zadaných parametrů. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Přečte Windows aktivity pro kanál se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/delete | Odstraní všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pozastaví libovolného kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/read | Přečte všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Obnoví libovolného kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/write | Vytvoří nebo aktualizuje libovolného kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Přečte aktivity Windows pro datové sady se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/delete | Odstraní všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/read | Načte všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Přečte Data řez spuštění pro danou datovou sadu s danou počáteční čas. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/slices/read | Získá datové řezy v dané období. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizujte stav datový řez. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/write | Vytvoří nebo aktualizuje všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/delete | Odstraní Data Factory. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Načte informace o připojení pro každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/delete | Odstraní všechny brány. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Seznam ověřovací klíče pro každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/read | Přečte každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Znovu vygeneruje ověřovací klíče pro každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/write | Vytvoří nebo aktualizuje každou bránu. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/delete | Odstraní všechny propojené služby. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/read | Přečte všechny propojené služby. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/write | Vytvoří nebo aktualizuje všechny propojené služby. |
> | Akce | Microsoft.DataFactory/datafactories/read | Přečte Data Factory. |
> | Akce | Microsoft.DataFactory/datafactories/runs/loginfo/read | Načte identifikátor URI SAS pro kontejner objektů blob, který obsahuje protokoly. |
> | Akce | Microsoft.DataFactory/datafactories/tables/delete | Odstraní všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/tables/read | Načte všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/tables/write | Vytvoří nebo aktualizuje všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/write | Vytvoří nebo aktualizuje Data Factory. |
> | Akce | Microsoft.DataFactory/factories/cancelpipelinerun/action | Zruší spuštění podle ID spuštění kanálu |
> | Akce | Microsoft.DataFactory/factories/datasets/delete | Odstraní všechny datové sady. |
> | Akce | Microsoft.DataFactory/factories/datasets/read | Načte všechny datové sady. |
> | Akce | Microsoft.DataFactory/factories/datasets/write | Vytvoří nebo aktualizuje všechny datové sady. |
> | Akce | Microsoft.DataFactory/factories/delete | Odstraní Data Factory. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/delete | Odstraní všechny prostředí Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Načte informace o připojení modulu Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Přečte stav modulu Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Seznam ověřovací klíče pro libovolný modul Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Získá Data monitorování pro libovolný modul Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Odstraní uzel zadané prostředí Integration runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Vrátí IP adresu pro zadaný uzel Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Přečte uzel zadané prostředí Integration runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje v místním prostředí uzel Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/queryactivityruns/action | Zadané prostředí Integration runtime se dotazuje spuštění aktivit. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/read | Přečte všechny prostředí Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Znovu vygeneruje ověřovací klíče pro zadaný modul Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Odebere ze zadaného modulu Runtime integrace propojené odkazy modulu Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/start/action | Spustí všechny prostředí Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Zastaví všechny prostředí Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronizuje přihlašovací údaje zadané prostředí Integration runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Upgraduje se zadané prostředí Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/write | Vytvoří nebo aktualizuje všechny prostředí Integration Runtime. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/delete | Odstraní propojenou službu. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/read | Čtení propojenou službu. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/write | Vytvořit nebo aktualizovat propojené služby |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Čte se že spuštění aktivit pro zadané ID spuštění kanálu |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Zruší spuštění podle ID spuštění kanálu |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | ID spuštění dotazů, které spustí aktivitu pro zadaný kanál. |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Čte se že výsledek dotazu aktivity spuštění pro zadané ID spuštění kanálu |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/read | Přečte spuštění kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/createrun/action | Vytvoří spuštění pro kanál. |
> | Akce | Microsoft.DataFactory/factories/pipelines/delete | Odstraní kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Získá průběh spuštění aktivit. |
> | Akce | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Přečte spuštění kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/read | Čtení kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/write | Vytvořit nebo aktualizovat kanál |
> | Akce | Microsoft.DataFactory/factories/querypipelineruns/action | Dotazuje spuštění kanálu. |
> | Akce | Microsoft.DataFactory/factories/querypipelineruns/read | Přečte výsledek dotazu spuštění kanálu. |
> | Akce | Microsoft.DataFactory/factories/querytriggerruns/action | Dotazuje spuštění aktivační události. |
> | Akce | Microsoft.DataFactory/factories/querytriggerruns/read | Přečte výsledek spuštění aktivační události. |
> | Akce | Microsoft.DataFactory/factories/read | Přečte Data Factory. |
> | Akce | Microsoft.DataFactory/factories/triggerruns/read | Přečte spuštění aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/delete | Odstraní všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/read | Přečte všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/start/action | Spustí všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/stop/action | Zastaví všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/triggerruns/read | Přečte spuštění aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/write | Vytvoří nebo aktualizuje všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/write | Vytvořit nebo aktualizovat Data Factory |
> | Akce | Microsoft.DataFactory/locations/configureFactoryRepo/action | Nakonfiguruje úložiště pro objekt pro vytváření. |
> | Akce | Microsoft.DataFactory/operations/read | Přečte všechny operace v Factory poskytovatele dat Microsoftu. |
> | Akce | Microsoft.DataFactory/register/action | Zaregistruje předplatné u poskytovatele prostředků Data Factory. |
> | Akce | Microsoft.DataFactory/unregister/action | Zruší registraci předplatného u poskytovatele prostředků Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Odstranění zásad pro výpočetní prostředky. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Získejte informace o zásadách výpočetní prostředky. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Vytvořit nebo aktualizujete zásady pro výpočetní prostředky. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Zrušení propojení účtu DataLakeStore z DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Získejte informace o propojený účet DataLakeStore DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Vytvořit nebo aktualizovat propojený účet DataLakeStore DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/delete | Odstranění účtu DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Odstraňte pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Získejte informace o pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Vytvořit nebo aktualizovat pravidla brány firewall. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Získáte výsledek operace DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/read | Získejte informace o účet DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Seznam tokenů SAS pro kontejnery úložiště v propojeném účtu úložiště DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Získáte kontejnery z účtu DataLakeAnalytics propojeného účtu úložiště. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Zrušit propojení účtu úložiště z účtu DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Získejte informace o propojeného účtu úložiště DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Vytvořit nebo aktualizovat DataLakeAnalytics účtu propojeného účtu úložiště. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udělení oprávnění pro zrušení úlohy od jiných uživatelů. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/write | Vytvořit nebo aktualizovat účet DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/capability/read | Získejte informace o schopnostech předplatného týkající se používání DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Zkontrolujte dostupnost názvu účtu DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/operationResults/read | Získáte výsledek operace DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/operations/read | Získáte dostupné operace DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/register/action | Zaregistrujte předplatné pro DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataLakeStore/accounts/delete | Odstranění účtu DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Povolení služby KeyVault pro DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Odstraňte filtr EventGrid. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Získá filtr EventGrid. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Vytvořit nebo aktualizovat filtr EventGrid. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/delete | Odstraňte pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/read | Získejte informace o pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/write | Vytvořit nebo aktualizovat pravidla brány firewall. |
> | Akce | Microsoft.DataLakeStore/accounts/operationResults/read | Získáte výsledek operace DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/accounts/read | Získejte informace o účet DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/accounts/Superuser/action | Udělit superuživatele v Data Lake Store, pokud ho získají s Microsoft.Authorization/roleAssignments/write. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Odstraňte důvěryhodného zprostředkovatele identity. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Získejte informace o důvěryhodného zprostředkovatele identity. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Vytvořit nebo aktualizovat důvěryhodného zprostředkovatele identity. |
> | Akce | Microsoft.DataLakeStore/accounts/write | Vytvořit nebo aktualizovat účet DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/capability/read | Získejte informace o schopnostech předplatného týkající se používání DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Zkontrolujte dostupnost názvu účtu DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/operationResults/read | Získáte výsledek operace DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/operations/read | Získáte dostupné operace DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/register/action | Zaregistrujte předplatné pro DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataMigration/locations/operationResults/read | Získání stavu dlouhotrvající operace související s přijatou odpovědí 202 |
> | Akce | Microsoft.DataMigration/locations/operationStatuses/read | Získání stavu dlouhotrvající operace související s přijatou odpovědí 202 |
> | Akce | Microsoft.DataMigration/register/action | Zaregistruje předplatné u poskytovatele Azure Database Migration Service |
> | Akce | Microsoft.DataMigration/services/checkStatus/action | Zkontroluje, jestli je služba nasazená a běží |
> | Akce | Microsoft.DataMigration/services/delete | Odstraní prostředek a všechny jeho podřízené objekty |
> | Akce | Microsoft.DataMigration/services/projects/accessArtifacts/action | Generování adresy URL, kterou jde použít na akci GET nebo PUT s artefakty projektu |
> | Akce | Microsoft.DataMigration/services/projects/delete | Odstraní prostředek a všechny jeho podřízené objekty |
> | Akce | Microsoft.DataMigration/services/projects/files/delete | Odstraní prostředek a všechny jeho podřízené objekty |
> | Akce | Microsoft.DataMigration/services/projects/files/read | Čtení informací o prostředcích |
> | Akce | Microsoft.DataMigration/services/projects/files/read/action | Získat adresu URL, kterou jde použít ke čtení obsahu souboru |
> | Akce | Microsoft.DataMigration/services/projects/files/readWrite/action | Získat adresu URL, kterou jde použít ke čtení nebo zápisu obsahu souboru |
> | Akce | Microsoft.DataMigration/services/projects/files/write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Akce | Microsoft.DataMigration/services/projects/read | Čtení informací o prostředcích |
> | Akce | Microsoft.DataMigration/services/projects/tasks/cancel/action | Zrušení úlohy, pokud právě běží |
> | Akce | Microsoft.DataMigration/services/projects/tasks/delete | Odstraní prostředek a všechny jeho podřízené objekty |
> | Akce | Microsoft.DataMigration/services/projects/tasks/read | Čtení informací o prostředcích |
> | Akce | Microsoft.DataMigration/services/projects/tasks/write | Spuštění úloh Azure Database Migration Service |
> | Akce | Microsoft.DataMigration/services/projects/write | Spuštění úloh Azure Database Migration Service |
> | Akce | Microsoft.DataMigration/services/read | Čtení informací o prostředcích |
> | Akce | Microsoft.DataMigration/services/slots/delete | Odstraní prostředek a všechny jeho podřízené objekty |
> | Akce | Microsoft.DataMigration/services/slots/read | Čtení informací o prostředcích |
> | Akce | Microsoft.DataMigration/services/slots/write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Akce | Microsoft.DataMigration/services/start/action | Spuštění služby DMS, aby mohla znovu zpracovávat migrace |
> | Akce | Microsoft.DataMigration/services/stop/action | Zastavení služby DMS, aby se minimalizovaly její náklady |
> | Akce | Microsoft.DataMigration/services/write | Vytvoření nebo aktualizace prostředků a jejich vlastností |
> | Akce | Microsoft.DataMigration/skus/read | Získání seznamu skladových položek podporovaných prostředky DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DBforMariaDB/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMariaDB/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMariaDB/servers/configurations/read | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Akce | Microsoft.DBforMariaDB/servers/configurations/write | Aktualizujte hodnotu pro zadanou konfiguraci |
> | Akce | Microsoft.DBforMariaDB/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforMariaDB/servers/firewallRules/delete | Odstraní stávající pravidlo brány firewall. |
> | Akce | Microsoft.DBforMariaDB/servers/firewallRules/read | Vrátí seznam brány firewall pravidla pro server nebo načte vlastnosti pro pravidlo brány firewall na zadané. |
> | Akce | Microsoft.DBforMariaDB/servers/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci stávající pravidlo. |
> | Akce | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá disagnostic nastavení pro prostředek. |
> | Akce | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforMariaDB/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforMariaDB/servers/recoverableServers/read | Vrátí obnovitelné informace o serveru MariaDB |
> | Akce | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Aktualizace konfigurace pro zadaný server |
> | Akce | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Vrátí seznam virtuálních sítí, pravidla nebo načte vlastnosti pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.DBforMariaDB/servers/write | Vytvoří server se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DBforMySQL/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMySQL/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMySQL/servers/configurations/read | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Akce | Microsoft.DBforMySQL/servers/configurations/write | Aktualizujte hodnotu pro zadanou konfiguraci |
> | Akce | Microsoft.DBforMySQL/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforMySQL/servers/firewallRules/delete | Odstraní stávající pravidlo brány firewall. |
> | Akce | Microsoft.DBforMySQL/servers/firewallRules/read | Vrátí seznam brány firewall pravidla pro server nebo načte vlastnosti pro pravidlo brány firewall na zadané. |
> | Akce | Microsoft.DBforMySQL/servers/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci stávající pravidlo. |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá disagnostic nastavení pro prostředek. |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro servery MySQL |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforMySQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforMySQL/servers/recoverableServers/read | Vrátí informace, které obnovit MySQL Server |
> | Akce | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Načíst podrobnosti o zásadu detekce hrozeb server nakonfigurovaný na daném serveru |
> | Akce | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Změna serveru zásadu detekce hrozeb pro daný server |
> | Akce | Microsoft.DBforMySQL/servers/updateConfigurations/action | Aktualizace konfigurace pro zadaný server |
> | Akce | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Vrátí seznam virtuálních sítí, pravidla nebo načte vlastnosti pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.DBforMySQL/servers/write | Vytvoří server se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforPostgreSQL/performanceTiers/read | Vrátí seznam úrovní výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforPostgreSQL/servers/advisors/read | Vrátí seznam advisros |
> | Akce | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Vrátí seznam doporučené akce |
> | Akce | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Doporučení |
> | Akce | Microsoft.DBforPostgreSQL/servers/configurations/read | Vrátí seznam konfigurací pro server nebo získá vlastnosti pro zadanou konfiguraci. |
> | Akce | Microsoft.DBforPostgreSQL/servers/configurations/write | Aktualizujte hodnotu pro zadanou konfiguraci |
> | Akce | Microsoft.DBforPostgreSQL/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Odstraní stávající pravidlo brány firewall. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Vrátí seznam brány firewall pravidla pro server nebo načte vlastnosti pro pravidlo brány firewall na zadané. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci stávající pravidlo. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá disagnostic nastavení pro prostředek. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro servery Postgres |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Vrátí text dotazu |
> | Akce | Microsoft.DBforPostgreSQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Vrátí obnovitelné informace o serveru PostgreSQL |
> | Akce | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Načíst podrobnosti o zásadu detekce hrozeb server nakonfigurovaný na daném serveru |
> | Akce | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Změna serveru zásadu detekce hrozeb pro daný server |
> | Akce | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Vrátí seznam statistiky dotazů pro nejčastější dotazy. |
> | Akce | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Aktualizace konfigurace pro zadaný server |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Vrátí seznam virtuálních sítí, pravidla nebo načte vlastnosti pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Vrátí statistiky čekání pro instance |
> | Akce | Microsoft.DBforPostgreSQL/servers/write | Vytvoří server se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Devices/checkNameAvailability/Action | Zkontrolujte, že pokud IotHub name je k dispozici |
> | Akce | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Zkontrolujte, že pokud zřizování název služby je k dispozici |
> | Akce | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Odstraní certifikát |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Vygenerovat ověřovací kód |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Získá certifikát |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Ověřte, prostředek certifikátu |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Odstranění prostředku tenanta IotHub |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Odstranit skupinu uživatelů centra událostí |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Získat skupiny příjemců centra událostí |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Vytvořit skupinu uživatelů centra událostí |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Export zařízení |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Získá tenanta IotHub statistiky prostředků |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importovat zařízení |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Získá klíč tenanta IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Získat podrobnosti odeslaného na daný IotHub úlohy |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Získá klíče tenanta IotHub |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Načte definice protokolů k dispozici pro službu IOT hub |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Získá dostupné metriky pro službu IOT hub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Získat metriky kvót |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/Read | Získá prostředek tenanta IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testovací zpráva proti všechny existující trasy |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testovací zpráva proti zadaný testovací trasy |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Získá stav všech směrování koncových bodů pro IOT hub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/Write | Vytvořit nebo aktualizovat prostředek tenanta IotHub |
> | Akce | Microsoft.Devices/ElasticPools/metricDefinitions/read | Získá dostupné metriky pro službu IOT hub |
> | Akce | Microsoft.Devices/iotHubs/certificates/Delete | Odstraní certifikát |
> | Akce | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Vygenerovat ověřovací kód |
> | Akce | Microsoft.Devices/iotHubs/certificates/Read | Získá certifikát |
> | Akce | Microsoft.Devices/iotHubs/certificates/verify/Action | Ověřte, prostředek certifikátu |
> | Akce | Microsoft.Devices/iotHubs/certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Akce | Microsoft.Devices/iotHubs/Delete | Odstranit prostředek IotHub |
> | Akce | Microsoft.Devices/IotHubs/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/IotHubs/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Odstraní filtr Event gridu |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Read | Získá filtr Event gridu |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Write | Vytvořit novou nebo aktualizovat existující filtr Event gridu |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Odstranit skupinu uživatelů centra událostí |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Získat skupiny příjemců centra událostí |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Vytvořit skupinu uživatelů centra událostí |
> | Akce | Microsoft.Devices/iotHubs/exportDevices/Action | Export zařízení |
> | Akce | Microsoft.Devices/iotHubs/importDevices/Action | Importovat zařízení |
> | Akce | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Získání IotHub klíče pro daný název |
> | Akce | Microsoft.Devices/iotHubs/iotHubStats/Read | Získání statistiky IotHub |
> | Akce | Microsoft.Devices/iotHubs/jobs/Read | Získat podrobnosti odeslaného na daný IotHub úlohy |
> | Akce | Microsoft.Devices/iotHubs/listkeys/Action | Získat všechny klíče IotHub |
> | Akce | Microsoft.Devices/IotHubs/logDefinitions/read | Načte definice protokolů k dispozici pro službu IOT hub |
> | Akce | Microsoft.Devices/IotHubs/metricDefinitions/read | Získá dostupné metriky pro službu IOT hub |
> | Akce | Microsoft.Devices/iotHubs/operationresults/Read | Získat výsledek operace (zastaralé rozhraní API) |
> | Akce | Microsoft.Devices/iotHubs/quotaMetrics/Read | Získat metriky kvót |
> | Akce | Microsoft.Devices/iotHubs/Read | Získá prostředky IOT hub |
> | Akce | Microsoft.Devices/iotHubs/routing/$testall/Action | Testovací zpráva proti všechny existující trasy |
> | Akce | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testovací zpráva proti zadaný testovací trasy |
> | Akce | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Získá stav všech směrování koncových bodů pro IOT hub |
> | Akce | Microsoft.Devices/iotHubs/skus/Read | Získat položky SKU platný IotHub |
> | Akce | Microsoft.Devices/iotHubs/Write | Vytvořit nebo aktualizovat prostředek IotHub |
> | Akce | Microsoft.Devices/locations/operationresults/Read | Získat výsledek operace podle umístění |
> | Akce | Microsoft.Devices/operationresults/Read | Získat výsledek operace |
> | Akce | Microsoft.Devices/operations/Read | Získat všechny operace ResourceProvider |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Delete | Odstraní certifikát |
> | Akce | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Vygenerovat ověřovací kód |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Read | Získá certifikát |
> | Akce | Microsoft.Devices/provisioningServices/certificates/verify/Action | Ověřte, prostředek certifikátu |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Akce | Microsoft.Devices/provisioningServices/Delete | Odstranit prostředek IotDps |
> | Akce | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. Poznámka: budete potřebovat `Microsoft.Insights/diagnosticSettings/read` i  |
> | Akce | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Získání klíčů IotDps pro název klíče |
> | Akce | Microsoft.Devices/provisioningServices/listkeys/Action | Získat všechny IotDps klíče |
> | Akce | Microsoft.Devices/provisioningServices/logDefinitions/read | Načte definice protokolů k dispozici pro službu zřizování. Poznámka: budete potřebovat `Microsoft.Insights/logDefinitions/read` i |
> | Akce | Microsoft.Devices/provisioningServices/metricDefinitions/read | Získá dostupné metriky pro službu zřizování. Poznámka: budete potřebovat `Microsoft.Insights/metricDefinitions/read` i |
> | Akce | Microsoft.Devices/provisioningServices/operationresults/Read | Získat výsledek operace distribučních bodů |
> | Akce | Microsoft.Devices/provisioningServices/Read | Získat prostředek IotDps |
> | Akce | Microsoft.Devices/provisioningServices/skus/Read | Získat platný IotDps SKU |
> | Akce | Microsoft.Devices/provisioningServices/Write | Vytvořit prostředek IotDps |
> | Akce | Microsoft.Devices/register/action | Zaregistrovat předplatné u poskytovatele prostředků IOT hub a povolí vytváření prostředků IOT hub |
> | Akce | Microsoft.Devices/register/action | Zaregistrovat předplatné u poskytovatele prostředků IOT hub a povolí vytváření prostředků IOT hub |
> | Akce | Microsoft.Devices/usages/Read | Získáte podrobnosti o použití předplatné pro tohoto zprostředkovatele. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DevSpaces/controllers/delete | Odstranění služby Azure Dev prostory Kontroleru a roviny dat |
> | Akce | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Seznam připojení podrobnosti pro kontroler serveru Azure Dev mezery infrastrukturou |
> | Akce | Microsoft.DevSpaces/controllers/read | Vlastnosti čtení Azure Dev prostory Kontroleru |
> | Akce | Microsoft.DevSpaces/controllers/write | Vytvoření nebo aktualizace Azure Dev prostory Kontroleru vlastnosti |
> | Akce | Microsoft.DevSpaces/register/action | Registrace poskytovatele prostředků Microsoft Dev prostory s předplatným |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DevTestLab/labCenters/delete | Odstranění centra testovacích prostředí. |
> | Akce | Microsoft.DevTestLab/labCenters/read | Přečtěte si centra testovacích prostředí. |
> | Akce | Microsoft.DevTestLab/labCenters/write | Přidat nebo upravit centra testovacích prostředí. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Přečtěte si šablon správce prostředků azure. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Vygeneruje šablonu ARM pro daný artefakt, nahraje požadované soubory do účtu úložiště a ověří vygenerovaný artefakt. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Přečíst artefakty. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/delete | Odstranění zdrojů artefaktů. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/read | Zdroje artefaktů pro čtení. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/write | Přidat nebo upravit zdrojů artefaktů. |
> | Akce | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Deklarace identity náhodný nárokovatelný virtuální počítač v testovacím prostředí. |
> | Akce | Microsoft.DevTestLab/labs/costs/read | Další náklady. |
> | Akce | Microsoft.DevTestLab/labs/costs/write | Přidat nebo upravit náklady. |
> | Akce | Microsoft.DevTestLab/labs/CreateEnvironment/action | Vytvoření virtuálních počítačů v testovacím prostředí. |
> | Akce | Microsoft.DevTestLab/labs/customImages/delete | Odstraňte vlastní Image. |
> | Akce | Microsoft.DevTestLab/labs/customImages/read | Čtení vlastních imagí. |
> | Akce | Microsoft.DevTestLab/labs/customImages/write | Přidat nebo upravit vlastní Image. |
> | Akce | Microsoft.DevTestLab/labs/delete | Odstraňte testovací prostředí. |
> | Akce | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exportuje využití prostředku testovacího prostředí do účtu úložiště |
> | Akce | Microsoft.DevTestLab/labs/formulas/delete | Odstraňte vzorce. |
> | Akce | Microsoft.DevTestLab/labs/formulas/read | Další vzorce. |
> | Akce | Microsoft.DevTestLab/labs/formulas/write | Přidat nebo upravit vzorce. |
> | Akce | Microsoft.DevTestLab/labs/galleryImages/read | Načíst Image z galerie. |
> | Akce | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Vygenerujte identifikátor URI pro nahrání vlastních imagí disků do testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importujte virtuální počítač do jiného testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labs/ListVhds/action | Výpis imagí disků, které jsou k dispozici pro vytváření vlastních imagí. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/delete | Odstraňte notificationchannels. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Odešle oznámení zadanému kanálu. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/read | Přečtěte si notificationchannels. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/write | Přidat nebo upravit notificationchannels. |
> | Akce | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Vyhodnotí zásady testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/delete | Odstraňte zásady. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/read | Přečtěte si zásady. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/write | Přidání nebo úprava zásad. |
> | Akce | Microsoft.DevTestLab/labs/policySets/read | Čtení sady zásad. |
> | Akce | Microsoft.DevTestLab/labs/read | Číst testovací prostředí. |
> | Akce | Microsoft.DevTestLab/labs/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/labs/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Výpis všech použitelných plánů |
> | Akce | Microsoft.DevTestLab/labs/schedules/read | Přečtěte si plány. |
> | Akce | Microsoft.DevTestLab/labs/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/delete | Odstraňte Spouštěče služeb. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/read | Přečtěte si Spouštěče služeb. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/write | Přidat nebo upravit Spouštěče služeb. |
> | Akce | Microsoft.DevTestLab/labs/users/delete | Odstraňte profily uživatelů. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/Attach/action | Připojení a vytvoří zapůjčení disku k virtuálnímu počítači. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/delete | Odstraní disky. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odpojení a zrušení zapůjčení disku připojeného k virtuálnímu počítači. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/read | Další disky. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/write | Přidat nebo upravit disky. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/read | Čtení prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/write | Přidat nebo změnit prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/read | Číst profily uživatelů. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/delete | Odstraňte tajných kódů. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/read | Čtení tajných klíčů. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/write | Přidat nebo upravit tajných kódů. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Odstraňte prostředky service Fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Obsahuje příslušné spustit/zastavit plány. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Přečtěte si service Fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Přečtěte si plány. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Spusťte service fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zastaví service fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Přidat nebo změnit prostředky service Fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/write | Přidat nebo upravit profily uživatelů. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Připojte nový nebo existující datový disk k virtuálnímu počítači. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Použijte artefakty ve virtuálním počítači. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Převzít vlastnictví existujícího virtuálního počítače |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/delete | Odstraňte virtuální počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Odpojte zadaný disk od virtuálního počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Získá řetězec, který představuje obsah souboru RDP pro virtuální počítač |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Obsahuje příslušné spustit/zastavit plány. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/read | Přečtěte si virtuální počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Opětovné nasazení virtuálního počítače |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Změnit velikost virtuálního počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Virtuální počítač restartujte. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Přečtěte si plány. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Spuštění virtuálního počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zastavit virtuální počítač |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Převede všechny datové disky připojené k virtuálnímu počítači vlastníkem aktuálním uživatelem. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Uvolní vlastnictví existujícího virtuálního počítače |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/write | Umožňuje přidat nebo upravit virtuální počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/delete | Odstraňte virtuální sítě. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/read | Načíst virtuální sítě. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/write | Přidat nebo upravit virtuální sítě. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/delete | Odstraňte fondy virtuálních počítačů. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/read | Přečtěte si fondy virtuálních počítačů. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/write | Přidat nebo upravit fondy virtuálních počítačů. |
> | Akce | Microsoft.DevTestLab/labs/write | Přidání a úpravy testovacích prostředí. |
> | Akce | Microsoft.DevTestLab/locations/operations/read | Operace čtení. |
> | Akce | Microsoft.DevTestLab/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.DevTestLab/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/schedules/read | Přečtěte si plány. |
> | Akce | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizuje ID cílového prostředku v plánu |
> | Akce | Microsoft.DevTestLab/schedules/write | Přidat nebo změnit plány. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DocumentDB/databaseAccountNames/read | Zkontroluje dostupnost názvu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/backup/action | Odeslat žádost o konfiguraci zálohování |
> | Akce | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Změnit skupinu prostředků účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Přečte definice metriky kolekce. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Načte kolekci metriky. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Čtení databáze účtu oddílu klíčové metriky na úrovni |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Přečíst metriky úrovně oddílu v účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Čtení databáze účtu na úrovni kolekce |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Přečtěte si účet databáze úrovně použití oddílu |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Načte kolekci použití. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Přečte definice metriky databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Přečte metriky databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Přečte použití databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/delete | Odstraní účty databází. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Změna priority převzetí služeb při selhání oblastí na databázovém účtu. Používá se k provedení operace ruční převzetí služeb při selhání |
> | Akce | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Získejte připojovací řetězce pro databázový účet |
> | Akce | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Vypsat klíče účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Přečte definice metriky účtu databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/metrics/read | Přečte účet metriky databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline oblast na databázovém účtu.  |
> | Akce | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online oblast na databázovém účtu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Umožňuje načíst stav asynchronní operace |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metriky latence pro čtení |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/read | Přečtěte si percentily latenci replikace |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Číst metriky latence pro konkrétní zdrojová a cílová oblast |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Metriky latence pro čtení pro konkrétní cílové oblasti |
> | Akce | Microsoft.DocumentDB/databaseAccounts/read | Přečte účet databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Čte databázi, klíče účtu jen pro čtení. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Čte databázi, klíče účtu jen pro čtení. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Obměna klíčů účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Čtení metrik místní kolekce. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Přečtěte si místní databáze účtu oddílu klíčové metriky na úrovni |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Číst metriky úrovně oddílu v účtu místní databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Přečtěte si místní databáze účtu na úrovni kolekce |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Přečte účet metriky oblasti a databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/restore/action | Odeslat žádost o obnovení |
> | Akce | Microsoft.DocumentDB/databaseAccounts/usages/read | Použití účtu čte databázi. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/write | Aktualizace databáze účtů. |
> | Akce | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Upozorní Microsoft.DocumentDB, že se odstraňuje virtuální síť nebo podsítě |
> | Akce | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Umožňuje načíst stav z deleteVirtualNetworkOrSubnets asynchronní operace |
> | Akce | Microsoft.DocumentDB/operationResults/read | Umožňuje načíst stav asynchronní operace |
> | Akce | Microsoft.DocumentDB/operations/read | Operace čtení documentdb Microsoft  |
> | Akce | Microsoft.DocumentDB/register/action |  Registrace poskytovatele prostředků Microsoft DocumentDB pro předplatné |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DomainRegistration/checkDomainAvailability/Action | Zkontrolujte, jestli je možné zakoupit domény |
> | Akce | Microsoft.DomainRegistration/domains/Delete | Odstraňte existující domény. |
> | Akce | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Odstranit identifikátor vlastnictví |
> | Akce | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Seznam identifikátorů vlastnictví |
> | Akce | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Získat identifikátor vlastnictví |
> | Akce | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Vytvořit nebo aktualizovat identifikátor |
> | Akce | Microsoft.DomainRegistration/domains/operationresults/Read | Získat operace domény |
> | Akce | Microsoft.DomainRegistration/domains/Read | Získat seznam domén |
> | Akce | Microsoft.DomainRegistration/domains/Read | Získejte doménu |
> | Akce | Microsoft.DomainRegistration/domains/renew/Action | Obnovte existující doménu. |
> | Akce | Microsoft.DomainRegistration/domains/Write | Přidat novou doménu nebo aktualizaci existující |
> | Akce | Microsoft.DomainRegistration/generateSsoRequest/Action | Vytvořit žádost o přihlášení do control centeru domény. |
> | Akce | Microsoft.DomainRegistration/listDomainRecommendations/Action | Načíst seznam doporučení domény na základě klíčových slov |
> | Akce | Microsoft.DomainRegistration/operations/Read | Vypsat všechny operace registrace domény app service |
> | Akce | Microsoft.DomainRegistration/register/action | Registrace poskytovatele prostředků Domains Microsoftu pro předplatné |
> | Akce | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Seznam dohoda akce |
> | Akce | Microsoft.DomainRegistration/topLevelDomains/Read | Získat toplevel domény |
> | Akce | Microsoft.DomainRegistration/topLevelDomains/Read | Získejte toplevel doménu |
> | Akce | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Ověření objektu nákupu domény bez odeslání |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | V projektu Microsoft Dynamics Lifecycle Services, do které patří uživateli zobrazit Microsoft Dynamics AX 2012 R3 vyhodnocení nasazení |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Vytvoření nasazení aplikace Microsoft Dynamics AX 2012 R3 hodnocení v projektu Microsoft Dynamics Lifecycle Services, do které patří uživateli. Nasazení se dají spravovat z portálu pro správu Azure |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Přečtěte si konektory, které patří do projektu Microsoft Dynamics Lifecycle Services |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Vytvoření a aktualizaci konektory, které patří do projektu Microsoft Dynamics Lifecycle Services |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/delete | Odstranit Microsoft Dynamics Lifecycle Services projekty, které patří uživateli |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/read | Zobrazení projektů Microsoft Dynamics Lifecycle Services, které patří uživateli |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/write | Vytvoření a aktualizace projektů Microsoft Dynamics Lifecycle Services, které patří uživateli. Je možné aktualizovat jenom vlastnosti název a popis. Po vytvoření nelze aktualizovat předplatné a umístění přidružené k projektu |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.EventGrid/domains/delete | Odstranit doménu |
> | Akce | Microsoft.EventGrid/domains/listKeys/action | Seznam klíčů pro doménu |
> | Akce | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro domény |
> | Akce | Microsoft.EventGrid/domains/read | Přečtěte si k doméně |
> | Akce | Microsoft.EventGrid/domains/regenerateKey/action | Znovu vygenerovat klíč pro doménu |
> | Akce | Microsoft.EventGrid/domains/topics/read | Přečtěte si téma domény |
> | Akce | Microsoft.EventGrid/domains/write | Vytvořit nebo aktualizovat domény |
> | Akce | Microsoft.EventGrid/eventSubscriptions/delete | Odstranit eventSubscription |
> | Akce | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Získat úplnou adresu url pro odběr události |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro odběry událostí |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro odběry událostí |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro eventSubscriptions |
> | Akce | Microsoft.EventGrid/eventSubscriptions/read | Přečtěte si eventSubscription |
> | Akce | Microsoft.EventGrid/eventSubscriptions/write | Vytvořit nebo aktualizovat eventSubscription |
> | Akce | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro témata |
> | Akce | Microsoft.EventGrid/locations/eventSubscriptions/read | Seznam předplatných místní události |
> | Akce | Microsoft.EventGrid/locations/operationResults/read | Načíst výsledek operace s místní |
> | Akce | Microsoft.EventGrid/locations/operationsStatus/read | Umožňuje načíst stav operace na místní |
> | Akce | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Seznam odběrů událostí regionální topictype |
> | Akce | Microsoft.EventGrid/operationResults/read | Načíst výsledek operace |
> | Akce | Microsoft.EventGrid/operationsStatus/read | Umožňuje načíst stav operace |
> | Akce | Microsoft.EventGrid/register/action | Zaregistruje předplatné u poskytovatele prostředků EventGrid. |
> | Akce | Microsoft.EventGrid/topics/delete | Odstranit téma |
> | Akce | Microsoft.EventGrid/topics/listKeys/action | Seznam klíčů pro téma |
> | Akce | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro témata |
> | Akce | Microsoft.EventGrid/topics/read | Přečtěte si téma |
> | Akce | Microsoft.EventGrid/topics/regenerateKey/action | Znovu vygenerovat klíč tématu |
> | Akce | Microsoft.EventGrid/topics/write | Vytvořit nebo aktualizovat téma |
> | Akce | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Seznam předplatných globální události podle typu tématu |
> | Akce | Microsoft.EventGrid/topictypes/eventtypes/read | Podporované topictype eventtypes pro čtení |
> | Akce | Microsoft.EventGrid/topictypes/read | Přečtěte si topictype |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.EventHub/checkNameAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. |
> | Akce | Microsoft.EventHub/checkNamespaceAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. použijte místo něj Checknameavailability. |
> | Akce | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Získání seznamu metriky clusteru popisů prostředků |
> | Akce | Microsoft.EventHub/clusters/read | Získá popis prostředku clusteru. |
> | Akce | Microsoft.EventHub/clusters/write | Získá popis prostředku clusteru. |
> | Akce | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla zadané virtuální sítě v poskytovateli prostředků EventHub. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/action | Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je PUT. Použijte prosím volání PUT se místo toho aktualizovat autorizační pravidlo Namespace... Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/delete | Odstraňte autorizační pravidlo Namespace. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/write | Vytvořit Namespace úrovně autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla, primární a sekundární klíč. |
> | Akce | Microsoft.EventHub/namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Získá klíče pravidel ověřování pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat pravidla ověřování primárního oboru názvů zotavení po havárii |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních. |
> | Akce | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tato operace dá vyvolat jen přes primární obor názvů. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operace aktualizace centra událostí. Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte prosím volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operace, která odstraní autorizační pravidla centra událostí |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Získá připojovací řetězec k centru EventHub |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Získá seznam autorizačních pravidel centra událostí |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Autorizační pravidla centra událostí vytvořit a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla. |
> | Akce | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operace odstranění skupiny ConsumerGroup prostředku |
> | Akce | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Získá seznam popisů prostředků ConsumerGroup |
> | Akce | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Vytvoření nebo aktualizace skupiny ConsumerGroup vlastnosti. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/Delete | Operace, která odstraní prostředek centra událostí |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/read | Získá seznam popisů prostředků centra událostí |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/write | Vytvoření nebo aktualizaci EventHub vlastnosti. |
> | Akce | Microsoft.EventHub/namespaces/ipFilterRules/delete | Odstranit prostředek filtru IP |
> | Akce | Microsoft.EventHub/namespaces/ipFilterRules/read | Získat prostředek filtru IP |
> | Akce | Microsoft.EventHub/namespaces/ipFilterRules/write | Vytvořit prostředek filtru IP |
> | Akce | Microsoft.EventHub/namespaces/messagingPlan/read | Získá plán pro zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti zveřejněné přes prostředek MessagingPlan přesunuly do (nadřazeného) prostředku Namespace v pozdějších verzích rozhraní API...<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.EventHub/namespaces/messagingPlan/write | Aktualizuje se plán pro zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti zveřejněné přes prostředek MessagingPlan přesunuly do (nadřazeného) prostředku Namespace v pozdějších verzích rozhraní API...<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.EventHub/namespaces/operationresults/read | Získá stav operace Namespace. |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Získá seznam popisů prostředků Namespace nastavení diagnostiky |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Získá seznam popisů prostředků Namespace nastavení diagnostiky |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Získání seznamu protokolů Namespace popisů prostředků |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získat seznam metrik Namespace popisů prostředků |
> | Akce | Microsoft.EventHub/namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Odebrat obor názvů služby ACS |
> | Akce | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Získat prostředek pravidla virtuální sítě |
> | Akce | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft.EventHub/namespaces/write | Vytvoření prostředku Namespace a aktualizovat jeho vlastnosti. Značky a kapacita Namespace jsou vlastnosti, které je možné aktualizovat. |
> | Akce | Microsoft.EventHub/operations/read | Získat operace |
> | Akce | Microsoft.EventHub/register/action | Zaregistruje předplatné pro poskytovatele prostředků EventHub a povolí vytváření prostředků EventHub. |
> | Akce | Microsoft.EventHub/sku/read | Získá seznam popisů prostředků Sku |
> | Akce | Microsoft.EventHub/sku/regions/read | Získá seznam popisů prostředků SkuRegions |
> | Akce | Microsoft.EventHub/unregister/action | Zaregistrovat poskytovatele prostředků EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Features/features/read | Získá funkce předplatného. |
> | Akce | Microsoft.Features/operations/read | Získá seznam operací. |
> | Akce | Microsoft.Features/providers/features/read | Získá funkci předplatného v daném zprostředkovateli prostředků. |
> | Akce | Microsoft.Features/providers/features/register/action | Registruje funkci pro předplatné v daném zprostředkovateli prostředků. |
> | Akce | Microsoft.Features/providers/features/unregister/action | Zruší registraci funkce pro předplatné v daném poskytovateli prostředků. |
> | Akce | Microsoft.Features/register/action | Zaregistruje funkci předplatného. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Přiřazení konfigurace Get Host. |
> | Akce | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Sestava ke konfiguraci přiřazení Get Host. |
> | Akce | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Vytvořte nové přiřazení konfigurace hosta. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.HDInsight/clusters/applications/delete | Odstranit aplikaci pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/applications/read | Získat aplikace pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/applications/write | Vytvořit nebo aktualizovat aplikaci pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/changerdpsetting/action | Změnit nastavení protokolu RDP pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/configurations/action | Aktualizace konfigurace clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/configurations/read | Získání konfigurace clusterů HDInsight |
> | Akce | Microsoft.HDInsight/clusters/delete | Odstranění clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/read | Získat podrobnosti o clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/roles/resize/action | Změnit velikost clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/write | Vytvořit nebo aktualizovat HDInsight Cluster |
> | Akce | Microsoft.HDInsight/locations/capabilities/read | Získat možnosti předplatného |
> | Akce | Microsoft.HDInsight/locations/checkNameAvailability/read | Zkontrolovat dostupnost názvu |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ImportExport/jobs/delete | Odstraní existující úlohy. |
> | Akce | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Získá klíče Bitlockeru pro zadanou úlohu. |
> | Akce | Microsoft.ImportExport/jobs/read | Získá vlastnosti pro zadanou úlohu nebo vrátí seznam úloh. |
> | Akce | Microsoft.ImportExport/jobs/write | Vytvoří úlohu se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadanou úlohu. |
> | Akce | Microsoft.ImportExport/locations/read | Získá vlastnosti pro zadané umístění nebo vrátí seznam umístění. |
> | Akce | Microsoft.ImportExport/register/action | Zaregistruje předplatné u poskytovatele prostředků importu/exportu a umožňuje vytvoření úlohy importu/exportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Insights/ActionGroups/Delete | Odstraní skupinu akcí. |
> | Akce | Microsoft.Insights/ActionGroups/Read | Přečte skupinu akcí. |
> | Akce | Microsoft.Insights/ActionGroups/Write | Vytvoří nebo aktualizuje skupinu akcí. |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivováno upozornění protokolu aktivit |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Delete | Odstraní upozornění protokolu aktivit. |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Read | Přečte upozornění protokolu aktivit. |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Write | Vytvoří nebo aktualizuje upozornění protokolu aktivit. |
> | Akce | Microsoft.Insights/AlertRules/Activated/Action | Aktivováno klasické upozornění na metriku |
> | Akce | Microsoft.Insights/AlertRules/Delete | Odstraní klasické upozornění na metriku. |
> | Akce | Microsoft.Insights/AlertRules/Incidents/Read | Přečte incident klasického upozornění na metriku. |
> | Akce | Microsoft.Insights/AlertRules/Read | Přečte klasické upozornění na metriku. |
> | Akce | Microsoft.Insights/AlertRules/Resolved/Action | Přeloženo klasické upozornění na metriku |
> | Akce | Microsoft.Insights/AlertRules/Throttled/Action | Omezeno pravidlo klasického upozornění na metriku |
> | Akce | Microsoft.Insights/AlertRules/Write | Vytvoří nebo aktualizuje klasické upozornění na metriku. |
> | Akce | Microsoft.Insights/AutoscaleSettings/Delete | Odstraní nastavení automatického škálování. |
> | Akce | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Přečte nastavení diagnostiky prostředků. |
> | Akce | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředků. |
> | Akce | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Číst definice protokolů |
> | Akce | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/AutoscaleSettings/Read | Přečte nastavení automatického škálování. |
> | Akce | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Zahájeno automatické škálování vertikálním snížením kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Dokončeno automatické škálování vertikálním snížením kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Zahájeno automatické škálování vertikálním navýšením kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Dokončeno automatické škálování vertikálním navýšením kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/Write | Vytvoří nebo aktualizuje nastavení automatického škálování. |
> | Akce | Microsoft.Insights/Components/AnalyticsItems/Delete | Odstranění položky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/AnalyticsItems/Read | Čtení položky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/AnalyticsItems/Write | Zápis položky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/AnalyticsTables/Action | Akce tabulky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/AnalyticsTables/Delete | Odstranění schématu tabulky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/AnalyticsTables/Read | Čtení schématu tabulky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/AnalyticsTables/Write | Zápis schématu tabulky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/Annotations/Delete | Odstranění poznámky Application Insights |
> | Akce | Microsoft.Insights/Components/Annotations/Read | Čtení poznámky Application Insights |
> | Akce | Microsoft.Insights/Components/Annotations/Write | Zápis poznámky Application Insights |
> | Akce | Microsoft.Insights/Components/Api/Read | Čtení rozhraní API pro data komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/ApiKeys/Action | Generování klíče rozhraní API pro Application Insights |
> | Akce | Microsoft.Insights/Components/ApiKeys/Delete | Odstranění klíče rozhraní API pro Application Insights |
> | Akce | Microsoft.Insights/Components/ApiKeys/Read | Čtení klíče rozhraní API pro Application Insights |
> | Akce | Microsoft.Insights/Components/BillingPlanForComponent/Read | Čtení účtovacího plánu pro komponentu Application Insights |
> | Akce | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Čtení aktuálních funkcí fakturace pro komponentu Application Insights |
> | Akce | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Zápis aktuálních funkcí fakturace pro komponentu Application Insights |
> | Akce | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Čtení výchozí konfigurace integrace ALM Application Insights |
> | Akce | Microsoft.Insights/Components/Delete | Odstraní konfiguraci komponenty Application Insights. |
> | Akce | Microsoft.Insights/Components/Events/Read | Získá protokoly z Application Insights pomocí formátu dotazů OData. |
> | Akce | Microsoft.Insights/Components/ExportConfiguration/Action | Akce nastavení exportu Application Insights |
> | Akce | Microsoft.Insights/Components/ExportConfiguration/Delete | Odstranění nastavení exportu Application Insights |
> | Akce | Microsoft.Insights/Components/ExportConfiguration/Read | Čtení nastavení exportu Application Insights |
> | Akce | Microsoft.Insights/Components/ExportConfiguration/Write | Zápis nastavení exportu Application Insights |
> | Akce | Microsoft.Insights/Components/ExtendQueries/Read | Čtení výsledků na rozšířený dotaz komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/Favorites/Delete | Odstranění oblíbené položky Application Insights |
> | Akce | Microsoft.Insights/Components/Favorites/Read | Čtení oblíbené položky Application Insights |
> | Akce | Microsoft.Insights/Components/Favorites/Write | Zápis oblíbené položky Application Insights |
> | Akce | Microsoft.Insights/Components/FeatureCapabilities/Read | Čtení možností funkce komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Čtení funkcí fakturace dostupných komponentě Application Insights |
> | Akce | Microsoft.Insights/Components/GetToken/Read | Čtení tokenu komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/MetricDefinitions/Read | Čtení definic metrik komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/Metrics/Read | Čtení metrik komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/Move/Action | Přesune součást Application Insights do jiné skupiny prostředků nebo předplatného. |
> | Akce | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Odstranění osobní položky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/MyAnalyticsItems/Read | Čtení osobní položky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/MyAnalyticsItems/Write | Zápis osobní položky analýzy Application Insights |
> | Akce | Microsoft.Insights/Components/MyFavorites/Read | Čtení osobní oblíbené položky Application Insights |
> | Akce | Microsoft.Insights/Components/Operations/Read | Získá stav dlouhodobých operací v Application Insights. |
> | Akce | Microsoft.Insights/Components/PricingPlans/Read | Čtení cenového plánu komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/PricingPlans/Write | Zápis cenového plánu komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Čtení konfigurace služby Proactive Detection pro Application Insights |
> | Akce | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Zápis konfigurace služby Proactive Detection pro Application Insights |
> | Akce | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/Components/Purge/Action | Vyprázdní data z Application Insights. |
> | Akce | Microsoft.Insights/Components/Query/Read | Spustí dotazy na protokoly Application Insights. |
> | Akce | Microsoft.Insights/Components/QuotaStatus/Read | Čtení stavu kvót komponenty Application Insights |
> | Akce | Microsoft.Insights/Components/Read | Přečte konfiguraci komponenty Application Insights. |
> | Akce | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Čtení umístění webových testů Application Insights |
> | Akce | Microsoft.Insights/Components/Webtests/Read | Přečte konfiguraci webového testu. |
> | Akce | Microsoft.Insights/Components/WorkItemConfigs/Delete | Odstranění konfigurace integrace ALM Application Insights |
> | Akce | Microsoft.Insights/Components/WorkItemConfigs/Read | Čtení konfigurace integrace ALM Application Insights |
> | Akce | Microsoft.Insights/Components/WorkItemConfigs/Write | Zápis konfigurace integrace ALM Application Insights |
> | Akce | Microsoft.Insights/Components/Write | Provede zápis do konfigurace komponenty Application Insights. |
> | Akce | Microsoft.Insights/DiagnosticSettings/Delete | Odstraní nastavení diagnostiky prostředků. |
> | Akce | Microsoft.Insights/DiagnosticSettings/Read | Přečte nastavení diagnostiky prostředků. |
> | Akce | Microsoft.Insights/DiagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředků. |
> | Akce | Microsoft.Insights/EventCategories/Read | Přečte dostupné kategorie událostí protokolu aktivit. |
> | Akce | Microsoft.Insights/eventtypes/digestevents/Read | Číst výtah typů událostí správy |
> | Akce | Microsoft.Insights/eventtypes/values/Read | Přečte události protokolu aktivit |
> | Akce | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Odstraní nastavení diagnostiky protokolů síťových toků. |
> | Akce | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Přečte nastavení diagnostiky protokolů síťových toků. |
> | Akce | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky protokolů síťových toků. |
> | Akce | Microsoft.Insights/ListMigrationDate/Action | Získá datum migrace předplatného. |
> | Akce | Microsoft.Insights/ListMigrationDate/Read | Získá datum migrace předplatného. |
> | Akce | Microsoft.Insights/LogDefinitions/Read | Číst definice protokolů |
> | Akce | Microsoft.Insights/LogProfiles/Delete | Odstraní profil protokolu aktivit. |
> | Akce | Microsoft.Insights/LogProfiles/Read | Přečte profil protokolu aktivit. |
> | Akce | Microsoft.Insights/LogProfiles/Write | Vytvoří nebo aktualizuje profil protokolu aktivit. |
> | Akce | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Přečte data z tabulky ADAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/ADReplicationResult/Read | Přečte data z tabulky ADReplicationResult. |
> | Akce | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Přečte data z tabulky ADSecurityAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/Alert/Read | Přečte data z tabulky Alert. |
> | Akce | Microsoft.Insights/Logs/AlertHistory/Read | Přečte data z tabulky AlertHistory. |
> | Akce | Microsoft.Insights/Logs/ApplicationInsights/Read | Přečte data z tabulky ApplicationInsights. |
> | Akce | Microsoft.Insights/Logs/AzureActivity/Read | Přečte data z tabulky AzureActivity. |
> | Akce | Microsoft.Insights/Logs/AzureMetrics/Read | Přečte data z tabulky AzureMetrics. |
> | Akce | Microsoft.Insights/Logs/BoundPort/Read | Přečte data z tabulky BoundPort. |
> | Akce | Microsoft.Insights/Logs/CommonSecurityLog/Read | Přečte data z tabulky CommonSecurityLog. |
> | Akce | Microsoft.Insights/Logs/ComputerGroup/Read | Přečte data z tabulky ComputerGroup. |
> | Akce | Microsoft.Insights/Logs/ConfigurationChange/Read | Přečte data z tabulky ConfigurationChange. |
> | Akce | Microsoft.Insights/Logs/ConfigurationData/Read | Přečte data z tabulky ConfigurationData. |
> | Akce | Microsoft.Insights/Logs/ContainerImageInventory/Read | Přečte data z tabulky ContainerImageInventory. |
> | Akce | Microsoft.Insights/Logs/ContainerInventory/Read | Přečte data z tabulky ContainerInventory. |
> | Akce | Microsoft.Insights/Logs/ContainerLog/Read | Přečte data z tabulky ContainerLog. |
> | Akce | Microsoft.Insights/Logs/ContainerServiceLog/Read | Přečte data z tabulky ContainerServiceLog. |
> | Akce | Microsoft.Insights/Logs/DeviceAppCrash/Read | Přečte data z tabulky DeviceAppCrash. |
> | Akce | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Přečte data z tabulky DeviceAppLaunch. |
> | Akce | Microsoft.Insights/Logs/DeviceCalendar/Read | Přečte data z tabulky DeviceCalendar. |
> | Akce | Microsoft.Insights/Logs/DeviceCleanup/Read | Přečte data z tabulky DeviceCleanup. |
> | Akce | Microsoft.Insights/Logs/DeviceConnectSession/Read | Přečte data z tabulky DeviceConnectSession. |
> | Akce | Microsoft.Insights/Logs/DeviceEtw/Read | Přečte data z tabulky DeviceEtw. |
> | Akce | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Přečte data z tabulky DeviceHardwareHealth. |
> | Akce | Microsoft.Insights/Logs/DeviceHealth/Read | Přečte data z tabulky DeviceHealth. |
> | Akce | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Přečte data z tabulky DeviceHeartbeat. |
> | Akce | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Přečte data z tabulky DeviceSkypeHeartbeat. |
> | Akce | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Přečte data z tabulky DeviceSkypeSignIn. |
> | Akce | Microsoft.Insights/Logs/DeviceSleepState/Read | Přečte data z tabulky DeviceSleepState. |
> | Akce | Microsoft.Insights/Logs/DHAppFailure/Read | Přečte data z tabulky DHAppFailure. |
> | Akce | Microsoft.Insights/Logs/DHAppReliability/Read | Přečte data z tabulky DHAppReliability. |
> | Akce | Microsoft.Insights/Logs/DHDriverReliability/Read | Přečte data z tabulky DHDriverReliability. |
> | Akce | Microsoft.Insights/Logs/DHLogonFailures/Read | Přečte data z tabulky DHLogonFailures. |
> | Akce | Microsoft.Insights/Logs/DHLogonMetrics/Read | Přečte data z tabulky DHLogonMetrics. |
> | Akce | Microsoft.Insights/Logs/DHOSCrashData/Read | Přečte data z tabulky DHOSCrashData. |
> | Akce | Microsoft.Insights/Logs/DHOSReliability/Read | Přečte data z tabulky DHOSReliability. |
> | Akce | Microsoft.Insights/Logs/DHWipAppLearning/Read | Přečte data z tabulky DHWipAppLearning. |
> | Akce | Microsoft.Insights/Logs/DnsEvents/Read | Přečte data z tabulky DnsEvents. |
> | Akce | Microsoft.Insights/Logs/DnsInventory/Read | Přečte data z tabulky DnsInventory. |
> | Akce | Microsoft.Insights/Logs/ETWEvent/Read | Přečte data z tabulky ETWEvent. |
> | Akce | Microsoft.Insights/Logs/Event/Read | Přečte data z tabulky Event. |
> | Akce | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Přečte data z tabulky ExchangeAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Přečte data z tabulky ExchangeOnlineAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/Heartbeat/Read | Přečte data z tabulky Heartbeat. |
> | Akce | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Přečte data z tabulky IISAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/InboundConnection/Read | Přečte data z tabulky InboundConnection. |
> | Akce | Microsoft.Insights/Logs/KubeNodeInventory/Read | Přečte data z tabulky KubeNodeInventory. |
> | Akce | Microsoft.Insights/Logs/KubePodInventory/Read | Přečte data z tabulky KubePodInventory. |
> | Akce | Microsoft.Insights/Logs/LinuxAuditLog/Read | Přečte data z tabulky LinuxAuditLog. |
> | Akce | Microsoft.Insights/Logs/MAApplication/Read | Přečte data z tabulky MAApplication. |
> | Akce | Microsoft.Insights/Logs/MAApplicationHealth/Read | Přečte data z tabulky MAApplicationHealth. |
> | Akce | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Přečte data z tabulky MAApplicationHealthAlternativeVersions. |
> | Akce | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Přečte data z tabulky MAApplicationHealthIssues. |
> | Akce | Microsoft.Insights/Logs/MAApplicationInstance/Read | Přečte data z tabulky MAApplicationInstance. |
> | Akce | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Přečte data z tabulky MAApplicationInstanceReadiness. |
> | Akce | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Přečte data z tabulky MAApplicationReadiness. |
> | Akce | Microsoft.Insights/Logs/MADeploymentPlan/Read | Přečte data z tabulky MADeploymentPlan. |
> | Akce | Microsoft.Insights/Logs/MADevice/Read | Přečte data z tabulky MADevice. |
> | Akce | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Přečte data z tabulky MADevicePnPHealth. |
> | Akce | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Přečte data z tabulky MADevicePnPHealthAlternativeVersions. |
> | Akce | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Přečte data z tabulky MADevicePnPHealthIssues. |
> | Akce | Microsoft.Insights/Logs/MADeviceReadiness/Read | Přečte data z tabulky MADeviceReadiness. |
> | Akce | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Přečte data z tabulky MADriverInstanceReadiness. |
> | Akce | Microsoft.Insights/Logs/MADriverReadiness/Read | Přečte data z tabulky MADriverReadiness. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddin/Read | Přečte data z tabulky MAOfficeAddin. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Přečte data z tabulky MAOfficeAddinHealth. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Přečte data z tabulky MAOfficeAddinHealthIssues. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Přečte data z tabulky MAOfficeAddinInstance. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Přečte data z tabulky MAOfficeAddinInstanceReadiness. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Přečte data z tabulky MAOfficeAddinReadiness. |
> | Akce | Microsoft.Insights/Logs/MAOfficeApp/Read | Přečte data z tabulky MAOfficeApp. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Přečte data z tabulky MAOfficeAppHealth. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Přečte data z tabulky MAOfficeAppInstance. |
> | Akce | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Přečte data z tabulky MAOfficeAppReadiness. |
> | Akce | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Přečte data z tabulky MAOfficeBuildInfo. |
> | Akce | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Přečte data z tabulky MAOfficeCurrencyAssessment. |
> | Akce | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Přečte data z tabulky MAOfficeCurrencyAssessmentDailyCounts. |
> | Akce | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Přečte data z tabulky MAOfficeDeploymentStatus. |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Přečte data z tabulky MAOfficeMacroHealth. |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Přečte data z tabulky MAOfficeMacroHealthIssues. |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Přečte data z tabulky MAOfficeMacroIssueInstanceReadiness. |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Přečte data z tabulky MAOfficeMacroIssueReadiness. |
> | Akce | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Přečte data z tabulky MAOfficeMacroSummary. |
> | Akce | Microsoft.Insights/Logs/MAOfficeSuite/Read | Přečte data z tabulky MAOfficeSuite. |
> | Akce | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Přečte data z tabulky MAOfficeSuiteInstance. |
> | Akce | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Přečte data z tabulky MAProposedPilotDevices. |
> | Akce | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Přečte data z tabulky MAWindowsBuildInfo. |
> | Akce | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Přečte data z tabulky MAWindowsCurrencyAssessment. |
> | Akce | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Přečte data z tabulky MAWindowsCurrencyAssessmentDailyCounts. |
> | Akce | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Přečte data z tabulky MAWindowsDeploymentStatus. |
> | Akce | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Přečte data z tabulky MAWindowsSysReqInstanceReadiness. |
> | Akce | Microsoft.Insights/Logs/NetworkMonitoring/Read | Přečte data z tabulky NetworkMonitoring. |
> | Akce | Microsoft.Insights/Logs/OfficeActivity/Read | Přečte data z tabulky OfficeActivity. |
> | Akce | Microsoft.Insights/Logs/Operation/Read | Přečte data z tabulky Operation. |
> | Akce | Microsoft.Insights/Logs/OutboundConnection/Read | Přečte data z tabulky OutboundConnection. |
> | Akce | Microsoft.Insights/Logs/Perf/Read | Přečte data z tabulky Perf. |
> | Akce | Microsoft.Insights/Logs/ProtectionStatus/Read | Přečte data z tabulky ProtectionStatus. |
> | Akce | Microsoft.Insights/Logs/Read | Přečte data ze všech protokolů. |
> | Akce | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Přečte data z tabulky ReservedAzureCommonFields. |
> | Akce | Microsoft.Insights/Logs/ReservedCommonFields/Read | Přečte data z tabulky ReservedCommonFields. |
> | Akce | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Přečte data z tabulky SCCMAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Přečte data z tabulky SCOMAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SecurityAlert/Read | Přečte data z tabulky SecurityAlert. |
> | Akce | Microsoft.Insights/Logs/SecurityBaseline/Read | Přečte data z tabulky SecurityBaseline. |
> | Akce | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Přečte data z tabulky SecurityBaselineSummary. |
> | Akce | Microsoft.Insights/Logs/SecurityDetection/Read | Přečte data z tabulky SecurityDetection. |
> | Akce | Microsoft.Insights/Logs/SecurityEvent/Read | Přečte data z tabulky SecurityEvent. |
> | Akce | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Přečte data z tabulky ServiceFabricOperationalEvent. |
> | Akce | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Přečte data z tabulky ServiceFabricReliableActorEvent. |
> | Akce | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Přečte data z tabulky ServiceFabricReliableServiceEvent. |
> | Akce | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Přečte data z tabulky SfBAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Přečte data z tabulky SfBOnlineAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Přečte data z tabulky SharePointOnlineAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Přečte data z tabulky SPAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Přečte data z tabulky SQLAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Přečte data z tabulky SQLQueryPerformance. |
> | Akce | Microsoft.Insights/Logs/Syslog/Read | Přečte data z tabulky Syslog. |
> | Akce | Microsoft.Insights/Logs/SysmonEvent/Read | Přečte data z tabulky SysmonEvent. |
> | Akce | Microsoft.Insights/Logs/UAApp/Read | Přečte data z tabulky UAApp. |
> | Akce | Microsoft.Insights/Logs/UAComputer/Read | Přečte data z tabulky UAComputer. |
> | Akce | Microsoft.Insights/Logs/UAComputerRank/Read | Přečte data z tabulky UAComputerRank. |
> | Akce | Microsoft.Insights/Logs/UADriver/Read | Přečte data z tabulky UADriver. |
> | Akce | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Přečte data z tabulky UADriverProblemCodes. |
> | Akce | Microsoft.Insights/Logs/UAFeedback/Read | Přečte data z tabulky UAFeedback. |
> | Akce | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Přečte data z tabulky UAHardwareSecurity. |
> | Akce | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Přečte data z tabulky UAIESiteDiscovery. |
> | Akce | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Přečte data z tabulky UAOfficeAddIn. |
> | Akce | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Přečte data z tabulky UAProposedActionPlan. |
> | Akce | Microsoft.Insights/Logs/UASysReqIssue/Read | Přečte data z tabulky UASysReqIssue. |
> | Akce | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Přečte data z tabulky UAUpgradedComputer. |
> | Akce | Microsoft.Insights/Logs/Update/Read | Přečte data z tabulky Update. |
> | Akce | Microsoft.Insights/Logs/UpdateRunProgress/Read | Přečte data z tabulky UpdateRunProgress. |
> | Akce | Microsoft.Insights/Logs/UpdateSummary/Read | Přečte data z tabulky UpdateSummary. |
> | Akce | Microsoft.Insights/Logs/Usage/Read | Přečte data z tabulky Usage. |
> | Akce | Microsoft.Insights/Logs/W3CIISLog/Read | Přečte data z tabulky W3CIISLog. |
> | Akce | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Přečte data z tabulky WaaSDeploymentStatus. |
> | Akce | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Přečte data z tabulky WaaSInsiderStatus. |
> | Akce | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Přečte data z tabulky WaaSUpdateStatus. |
> | Akce | Microsoft.Insights/Logs/WDAVStatus/Read | Přečte data z tabulky WDAVStatus. |
> | Akce | Microsoft.Insights/Logs/WDAVThreat/Read | Přečte data z tabulky WDAVThreat. |
> | Akce | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Přečte data z tabulky WindowsClientAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/WindowsFirewall/Read | Přečte data z tabulky WindowsFirewall. |
> | Akce | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Přečte data z tabulky WindowsServerAssessmentRecommendation. |
> | Akce | Microsoft.Insights/Logs/WireData/Read | Přečte data z tabulky WireData. |
> | Akce | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Přečte data z tabulky WUDOAggregatedStatus. |
> | Akce | Microsoft.Insights/Logs/WUDOStatus/Read | Přečte data z tabulky WUDOStatus. |
> | Akce | Microsoft.Insights/MetricAlerts/Delete | Odstraní upozornění na metriku. |
> | Akce | Microsoft.Insights/MetricAlerts/Read | Přečte upozornění na metriku. |
> | Akce | Microsoft.Insights/MetricAlerts/Status/Read | Přečíst stav upozornění na metriku |
> | Akce | Microsoft.Insights/MetricAlerts/Write | Vytvoří nebo aktualizuje upozornění na metriku. |
> | Akce | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/MetricDefinitions/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/Metrics/providers/Metrics/Read | Čtení metrik |
> | Akce | Microsoft.Insights/Metrics/Read | Čtení metrik |
> | Hodnotu DataAction | Microsoft.Insights/Metrics/Write | Zapíše metriky. |
> | Akce | Microsoft.Insights/MigrateToNewpricingModel/Action | Migruje předplatné na nový cenový model. |
> | Akce | Microsoft.Insights/Operations/Read | Operace čtení |
> | Akce | Microsoft.Insights/Register/Action | Zaregistruje poskytovatele platformy Microsoft Insights. |
> | Akce | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Vrátí předplatné na starší verzi cenového modelu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Delete | Odstraňuje se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Read | Načítá se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Write | Zapisuje se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/Tenants/Register/Action | Inicializuje poskytovatele platformy Microsoft Insights. |
> | Akce | Microsoft.Insights/Unregister/Action | Zaregistruje poskytovatele platformy Microsoft Insights. |
> | Akce | Microsoft.Insights/Webtests/Delete | Odstraní konfiguraci webového testu. |
> | Akce | Microsoft.Insights/Webtests/GetToken/Read | Čtení tokenu webového testu |
> | Akce | Microsoft.Insights/Webtests/MetricDefinitions/Read | Čtení definic metrik webového testu |
> | Akce | Microsoft.Insights/Webtests/Metrics/Read | Čtení metriky webového testu |
> | Akce | Microsoft.Insights/Webtests/Read | Přečte konfiguraci webového testu. |
> | Akce | Microsoft.Insights/Webtests/Write | Provede zápis do konfigurace webového testu. |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Intune/diagnosticsettings/delete | Odstranění nastavení diagnostiky |
> | Akce | Microsoft.Intune/diagnosticsettings/read | Čtení nastavení diagnostiky |
> | Akce | Microsoft.Intune/diagnosticsettings/write | Zápis nastavení diagnostiky |
> | Akce | Microsoft.Intune/diagnosticsettingscategories/read | Čtení kategorie nastavení diagnostiky |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.IoTCentral/checkNameAvailability/action | Kontroluje, jestli je k dispozici název aplikace IoT Central |
> | Akce | Microsoft.IoTCentral/checkSubdomainAvailability/action | Kontroluje, jestli je k dispozici centrální aplikaci IoT subdomény |
> | Akce | Microsoft.IoTCentral/IoTApps/delete | Odstranění aplikace IoT Central |
> | Akce | Microsoft.IoTCentral/IoTApps/read | Získá jedné aplikace IoT Central |
> | Akce | Microsoft.IoTCentral/IoTApps/write | Vytvoří nebo aktualizuje aplikací IoT Central |
> | Akce | Microsoft.IoTCentral/operations/read | Získá všechny dostupné operace u aplikací, jejichž IoT Central |
> | Akce | Microsoft.IoTCentral/register/action | Zaregistrovat předplatné pro Azure IoT Central poskytovatele prostředků |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.IoTSpaces/Graph/delete | Odstraní prostředek Microsoft.IoTSpaces grafu |
> | Akce | Microsoft.IoTSpaces/Graph/read | Získá prostředky Microsoft.IoTSpaces grafu |
> | Akce | Microsoft.IoTSpaces/Graph/write | Vytvořit prostředek Microsoft.IoTSpaces grafu |
> | Akce | Microsoft.IoTSpaces/register/action | Zaregistrovat předplatné u poskytovatele prostředků grafu Microsoft.IoTSpaces umožňující creationg prostředků |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.KeyVault/checkNameAvailability/read | Zkontroluje, jestli je název trezoru klíčů platný a jestli se nepoužívá. |
> | Akce | Microsoft.KeyVault/deletedVaults/read | Zobrazí vlastnosti obnovitelně odstraněných trezorů klíčů. |
> | Akce | Microsoft.KeyVault/hsmPools/delete | Odstraní fond HSM. |
> | Akce | Microsoft.KeyVault/hsmPools/joinVault/action | Připojí trezor klíčů k fondu HSM. |
> | Akce | Microsoft.KeyVault/hsmPools/read | Zobrazí vlastnosti fondu HSM. |
> | Akce | Microsoft.KeyVault/hsmPools/write | Pokud chcete aktualizovat vlastnosti existujícího fondu HSM, vytvořte nový fond HSM. |
> | Akce | Microsoft.KeyVault/locations/deletedVaults/purge/action | Trvale odstraní trezor klíčů, který se nejdříve odstranil obnovitelně. |
> | Akce | Microsoft.KeyVault/locations/deletedVaults/read | Zobrazí vlastnosti obnovitelně odstraněného trezoru klíčů. |
> | Akce | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Upozorní službu Microsoft.KeyVault, že se odstraňuje virtuální síť nebo podsíť. |
> | Akce | Microsoft.KeyVault/locations/operationResults/read | Zkontroluje výsledek dlouho běžící operace. |
> | Akce | Microsoft.KeyVault/operations/read | Umožňuje zobrazit seznam operací dostupných v poskytovateli prostředků Microsoft.KeyVault. |
> | Akce | Microsoft.KeyVault/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.KeyVault/unregister/action | Zruší registraci předplatného. |
> | Akce | Microsoft.KeyVault/vaults/accessPolicies/write | Existující zásady přístupu můžete aktualizovat sloučením nebo nahrazením, případně můžete do trezoru přidat nové zásady přístupu. |
> | Akce | Microsoft.KeyVault/vaults/delete | Odstranění trezoru klíčů |
> | Akce | Microsoft.KeyVault/vaults/deploy/action | Umožní přístup k tajným kódům v trezoru klíčů při nasazování prostředků Azure. |
> | Akce | Microsoft.KeyVault/vaults/read | Zobrazí vlastnosti trezoru klíčů. |
> | Akce | Microsoft.KeyVault/vaults/secrets/read | Zobrazí vlastnosti tajného kódu, ale ne jeho hodnotu. |
> | Akce | Microsoft.KeyVault/vaults/secrets/write | Vytvoří nový tajný kód nebo aktualizuje hodnotu existujícího tajného kódu. |
> | Akce | Microsoft.KeyVault/vaults/write | Vytvoří nový trezor klíčů nebo aktualizuje vlastnosti existujícího trezoru klíčů. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Kusto/Clusters/Databases/delete | Odstraní prostředek databáze. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Odstraní prostředek připojení data. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Odstraní prostředek připojení data. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Načte datový prostředek připojení. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Načte datový prostředek připojení. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Zapíše prostředek connetions data. |
> | Akce | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Zapíše prostředek connetions data. |
> | Akce | Microsoft.Kusto/Clusters/Databases/read | Načte prostředek databáze. |
> | Akce | Microsoft.Kusto/Clusters/Databases/write | Zapíše databáze prostředků. |
> | Akce | Microsoft.Kusto/Clusters/delete | Odstraní prostředek clusteru. |
> | Akce | Microsoft.Kusto/Clusters/read | Načte prostředek clusteru. |
> | Akce | Microsoft.Kusto/Clusters/write | Zapíše prostředek clusteru. |
> | Akce | Microsoft.Kusto/Locations/CheckNameAvailability/write | Čtení zkontrolujte název dostupnost prostředků |
> | Akce | Microsoft.Kusto/locations/operationresults/read | Prostředky pro operace čtení |
> | Akce | Microsoft.Kusto/Operations/read | Prostředky pro operace čtení |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.LabServices/labAccounts/CreateLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/delete | Odstranění účtů testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/galleryImages/delete | Odstraňte Image z galerie. |
> | Akce | Microsoft.LabServices/labAccounts/galleryImages/read | Načíst Image z galerie. |
> | Akce | Microsoft.LabServices/labAccounts/galleryImages/write | Přidat nebo upravit Image z galerie. |
> | Akce | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Získejte informace o místní dostupnosti pro každou kategorii velikost konfigurují v rámci účtu testovacího prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Přidání uživatelů do testovacího prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/delete | Odstraňte testovací prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Deklarace identity náhodné prostředí pro uživatele nastavení prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Odstraňte nastavení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Deklarace identity prostředí a přiřadí ji k uživateli |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Čtení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resetuje heslo uživatele v prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Spuštění prostředí spuštěním všechny prostředky v prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Zastaví prostředí zastavte všechny prostředky v prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Přidat nebo změnit prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Předpisy a deprovisions požadované prostředky pro nastavení prostředí založené na aktuální stav nastavení testovacího prostředí nebo prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Nastavení prostředí pro čtení. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Přečtěte si plány. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Spustí šablonu spuštěním všechny prostředky v šabloně. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Spustí šablonu spuštěním všechny prostředky v šabloně. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Přidat nebo upravit nastavení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/read | Číst testovací prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/Register/action | Zaregistrujte se a spravované testovací prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/delete | Odstraňte uživatele. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/read | Další uživatelé. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/write | Přidat nebo změnit uživatele. |
> | Akce | Microsoft.LabServices/labAccounts/labs/write | Přidání a úpravy testovacích prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/read | Čtení účtů testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/write | Přidat nebo upravit účtů testovacího prostředí. |
> | Akce | Microsoft.LabServices/locations/operations/read | Operace čtení. |
> | Akce | Microsoft.LabServices/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.LabServices/users/GetEnvironment/action | Načte podrobnosti virtuálního počítače |
> | Akce | Microsoft.LabServices/users/GetOperationBatchStatus/action | Načíst stav operace služby batch |
> | Akce | Microsoft.LabServices/users/GetOperationStatus/action | Umožňuje získat stav dlouhotrvající operace |
> | Akce | Microsoft.LabServices/users/GetPersonalPreferences/action | Získat osobní preference pro uživatele |
> | Akce | Microsoft.LabServices/users/ListEnvironments/action | Seznam prostředí pro uživatele |
> | Akce | Microsoft.LabServices/users/ListLabs/action | Seznam testovacích prostředí pro uživatele. |
> | Akce | Microsoft.LabServices/users/Register/action | Registrace uživatele do spravované testovacího prostředí |
> | Akce | Microsoft.LabServices/users/ResetPassword/action | Resetuje heslo uživatele v prostředí |
> | Akce | Microsoft.LabServices/users/StartEnvironment/action | Spuštění prostředí spuštěním všechny prostředky v prostředí. |
> | Akce | Microsoft.LabServices/users/StopEnvironment/action | Zastaví prostředí zastavte všechny prostředky v prostředí |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.LocationBasedServices/accounts/delete | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) odstranit účet Location Based Services. |
> | Akce | Microsoft.LocationBasedServices/accounts/listKeys/action | (Zastaralé: Použijte prosím klíče /providers/Microsoft.Maps)List účet Location Based Services |
> | Akce | Microsoft.LocationBasedServices/accounts/read | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) získat účet Location Based Services. |
> | Akce | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) vygenerovat nový účet Location Based Services primární nebo sekundární klíč |
> | Akce | Microsoft.LocationBasedServices/accounts/write | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) vytvořit nebo aktualizovat účet Location Based Services. |
> | Akce | Microsoft.LocationBasedServices/register/action | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) zaregistrujte poskytovatele |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.LocationServices/accounts/delete | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) odstranit účet služby umístění. |
> | Akce | Microsoft.LocationServices/accounts/listKeys/action | (Zastaralé: Použijte prosím klíče /providers/Microsoft.Maps)List účet Location Based Services |
> | Akce | Microsoft.LocationServices/accounts/read | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) získat účet služby umístění. |
> | Akce | Microsoft.LocationServices/accounts/regenerateKey/action | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) vygenerovat nový účet Location Based Services primární nebo sekundární klíč |
> | Akce | Microsoft.LocationServices/accounts/write | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) vytvořit nebo aktualizovat účet služby umístění. |
> | Akce | Microsoft.LocationServices/register/action | (Zastaralé: Použijte prosím /providers/Microsoft.Maps) zaregistrujte poskytovatele |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Přečte data z tabulky ADAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Přečte data z tabulky ADReplicationResult. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Přečte data z tabulky ADSecurityAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Alert/read | Přečte data z tabulky Alert. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Přečte data z tabulky AlertHistory. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/AppCenterError/read | Čtení dat z tabulky AppCenterError |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Přečte data z tabulky ApplicationInsights. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/AuditLogs/read | Čtení dat z tabulky mají |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Přečte data z tabulky AzureActivity. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Přečte data z tabulky AzureMetrics. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Přečte data z tabulky BoundPort. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Přečte data z tabulky CommonSecurityLog. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Přečte data z tabulky ComputerGroup. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Přečte data z tabulky ConfigurationChange. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Přečte data z tabulky ConfigurationData. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Přečte data z tabulky ContainerImageInventory. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Přečte data z tabulky ContainerInventory. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Přečte data z tabulky ContainerLog. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Přečte data z tabulky ContainerServiceLog. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Přečte data z tabulky DeviceAppCrash. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Přečte data z tabulky DeviceAppLaunch. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Přečte data z tabulky DeviceCalendar. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Přečte data z tabulky DeviceCleanup. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Přečte data z tabulky DeviceConnectSession. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Přečte data z tabulky DeviceEtw. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Přečte data z tabulky DeviceHardwareHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Přečte data z tabulky DeviceHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Přečte data z tabulky DeviceHeartbeat. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Přečte data z tabulky DeviceSkypeHeartbeat. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Přečte data z tabulky DeviceSkypeSignIn. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Přečte data z tabulky DeviceSleepState. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Přečte data z tabulky DHAppFailure. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Přečte data z tabulky DHAppReliability. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Přečte data z tabulky DHDriverReliability. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Přečte data z tabulky DHLogonFailures. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Přečte data z tabulky DHLogonMetrics. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Přečte data z tabulky DHOSCrashData. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Přečte data z tabulky DHOSReliability. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Přečte data z tabulky DHWipAppLearning. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Přečte data z tabulky DnsEvents. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Přečte data z tabulky DnsInventory. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Přečte data z tabulky ETWEvent. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Event/read | Přečte data z tabulky Event. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Přečte data z tabulky ExchangeAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Přečte data z tabulky ExchangeOnlineAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Přečte data z tabulky Heartbeat. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Přečte data z tabulky IISAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Přečte data z tabulky InboundConnection. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/KubeEvents/read | Čtení dat z tabulky KubeEvents |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Přečte data z tabulky KubeNodeInventory. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Přečte data z tabulky KubePodInventory. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/KubeServices/read | Čtení dat z tabulky KubeServices |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Přečte data z tabulky LinuxAuditLog. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Přečte data z tabulky MAApplication. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Přečte data z tabulky MAApplicationHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Přečte data z tabulky MAApplicationHealthAlternativeVersions. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Přečte data z tabulky MAApplicationHealthIssues. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Přečte data z tabulky MAApplicationInstance. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Přečte data z tabulky MAApplicationInstanceReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Přečte data z tabulky MAApplicationReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Přečte data z tabulky MADeploymentPlan. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Přečte data z tabulky MADevice. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADeviceNotEnrolled/read | Čtení dat z tabulky MADeviceNotEnrolled |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Přečte data z tabulky MADevicePnPHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Přečte data z tabulky MADevicePnPHealthAlternativeVersions. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Přečte data z tabulky MADevicePnPHealthIssues. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Přečte data z tabulky MADeviceReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Přečte data z tabulky MADriverInstanceReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Přečte data z tabulky MADriverReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Přečte data z tabulky MAOfficeAddin. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Přečte data z tabulky MAOfficeAddinHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Přečte data z tabulky MAOfficeAddinHealthIssues. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Přečte data z tabulky MAOfficeAddinInstance. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Přečte data z tabulky MAOfficeAddinInstanceReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Přečte data z tabulky MAOfficeAddinReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Přečte data z tabulky MAOfficeApp. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Přečte data z tabulky MAOfficeAppHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Přečte data z tabulky MAOfficeAppInstance. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Přečte data z tabulky MAOfficeAppReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Přečte data z tabulky MAOfficeBuildInfo. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Přečte data z tabulky MAOfficeCurrencyAssessment. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Přečte data z tabulky MAOfficeCurrencyAssessmentDailyCounts. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Přečte data z tabulky MAOfficeDeploymentStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Přečte data z tabulky MAOfficeMacroHealth. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Přečte data z tabulky MAOfficeMacroHealthIssues. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Přečte data z tabulky MAOfficeMacroIssueInstanceReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Přečte data z tabulky MAOfficeMacroIssueReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Přečte data z tabulky MAOfficeMacroSummary. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Přečte data z tabulky MAOfficeSuite. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Přečte data z tabulky MAOfficeSuiteInstance. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Přečte data z tabulky MAProposedPilotDevices. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Přečte data z tabulky MAWindowsBuildInfo. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Přečte data z tabulky MAWindowsCurrencyAssessment. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Přečte data z tabulky MAWindowsCurrencyAssessmentDailyCounts. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Přečte data z tabulky MAWindowsDeploymentStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Přečte data z tabulky MAWindowsSysReqInstanceReadiness. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Přečte data z tabulky NetworkMonitoring. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Přečte data z tabulky OfficeActivity. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Operation/read | Přečte data z tabulky Operation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Přečte data z tabulky OutboundConnection. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Perf/read | Přečte data z tabulky Perf. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Přečte data z tabulky ProtectionStatus. |
> | Akce | Microsoft.LogAnalytics/logs/read | Přečte data ze všech protokolů. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Přečte data z tabulky ReservedAzureCommonFields. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Přečte data z tabulky ReservedCommonFields. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Přečte data z tabulky SCCMAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Přečte data z tabulky SCOMAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Přečte data z tabulky SecurityAlert. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Přečte data z tabulky SecurityBaseline. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Přečte data z tabulky SecurityBaselineSummary. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Přečte data z tabulky SecurityDetection. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Přečte data z tabulky SecurityEvent. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Přečte data z tabulky ServiceFabricOperationalEvent. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Přečte data z tabulky ServiceFabricReliableActorEvent. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Přečte data z tabulky ServiceFabricReliableServiceEvent. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Přečte data z tabulky SfBAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Přečte data z tabulky SfBOnlineAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Přečte data z tabulky SharePointOnlineAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SigninLogs/read | Čtení dat z tabulky SigninLogs |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Přečte data z tabulky SPAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Přečte data z tabulky SQLAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Přečte data z tabulky SQLQueryPerformance. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Přečte data z tabulky Syslog. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Přečte data z tabulky SysmonEvent. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Tables.Custom/read | Přečte data z jakéhokoli vlastního protokolu. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Přečte data z tabulky UAApp. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Přečte data z tabulky UAComputer. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Přečte data z tabulky UAComputerRank. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Přečte data z tabulky UADriver. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Přečte data z tabulky UADriverProblemCodes. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Přečte data z tabulky UAFeedback. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Přečte data z tabulky UAHardwareSecurity. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Přečte data z tabulky UAIESiteDiscovery. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Přečte data z tabulky UAOfficeAddIn. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Přečte data z tabulky UAProposedActionPlan. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Přečte data z tabulky UASysReqIssue. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Přečte data z tabulky UAUpgradedComputer. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Update/read | Přečte data z tabulky Update. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Přečte data z tabulky UpdateRunProgress. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Přečte data z tabulky UpdateSummary. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/Usage/read | Přečte data z tabulky Usage. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Čtení dat z tabulky VMBoundPort |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Čtení dat z tabulky VMConnection |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Přečte data z tabulky W3CIISLog. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Přečte data z tabulky WaaSDeploymentStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Přečte data z tabulky WaaSInsiderStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Přečte data z tabulky WaaSUpdateStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Přečte data z tabulky WDAVStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Přečte data z tabulky WDAVThreat. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Přečte data z tabulky WindowsClientAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WindowsEvent/read | Čtení dat z tabulky WindowsEvent |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Přečte data z tabulky WindowsFirewall. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Přečte data z tabulky WindowsServerAssessmentRecommendation. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WireData/read | Přečte data z tabulky WireData. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WorkloadMonitoringPerf/read | Čtení dat z tabulky WorkloadMonitoringPerf |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Přečte data z tabulky WUDOAggregatedStatus. |
> | Hodnotu DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Přečte data z tabulky WUDOStatus. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/delete | Odstraní smlouvu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah smlouvy v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/read | Přečte smlouvu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/agreements/write | Vytvoří nebo aktualizuje smlouvu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/delete | Odstraní sestavení v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah sestavení v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/read | Přečte sestavení v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/assemblies/write | Vytvoří nebo aktualizuje sestavení v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Odstraní konfiguraci dávky v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Přečte konfiguraci dávky v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Vytvoří nebo aktualizuje konfiguraci dávky v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/certificates/delete | Odstraní certifikát v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/certificates/read | Přečte certifikát v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/certificates/write | Vytvoří nebo aktualizuje certifikát v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/delete | Odstraní účet integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/join/action | Připojí účet integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Načte adresu URL pro účet integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Získá klíče v trezoru klíčů. |
> | Akce | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Uloží události v účtu integrace do protokolu. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/delete | Odstraní mapu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah mapy v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/read | Přečte mapu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/maps/write | Vytvoří nebo aktualizuje mapu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/delete | Odstraní partnera v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah partnera v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/read | Přečte partnera v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/partners/write | Vytvoří nebo aktualizuje partnera v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Načte definice protokolů účtu pro integraci. |
> | Akce | Microsoft.Logic/integrationAccounts/read | Přečte účet integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Obnoví tajné kódy přístupových klíčů. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/delete | Odstraní schéma v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Získá adresu URL zpětného volání pro obsah schématu v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/read | Přečte schéma v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/schemas/write | Vytvoří nebo aktualizuje schéma v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/sessions/delete | Odstraní relaci v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/sessions/read | Přečte konfiguraci dávky v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/sessions/write | Vytvoří nebo aktualizuje relaci v účtu integrace. |
> | Akce | Microsoft.Logic/integrationAccounts/write | Vytvoří nebo aktualizuje účet integrace. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/delete | Odstraní prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/join/action | Připojí se k prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Přečte spravovanou operaci rozhraní API prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Přečte spravované rozhraní API prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Přečte stavy operací prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Přečte definice metrik prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/read | Přečte prostředí integrační služby. |
> | Akce | Microsoft.Logic/integrationServiceEnvironments/write | Vytvoří nebo aktualizuje prostředí integrační služby. |
> | Akce | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Získá skupiny operací doporučení pro pracovní postup. |
> | Akce | Microsoft.Logic/locations/workflows/validate/action | Ověří pracovní postup. |
> | Akce | Microsoft.Logic/operations/read | Získá operaci. |
> | Akce | Microsoft.Logic/register/action | Zaregistruje pro dané předplatné poskytovatele prostředků Microsoft.Logic. |
> | Akce | Microsoft.Logic/workflows/accessKeys/delete | Odstraní přístupový klíč. |
> | Akce | Microsoft.Logic/workflows/accessKeys/list/action | Zobrazí seznam tajných kódů přístupových klíčů. |
> | Akce | Microsoft.Logic/workflows/accessKeys/read | Načte přístupový klíč. |
> | Akce | Microsoft.Logic/workflows/accessKeys/regenerate/action | Obnoví tajné kódy přístupových klíčů. |
> | Akce | Microsoft.Logic/workflows/accessKeys/write | Vytvoří nebo aktualizuje přístupový klíč. |
> | Akce | Microsoft.Logic/workflows/delete | Odstraní pracovní postup. |
> | Akce | Microsoft.Logic/workflows/disable/action | Zakáže pracovní postup. |
> | Akce | Microsoft.Logic/workflows/enable/action | Povolí pracovní postup. |
> | Akce | Microsoft.Logic/workflows/listCallbackUrl/action | Načte adresu URL zpětného volání pro pracovní postup. |
> | Akce | Microsoft.Logic/workflows/listSwagger/action | Získá definice Swagger pracovních postupů. |
> | Akce | Microsoft.Logic/workflows/move/action | Přesune pracovní postup z existujícího ID předplatného, skupiny prostředků a/nebo názvu na jiné ID předplatného, skupinu prostředků nebo název. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Načte nastavení diagnostiky pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Načte definice protokolů pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Načte definice metrik pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/read | Načte pracovní postup. |
> | Akce | Microsoft.Logic/workflows/regenerateAccessKey/action | Obnoví tajné kódy přístupových klíčů. |
> | Akce | Microsoft.Logic/workflows/run/action | Spustí běh pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Získá trasy výrazu akce spuštění pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/read | Načte akci běhu pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Získá trasy výrazu opakování akce spuštění pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/read | Načte opakování akce běhu pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Načte historii požadavku akce opakování běhu pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Načte historii požadavku akce běhu pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Načte opakování rozsahu akce běhu pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/cancel/action | Zruší běh pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/operations/read | Načte stav operace běhu pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/runs/read | Načte běh pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/suspend/action | Pozastaví pracovní postup. |
> | Akce | Microsoft.Logic/workflows/triggers/histories/read | Načte historie triggerů. |
> | Akce | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Znovu spustí trigger pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Získá adresu URL zpětného volání pro trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/read | Načte trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/reset/action | Resetuje trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/run/action | Spustí trigger. |
> | Akce | Microsoft.Logic/workflows/triggers/setState/action | Nastaví stav triggeru. |
> | Akce | Microsoft.Logic/workflows/validate/action | Ověří pracovní postup. |
> | Akce | Microsoft.Logic/workflows/versions/read | Načte verzi pracovního postupu. |
> | Akce | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Získá adresu URL zpětného volání pro trigger. |
> | Akce | Microsoft.Logic/workflows/write | Vytvoří nebo aktualizuje pracovní postup. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Přesunout všechny strojového učení přidružení plánu závazku |
> | Akce | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Číst všechny strojového učení přidružení plánu závazku |
> | Akce | Microsoft.MachineLearning/commitmentPlans/delete | Odstranit jakýkoli počítač studijní plán závazku |
> | Akce | Microsoft.MachineLearning/commitmentPlans/join/action | Připojte se k jakýkoli počítač studijní plán závazku |
> | Akce | Microsoft.MachineLearning/commitmentPlans/read | Přečíst jakýkoli počítač studijní plán závazku |
> | Akce | Microsoft.MachineLearning/commitmentPlans/write | Vytvořit nebo aktualizovat libovolný plán závazku Machine Learning |
> | Akce | Microsoft.MachineLearning/locations/operationresults/read | Získat výsledek operace Machine Learning |
> | Akce | Microsoft.MachineLearning/locations/operationsstatus/read | Získání stavu operace probíhající Machine Learning |
> | Akce | Microsoft.MachineLearning/operations/read | Získat strojového učení operace |
> | Akce | Microsoft.MachineLearning/register/action | Zaregistruje předplatné pro strojové učení poskytovatele prostředků web service a povolí vytváření webových služeb. |
> | Akce | Microsoft.MachineLearning/skus/read | Získat strojového učení závazku plán SKU |
> | Akce | Microsoft.MachineLearning/webServices/action | Vytvoření místní vlastnosti webové služby pro podporované oblasti |
> | Akce | Microsoft.MachineLearning/webServices/delete | Odstranit všechny webové služby Machine Learning |
> | Akce | Microsoft.MachineLearning/webServices/listkeys/read | Získat klíče k webové službě Machine Learning |
> | Akce | Microsoft.MachineLearning/webServices/read | Číst všechny webové služby Machine Learning |
> | Akce | Microsoft.MachineLearning/webServices/write | Vytvořit nebo aktualizovat všechny webové služby Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/delete | Odstranit všechny pracovního prostoru Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Seznam klíčů pro pracovní prostor Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/read | Číst všechny pracovního prostoru Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Resynchronizace klíčů účtu úložiště, které jsou nakonfigurované pro pracovní prostor Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/write | Vytvořit nebo aktualizovat libovolný pracovního prostoru Machine Learning |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Zkontrolujte, jestli jsou aktualizace dostupné pro služby systému pro cluster operacionalizace |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Odstranit všechny hostitelské účty |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Seznam klíčů, přidružených operacionalizačního clusteru |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/read | Číst všechny hostitelské účty |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Aktualizace služby systému do operacionalizačního clusteru |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/write | Vytvořit nebo aktualizovat všechny hostitelské účty |
> | Akce | Microsoft.MachineLearningCompute/register/action | Zaregistruje ID předplatného u poskytovatele prostředků a povolí vytváření strojového učení výpočetních prostředků |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningModelManagement/accounts/delete | Odstranit všechny hostitelské účty |
> | Akce | Microsoft.MachineLearningModelManagement/accounts/read | Číst všechny hostitelské účty |
> | Akce | Microsoft.MachineLearningModelManagement/accounts/write | Vytvořit nebo aktualizovat všechny hostitelské účty |
> | Akce | Microsoft.MachineLearningModelManagement/register/action | Zaregistruje ID předplatného u poskytovatele prostředků a povolí vytváření, hostování účtu |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningServices/register/action | Zaregistruje předplatné u poskytovatele prostředků služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/delete | Odstraní výpočetní prostředky v pracovních prostorech služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Výpis tajných kódů pro výpočetní prostředky v pracovní prostor služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/read | Získá výpočetní prostředky, které v pracovních prostorech služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/write | Vytvoří nebo aktualizuje výpočetní prostředky v pracovních prostorech služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/delete | Odstraní Machine Learning Services pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/listKeys/action | Výpis tajných kódů pro pracovní prostor Machine Learning Services |
> | Akce | Microsoft.MachineLearningServices/workspaces/read | Získá Machine Learning Services pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/write | Vytvoří nebo aktualizuje v pracovních prostorech služby Machine Learning Services |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Akce RBAC pro přiřazení existujícího uživatele k prostředku je přiřazeno identity |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Odstraní stávajícímu identity přiřazené uživateli |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/read | Získá stávajícímu identity přiřazené uživateli |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/write | Vytvoří nového uživatele přiřadit identity nebo aktualizuje značkám přidruženým ke stávajícímu identity přiřazené uživateli |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ManagedLab/labAccounts/CreateLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/delete | Odstranění účtů testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/delete | Odstraňte testovací prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Odstraňte nastavení prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Čtení prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Přidat nebo změnit prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Nastavení prostředí pro čtení. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Přidat nebo upravit nastavení prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Odstranění testovacího prostředí virtuálních počítačů. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Čtení prostředí virtuálních počítačů. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Přidat nebo změnit testovací prostředí virtuálních počítačů. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/read | Číst testovací prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/write | Přidání a úpravy testovacích prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/read | Čtení účtů testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/write | Přidat nebo upravit účtů testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/locations/operations/read | Operace čtení. |
> | Akce | Microsoft.ManagedLab/register/action | Zaregistruje předplatné. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Management/checkNameAvailability/action | Ověří, zda název skupiny pro správu zadané platné a jedinečné. |
> | Akce | Microsoft.Management/getEntities/action | Seznam všech entit (skupin pro správu, předplatná atd.) pro ověřeného uživatele. |
> | Akce | Microsoft.Management/managementGroups/delete | Odstraňte skupinu pro správu. |
> | Akce | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Akce | Microsoft.Management/managementGroups/subscriptions/delete | Přidruží zrušit předplatné ze skupiny pro správu. |
> | Akce | Microsoft.Management/managementGroups/subscriptions/write | Přiřadí existující předplatné skupině pro správu. |
> | Akce | Microsoft.Management/managementGroups/write | Vytvořit nebo aktualizovat skupinu pro správu. |
> | Akce | Microsoft.Management/register/action | Zadané předplatné zaregistrovat Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Hodnotu DataAction | Microsoft.Maps/accounts/data/read | Uděluje přístup pro čtení dat do účtu mapy. |
> | Akce | Microsoft.Maps/accounts/delete | Odstraňte mapování účtu. |
> | Akce | Microsoft.Maps/accounts/eventGridFilters/delete | Odstranit filtr Event gridu |
> | Akce | Microsoft.Maps/accounts/eventGridFilters/read | Získat filtr Event gridu |
> | Akce | Microsoft.Maps/accounts/eventGridFilters/write | Vytvořit nebo aktualizovat filtr Event gridu |
> | Akce | Microsoft.Maps/accounts/listKeys/action | Vypsat klíče účtu mapy |
> | Akce | Microsoft.Maps/accounts/read | Získáte účet mapy. |
> | Akce | Microsoft.Maps/accounts/regenerateKey/action | Generovat nový účet Maps primární nebo sekundární klíč |
> | Akce | Microsoft.Maps/accounts/write | Vytvořit nebo aktualizovat účet Maps. |
> | Akce | Microsoft.Maps/register/action | Zaregistrujte poskytovatele |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Vrátí smlouvu. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Přijme podepsanou smlouvu. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje obrázek do ACR koncového uživatele. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Vrátí konfiguraci. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Uloží konfiguraci. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/delete | Provádí operaci odstranění pro prostředek služby classic dev. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Získá klíče správu prostředků služby classic dev. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Získá adresu jednotného přihlašování pro služby classic dev. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/read | Provádí operaci GET služby classic dev. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generuje klíče správu prostředků služby classic dev. |
> | Akce | Microsoft.MarketplaceApps/Operations/read | Přečíst operace pro všechny typy prostředků. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Zrušit smlouvu pro položky daného marketplace |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Vrátí smlouvu pro položky daného marketplace |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Podepsat smlouvu pro položky daného marketplace |
> | Akce | Microsoft.MarketplaceOrdering/agreements/read | Vrátí všechny smlouvy v rámci dané předplatné |
> | Akce | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Získat smlouvu pro položky marketplace daného virtuálního počítače |
> | Akce | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Přihlásit nebo zrušit smlouvu pro položky marketplace daného virtuálního počítače |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Media/checknameavailability/action | Kontroluje, jestli je název účtu Media Services k dispozici |
> | Akce | Microsoft.Media/locations/checkNameAvailability/action | Kontroluje, jestli je název účtu Media Services k dispozici |
> | Akce | Microsoft.Media/mediaservices/assets/delete | Odstraňovat jakékoli prostředky |
> | Akce | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Získání šifrovacího klíče prostředku |
> | Akce | Microsoft.Media/mediaservices/assets/listContainerSas/action | Seznam adres URL SAS kontejneru Asset |
> | Akce | Microsoft.Media/mediaservices/assets/read | Čtení všech prostředků |
> | Akce | Microsoft.Media/mediaservices/assets/write | Vytvořit nebo aktualizovat libovolný prostředek |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Odstraňte všechny zásady obsahu klíče |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Získání vlastností zásady s tajnými kódy |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/read | Čtení všech obsahu klíče zásad |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/write | Vytvořit nebo aktualizovat libovolný obsah klíče zásad |
> | Akce | Microsoft.Media/mediaservices/delete | Odstranění jakékoli účtu Media Services |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/delete | Odstranit libovolný filtr Event Grid |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/read | Číst libovolný filtr Event Grid |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/write | Vytvořit nebo aktualizovat libovolný filtr Event Grid |
> | Akce | Microsoft.Media/mediaservices/liveEventOperations/read | Všechny živé události operace čtení |
> | Akce | Microsoft.Media/mediaservices/liveEvents/delete | Odstranit všechny živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Odstranit všechny živé výstup |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Číst všechny živé výstup |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Vytvořit nebo aktualizovat všechny živé výstupy |
> | Akce | Microsoft.Media/mediaservices/liveEvents/read | Číst všechny živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/reset/action | Resetovat všechny operace živou událost |
> | Akce | Microsoft.Media/mediaservices/liveEvents/start/action | Spustit operaci všechny živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/stop/action | Zastavit všechny operace živou událost |
> | Akce | Microsoft.Media/mediaservices/liveEvents/write | Vytvořit nebo aktualizovat všechny živé události |
> | Akce | Microsoft.Media/mediaservices/liveOutputOperations/read | Žádné živé výstupní operace čtení |
> | Akce | Microsoft.Media/mediaservices/read | Číst všechny účtu Media Services |
> | Akce | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Přečtěte si operacím koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/delete | Odstranit libovolný koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/read | Číst libovolný koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Škálování operacím koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Spustit operacím koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Zastavit operacím koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/write | Vytvořit nebo aktualizovat libovolný koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/delete | Odstranit Lokátor streamování |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Vypsat klíče obsahu |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Seznam cest |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/read | Číst všechny Lokátor streamování |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/write | Vytvořit nebo aktualizovat libovolný Lokátor streamování |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/delete | Odstranit všechny datové proudy zásady |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/read | Čtení všech datových proudů zásad |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/write | Vytvořit nebo aktualizovat všechny zásady datových proudů |
> | Akce | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronizace úložiště klíčů pro účet připojené služby Azure Storage |
> | Akce | Microsoft.Media/mediaservices/transforms/delete | Odstranit všechny transformace |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Zrušit úlohu |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/delete | Odstranit všechny úlohy |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/read | Číst všechny úlohy |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/write | Vytvořit nebo aktualizovat libovolnou úlohu |
> | Akce | Microsoft.Media/mediaservices/transforms/read | Číst všechny transformace |
> | Akce | Microsoft.Media/mediaservices/transforms/write | Vytvořit nebo aktualizovat všechny transformace |
> | Akce | Microsoft.Media/mediaservices/write | Vytvořit nebo aktualizovat libovolný účet Media Services |
> | Akce | Microsoft.Media/operations/read | Získat dostupné operace |
> | Akce | Microsoft.Media/register/action | Zaregistruje předplatné u poskytovatele prostředků služby Media Services a povolí vytvoření účtů Media Services |
> | Akce | Microsoft.Media/unregister/action | Zruší registraci předplatného u poskytovatele prostředků služby Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Migrate/locations/assessmentOptions/read | Získá možnosti posouzení, které jsou k dispozici v daném umístění. |
> | Akce | Microsoft.Migrate/locations/checknameavailability/action | Zkontroluje dostupnost názvu prostředku pro dané předplatné v daném umístění. |
> | Akce | Microsoft.Migrate/Operations/read | Zobrazí seznam operací dostupných v poskytovateli prostředků Microsoft.Migrate. |
> | Akce | Microsoft.Migrate/projects/assessments/read | Vypíše posouzení v projektu. |
> | Akce | Microsoft.Migrate/projects/delete | Odstraní projekt. |
> | Akce | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Získá vlastnosti posouzeného počítače. |
> | Akce | Microsoft.Migrate/projects/groups/assessments/delete | Odstraní posouzení. |
> | Akce | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Stáhne adresu URL sestavy posouzení. |
> | Akce | Microsoft.Migrate/projects/groups/assessments/read | Získá vlastnosti posouzení. |
> | Akce | Microsoft.Migrate/projects/groups/assessments/write | Vytvoří nové posouzení nebo aktualizuje stávající. |
> | Akce | Microsoft.Migrate/projects/groups/delete | Odstraní skupinu. |
> | Akce | Microsoft.Migrate/projects/groups/read | Získá vlastnosti skupiny. |
> | Akce | Microsoft.Migrate/projects/groups/write | Vytvoří novou skupinu nebo aktualizuje stávající. |
> | Akce | Microsoft.Migrate/projects/keys/action | Získá sdílené klíče projektu. |
> | Akce | Microsoft.Migrate/projects/machines/read | Získá vlastnosti počítače. |
> | Akce | Microsoft.Migrate/projects/read | Získá vlastnosti projektu. |
> | Akce | Microsoft.Migrate/projects/write | Vytvoří nový projekt nebo aktualizuje stávající. |
> | Akce | Microsoft.Migrate/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft.Migrate. |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.NetApp/locations/operationresults/read | Načte prostředek výsledek operace. |
> | Akce | Microsoft.NetApp/locations/read | Čtení, což je dostupnost zkontrolujte prostředek. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Odstraní prostředek fondu. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/read | Načte prostředek fondu. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/delete | Odstranění prostředku svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/delete | Odstraní cílový prostředek připojení. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/read | Přečte cílový prostředek připojení. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/write | Zapíše cílový prostředek připojení. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/read | Přečte prostředku svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/delete | Odstraní prostředek snímku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/read | Načte prostředek snímku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/write | Zapíše prostředků snímku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/write | Zapíše prostředku svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/write | Zapíše fondu zdrojů. |
> | Akce | Microsoft.NetApp/netAppAccounts/delete | Odstraní prostředek účtu. |
> | Akce | Microsoft.NetApp/netAppAccounts/read | Načte prostředek účtu. |
> | Akce | Microsoft.NetApp/netAppAccounts/write | Zapíše prostředek účtu. |
> | Akce | Microsoft.NetApp/Operations/read | Přečte operace prostředků. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl předdefinované zásady |
> | Akce | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Dostupné možnosti Ssl aplikace brány |
> | Akce | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Získá služba Application Gateway sad pravidel k dispozici Waf |
> | Akce | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Připojí fond adres back-endu aplikace brány |
> | Akce | Microsoft.Network/applicationGateways/backendhealth/action | Získá stav back-endu aplikace brány |
> | Akce | Microsoft.Network/applicationGateways/delete | Odstraní službu application gateway |
> | Akce | Microsoft.Network/applicationGateways/read | Získá službu application gateway |
> | Akce | Microsoft.Network/applicationGateways/start/action | Spuštění služby application gateway |
> | Akce | Microsoft.Network/applicationGateways/stop/action | Zastaví službu application gateway |
> | Akce | Microsoft.Network/applicationGateways/write | Službu application gateway vytvoří nebo aktualizuje služby application gateway |
> | Akce | Microsoft.Network/applicationSecurityGroups/delete | Odstraní skupinu zabezpečení aplikace |
> | Akce | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Konfigurace protokolu IP se připojí k skupiny zabezpečení aplikací. |
> | Akce | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Spojí pravidlo zabezpečení pro skupiny zabezpečení aplikací. |
> | Akce | Microsoft.Network/applicationSecurityGroups/read | Získá identifikátor skupiny zabezpečení aplikace. |
> | Akce | Microsoft.Network/applicationSecurityGroups/write | Vytvoří skupinu zabezpečení aplikace nebo aktualizuje existující skupinu zabezpečení aplikace. |
> | Akce | Microsoft.Network/azureFirewallFqdnTags/read | Získá plně kvalifikovaný název domény značky Azure bránu Firewall |
> | Akce | Microsoft.Network/azurefirewalls/delete | Odstranění brány Firewall na Azure |
> | Akce | Microsoft.Network/azurefirewalls/read | Získat Azure bránu Firewall |
> | Akce | Microsoft.Network/azurefirewalls/write | Vytvoří nebo aktualizuje brány Firewall Azure |
> | Akce | Microsoft.Network/bastionHosts/delete | Odstraní Bastion Host |
> | Akce | Microsoft.Network/bastionHosts/read | Získá Bastion Host |
> | Akce | Microsoft.Network/bastionHosts/write | Vytvořit nebo aktualizovat Bastion Host |
> | Akce | Microsoft.Network/bgpServiceCommunities/read | Získat komunity protokolu Bgp služby |
> | Akce | Microsoft.Network/checkFrontDoorNameAvailability/action | Zkontroluje, zda je název vstupní brána k dispozici |
> | Akce | Microsoft.Network/checkTrafficManagerNameAvailability/action | Zkontroluje dostupnost názvu DNS Traffic Manageru relativní. |
> | Akce | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/sharedkey/action | Získat VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/sharedKey/read | Získá VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/sharedKey/write | Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Získá konfiguraci zařízení Vpn VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/write | Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/ddosCustomPolicies/delete | Odstraní před útoky DDoS vlastní zásady |
> | Akce | Microsoft.Network/ddosCustomPolicies/read | Získá před útoky DDoS přizpůsobit definition definici zásad |
> | Akce | Microsoft.Network/ddosCustomPolicies/write | Vytvoří zásadu před útoky DDoS přizpůsobit nebo aktualizuje existující DDoS přizpůsobené zásady |
> | Akce | Microsoft.Network/ddosProtectionPlans/delete | Odstraní plán DDoS Protection |
> | Akce | Microsoft.Network/ddosProtectionPlans/join/action | Připojí plán DDoS Protection |
> | Akce | Microsoft.Network/ddosProtectionPlans/read | Získá plán DDoS Protection |
> | Akce | Microsoft.Network/ddosProtectionPlans/write | Vytvoří plán DDoS Protection nebo aktualizuje plán DDoS Protection  |
> | Akce | Microsoft.Network/dnsoperationresults/read | Načte výsledky operace DNS |
> | Akce | Microsoft.Network/dnsoperationstatuses/read | Získá stav operace DNS  |
> | Akce | Microsoft.Network/dnszones/A/delete | Odebrat sadu záznamů daným názvem a zadejte "A" v zóně DNS. |
> | Akce | Microsoft.Network/dnszones/A/read | Získáte sadu záznamů typu "A", ve formátu JSON. Sada záznamů obsahuje seznam záznamů a hodnota TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/A/write | Vytvořit nebo aktualizovat sadu záznamů typu "A" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/AAAA/delete | Odeberte záznam sadu křestní jméno a zadejte "AAAA" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/AAAA/read | Získáte sadu záznamů typu "AAAA", ve formátu JSON. Sada záznamů obsahuje seznam záznamů a hodnota TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/AAAA/write | Vytvořit nebo aktualizovat sadu záznamů typu "AAAA" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/all/read | Získá sady záznamů DNS na typech |
> | Akce | Microsoft.Network/dnszones/CAA/delete | Odeberte záznam sadu křestní jméno a zadejte "KÁ" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/CAA/read | Získáte sadu záznamů typu "KÁ", ve formátu JSON. Sada záznamů obsahuje hodnotu TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/CAA/write | Vytvořit nebo aktualizovat sadu záznamů typu "KÁ" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/CNAME/delete | Odebrat sadu záznamů daným názvem a zadejte "CNAME" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/CNAME/read | Získáte sadu záznamů typu "CNAME", ve formátu JSON. Sada záznamů obsahuje hodnotu TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/CNAME/write | Vytvořit nebo aktualizovat sadu záznamů typu "CNAME" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/delete | Odstraňte zónu DNS ve formátu JSON. Vlastnosti zóny obsahovat značky, etag, numberOfRecordSets a maxNumberOfRecordSets. |
> | Akce | Microsoft.Network/dnszones/MX/delete | Odeberte záznam sadu křestní jméno a zadejte "MX" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/MX/read | Získáte sadu záznamů typu "MX", ve formátu JSON. Sada záznamů obsahuje seznam záznamů a hodnota TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/MX/write | Vytvořit nebo aktualizovat sadu záznamů typu "MX" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/NS/delete | Odstraní DNS sadu záznamů typu NS |
> | Akce | Microsoft.Network/dnszones/NS/read | Získá DNS sadu záznamů typu NS |
> | Akce | Microsoft.Network/dnszones/NS/write | Vytvoří nebo aktualizuje DNS sadu záznamů typu NS |
> | Akce | Microsoft.Network/dnszones/PTR/delete | Odeberte záznam sadu křestní jméno a zadejte "PTR" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/PTR/read | Získáte sadu záznamů typu "PTR", ve formátu JSON. Sada záznamů obsahuje seznam záznamů a hodnota TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/PTR/write | Vytvořit nebo aktualizovat sadu záznamů typu "PTR" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/read | Získejte zónu DNS ve formátu JSON. Vlastnosti zóny obsahovat značky, etag, numberOfRecordSets a maxNumberOfRecordSets. Všimněte si, že tento příkaz se načíst sady záznamů, které jsou obsaženy v rámci zóny. |
> | Akce | Microsoft.Network/dnszones/recordsets/read | Získá sady záznamů DNS na typech |
> | Akce | Microsoft.Network/dnszones/SOA/read | Získá sadu záznamů DNS typu SOA |
> | Akce | Microsoft.Network/dnszones/SOA/write | Vytvoří nebo aktualizuje sadu záznamů DNS typu SOA |
> | Akce | Microsoft.Network/dnszones/SRV/delete | Odebrat sadu záznamů daným názvem a zadejte "SRV" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/SRV/read | Získáte sadu záznamů typu "SRV", ve formátu JSON. Sada záznamů obsahuje seznam záznamů a hodnota TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/SRV/write | Vytvořit nebo aktualizovat sadu záznamů typu SRV |
> | Akce | Microsoft.Network/dnszones/TXT/delete | Odebrat sadu záznamů daným názvem a zadejte "TXT" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/TXT/read | Získáte sadu záznamů typu "TXT", ve formátu JSON. Sada záznamů obsahuje seznam záznamů a hodnota TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/TXT/write | Vytvořit nebo aktualizovat sadu záznamů typu "TXT" v zóně DNS. Záznamy určené nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/write | Vytvoření nebo aktualizaci zóny DNS v rámci skupiny prostředků.  Použít k aktualizaci značky pro prostředek zóny DNS. Všimněte si, že tento příkaz nelze použít k vytvoření nebo aktualizaci sady záznamů v zóně. |
> | Akce | Microsoft.Network/expressRouteCircuits/authorizations/delete | Odstraní ExpressRouteCircuit autorizaci |
> | Akce | Microsoft.Network/expressRouteCircuits/authorizations/read | Získá povolení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/authorizations/write | Vytvoří nebo aktualizuje existující autorizace ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/delete | Odstraní okruhu expressroute |
> | Akce | Microsoft.Network/expressRouteCircuits/join/action | Připojí okruhu Express Route |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Získá ArpTable ExpressRouteCircuit partnerský vztah |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Odstraní připojení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Získá připojení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Vytvoří nebo aktualizuje existující prostředek připojení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/delete | Odstraní vytvoření partnerského vztahu ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/read | Získá vytvoření partnerského vztahu ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Získá směrovací ExpressRouteCircuit partnerských vztahů tabulky |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Získá ExpressRouteCircuit partnerský vztah směrovací tabulky Souhrn |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Získá ExpressRouteCircuit partnerský vztah Stat |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/write | Vytvoří nebo aktualizuje existující ExpressRouteCircuit partnerský vztah |
> | Akce | Microsoft.Network/expressRouteCircuits/read | Získat okruhu expressroute |
> | Akce | Microsoft.Network/expressRouteCircuits/stats/read | Získá ExpressRouteCircuit Stat |
> | Akce | Microsoft.Network/expressRouteCircuits/write | Vytvoří nebo aktualizuje existující okruhu expressroute |
> | Akce | Microsoft.Network/expressRouteCrossConnections/join/action | Spojení s Express Route přes připojení |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Získá Express Route křížové připojení tabulky Arp partnerský vztah |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Odstraní Express Route křížové připojení partnerského vztahu |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/read | Získá Express Route křížové připojení partnerského vztahu |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Získá Express Route křížové připojení partnerského vztahu směrovací tabulky |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Získá Express Route křížové připojení partnerský vztah souhrn směrovací tabulky |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/write | Vytvoří pro různé připojení partnerského vztahu Express Route nebo aktualizuje existující Express Route pro různé připojení partnerský vztah |
> | Akce | Microsoft.Network/expressRouteCrossConnections/read | Získat Express Route přes připojení |
> | Akce | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Odstraní připojení Express Route |
> | Akce | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Získá připojení Express Route |
> | Akce | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Propojení Express Route vytvoří nebo aktualizuje existující připojení Express Route |
> | Akce | Microsoft.Network/expressRouteGateways/join/action | Spojí služby Expressroute Gateway |
> | Akce | Microsoft.Network/expressRouteGateways/read | Získejte bránu Expressroute |
> | Akce | Microsoft.Network/expressRoutePorts/delete | Odstraní ExpressRoutePorts |
> | Akce | Microsoft.Network/expressRoutePorts/join/action | ExpressRoutePorts spojení |
> | Akce | Microsoft.Network/expressRoutePorts/links/read | Získá ExpressRouteLink |
> | Akce | Microsoft.Network/expressRoutePorts/read | Získá ExpressRoutePorts |
> | Akce | Microsoft.Network/expressRoutePorts/write | Vytvoří nebo aktualizuje ExpressRoutePorts |
> | Akce | Microsoft.Network/expressRoutePortsLocations/read | Umístění Get Express Route porty |
> | Akce | Microsoft.Network/expressRouteServiceProviders/read | Získá poskytovatele služeb Express Route |
> | Akce | Microsoft.Network/frontDoors/backendPools/delete | Odstraní back-endový fond |
> | Akce | Microsoft.Network/frontDoors/backendPools/read | Získá back-endový fond |
> | Akce | Microsoft.Network/frontDoors/backendPools/write | Vytvoří nebo aktualizuje back-endový fond |
> | Akce | Microsoft.Network/frontDoors/delete | Odstraní přední dveře |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/delete | Odstraní koncový bod front-endu |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Zakáže HTTPS pro koncový bod front-endu |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Umožňuje HTTPS pro koncový bod front-endu |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/read | Získá koncový bod front-endu |
> | Akce | Microsoft.Network/frontDoors/frontendEndpoints/write | Vytvoří nebo aktualizuje koncový bod front-endu |
> | Akce | Microsoft.Network/frontDoors/healthProbeSettings/delete | Odstraní nastavení sondy stavu |
> | Akce | Microsoft.Network/frontDoors/healthProbeSettings/read | Získá nastavení sondy stavu |
> | Akce | Microsoft.Network/frontDoors/healthProbeSettings/write | Vytvoří nebo aktualizuje nastavení sondy stavu |
> | Akce | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Vytvoří nebo aktualizuje nastavení služby Vyrovnávání zatížení |
> | Akce | Microsoft.Network/frontDoors/loadBalancingSettings/read | Získá Vyrovnávání zatížení nastavení |
> | Akce | Microsoft.Network/frontDoors/loadBalancingSettings/write | Vytvoří nebo aktualizuje nastavení služby Vyrovnávání zatížení |
> | Akce | Microsoft.Network/frontDoors/purge/action | Vyprázdnit obsah uložený v mezipaměti z přední dveře |
> | Akce | Microsoft.Network/frontDoors/read | Získá přední dveře |
> | Akce | Microsoft.Network/frontDoors/routingRules/delete | Odstraní pravidlo směrování |
> | Akce | Microsoft.Network/frontDoors/routingRules/read | Získá pravidla směrování |
> | Akce | Microsoft.Network/frontDoors/routingRules/write | Vytvoří nebo aktualizuje pravidlo směrování |
> | Akce | Microsoft.Network/frontDoors/validateCustomDomain/action | Ověří koncový bod front-endu pro přední dveře |
> | Akce | Microsoft.Network/frontDoors/write | Vytvoří nebo aktualizuje přední dveře |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Odstraní zásadu brány Firewall webových aplikací |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Získá zásadu brány Firewall webových aplikací |
> | Akce | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Vytvoří nebo aktualizuje zásadu brány Firewall webových aplikací |
> | Akce | Microsoft.Network/interfaceEndpoints/delete | Odstraní prostředek koncového bodu rozhraní. |
> | Akce | Microsoft.Network/interfaceEndpoints/read | Získá prostředek koncového bodu rozhraní. |
> | Akce | Microsoft.Network/interfaceEndpoints/write | Vytvoří nový koncový bod rozhraní nebo aktualizuje existující koncový bod rozhraní. |
> | Akce | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Připojí pro fond adres back-endu nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/backendAddressPools/read | Získá definici zatížení adres back-endu nástroje pro vyrovnávání fondu |
> | Akce | Microsoft.Network/loadBalancers/delete | Odstraní nástroj pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Spojí konfiguraci protokolu IP front-endu nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Získá definici konfigurace IP front-endu nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Připojí nástroj pro vyrovnávání zatížení příchozí fond nat |
> | Akce | Microsoft.Network/loadBalancers/inboundNatPools/read | Získá nástroj pro vyrovnávání zatížení příchozího překladu adres definice fondu |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/delete | Odstraní pravidlo příchozí nat služby load balancer |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Připojí pravidla příchozích pravidel nat služby load balancer |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/read | Získá nástroj pro vyrovnávání zatížení příchozího definice pravidla nat |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/write | Pravidla příchozích pravidel nat služby load balancer vytvoří nebo aktualizuje stávající pravidlo příchozí nat nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/loadBalancingRules/read | Získá zatížení nástroje pro vyrovnávání zatížení vyrovnávání definice pravidla |
> | Akce | Microsoft.Network/loadBalancers/networkInterfaces/read | Získá odkazy na všech síťových rozhraní v části nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/outboundRules/read | Získá definice odchozí pravidlo nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/probes/join/action | Umožňuje použití sondy nástroje pro vyrovnávání zatížení. S touto vlastností sondu stavu oprávnění z virtuálního počítače škálovací sady můžete odkazovat, sondy. |
> | Akce | Microsoft.Network/loadBalancers/probes/read | Získá sondy nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/read | Získá definice nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/virtualMachines/read | Získá odkazy na všechny virtuální počítače v nástroji pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/write | Vytvoří nástroj pro vyrovnávání zatížení nebo aktualizuje existující pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/localnetworkgateways/delete | Odstraní LocalNetworkGateway |
> | Akce | Microsoft.Network/localnetworkgateways/read | Získá LocalNetworkGateway |
> | Akce | Microsoft.Network/localnetworkgateways/write | Vytvoří nebo aktualizuje existující LocalNetworkGateway |
> | Akce | Microsoft.Network/locations/availableDelegations/read | Získá dostupné delegace |
> | Akce | Microsoft.Network/locations/bareMetalTenants/action | Přidělí nebo ověří holé počítače Tenanta |
> | Akce | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Podpora kontroly Akcelerovanými síťovými službami |
> | Akce | Microsoft.Network/locations/checkDnsNameAvailability/read | Kontroluje, jestli název dns je k dispozici v zadaném umístění |
> | Akce | Microsoft.Network/locations/operationResults/read | Získá výsledek asynchronní příspěvku nebo operace odstranění |
> | Akce | Microsoft.Network/locations/operations/read | Získá operaci prostředek, který představuje stav asynchronní operace |
> | Akce | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Získá podporované velikosti virtuálních počítačů |
> | Akce | Microsoft.Network/locations/usages/read | Získá metriky využití prostředků |
> | Akce | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Získá seznam dostupné koncový bod služby virtuální sítě |
> | Akce | Microsoft.Network/networkIntentPolicies/delete | Odstraní zásadu záměru sítě |
> | Akce | Microsoft.Network/networkIntentPolicies/read | Získá popis záměru zásad sítě |
> | Akce | Microsoft.Network/networkIntentPolicies/write | Vytvoří zásadu záměr sítě nebo aktualizuje existující zásady sítě záměr |
> | Akce | Microsoft.Network/networkInterfaces/delete | Odstraní síťové rozhraní |
> | Akce | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Získat skupiny zabezpečení sítě nakonfigurované na síťových rozhraní z virtuálního počítače |
> | Akce | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Získejte směrovací tabulku nakonfigurovaný v síťovém rozhraní virtuálního počítače |
> | Akce | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Spojí konfigurace protokolu IP síťového rozhraní. |
> | Akce | Microsoft.Network/networkInterfaces/ipconfigurations/read | Získá definici konfigurace ip rozhraní sítě.  |
> | Akce | Microsoft.Network/networkInterfaces/join/action | Připojí k síťovému rozhraní virtuálního počítače |
> | Akce | Microsoft.Network/networkInterfaces/loadBalancers/read | Získá všechny nástroje pro vyrovnávání zatížení, které se síťové rozhraní je součástí |
> | Akce | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Akce | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Odstraní konfiguraci síťového rozhraní Tap. |
> | Akce | Microsoft.Network/networkInterfaces/tapConfigurations/read | Získá konfiguraci síťového rozhraní Tap. |
> | Akce | Microsoft.Network/networkInterfaces/tapConfigurations/write | Klepněte na konfiguraci síťového rozhraní vytvoří nebo aktualizuje existující síťové rozhraní klepněte na konfiguraci. |
> | Akce | Microsoft.Network/networkInterfaces/write | Vytvoří síťové rozhraní nebo aktualizuje existující síťové rozhraní.  |
> | Akce | Microsoft.Network/networkProfiles/delete | Odstraní síťový profil |
> | Akce | Microsoft.Network/networkProfiles/read | Získá profil sítě |
> | Akce | Microsoft.Network/networkProfiles/removeContainers/action | Odstraní kontejnery |
> | Akce | Microsoft.Network/networkProfiles/setContainers/action | Nastaví kontejnery |
> | Akce | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Nastaví kontejner síťová rozhraní |
> | Akce | Microsoft.Network/networkProfiles/write | Vytvoří nebo aktualizuje profil sítě |
> | Akce | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Získá výchozí definici pravidla zabezpečení |
> | Akce | Microsoft.Network/networkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě |
> | Akce | Microsoft.Network/networkSecurityGroups/join/action | Připojí skupinu zabezpečení sítě |
> | Akce | Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/delete | Odstraní pravidlo zabezpečení |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/read | Získá definice pravidla zabezpečení |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/write | Vytvoří pravidlo zabezpečení nebo aktualizuje stávající pravidlo zabezpečení |
> | Akce | Microsoft.Network/networkSecurityGroups/write | Vytvoří skupinu zabezpečení sítě nebo aktualizuje stávající skupina zabezpečení sítě |
> | Akce | Microsoft.Network/networkWatchers/availableProvidersList/action | Vrátí všechny dostupné internet poskytovatelů služeb pro zadanou oblast Azure. |
> | Akce | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Vrátí skóre relativní latence pro internet poskytovatelé služeb ze zadaného umístění do oblastí Azure. |
> | Akce | Microsoft.Network/networkWatchers/configureFlowLog/action | Nakonfiguruje protokolování toků pro cílový prostředek. |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/delete | Odstraní monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Monitorování připojení mezi koncovými body zadaný dotaz |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/read | Podrobnosti monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Zahájení monitorování připojení mezi zadaných koncových bodů |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Zastavit/pozastavit monitorování připojení mezi zadaných koncových bodů |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/write | Vytvoří monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectivityCheck/action | Ověří možnost navázání přímého připojení TCP z virtuálního počítače do daného koncového bodu, včetně jiného virtuálního počítače nebo libovolné vzdálený server. |
> | Akce | Microsoft.Network/networkWatchers/delete | Odstraní sledovací proces sítě |
> | Akce | Microsoft.Network/networkWatchers/ipFlowVerify/action | Vrátí, zda je paket povolený nebo zakázaný do nebo z konkrétního cíle. |
> | Akce | Microsoft.Network/networkWatchers/lenses/delete | Odstraní přehledu |
> | Akce | Microsoft.Network/networkWatchers/lenses/query/action | Dotaz monitorování síťového provozu na zadaný koncový bod |
> | Akce | Microsoft.Network/networkWatchers/lenses/read | Získat podrobnosti o přehledu |
> | Akce | Microsoft.Network/networkWatchers/lenses/start/action | Začít sledovat síťový provoz na zadaný koncový bod |
> | Akce | Microsoft.Network/networkWatchers/lenses/stop/action | Zastavit/pozastavit monitorování síťového provozu na zadaný koncový bod |
> | Akce | Microsoft.Network/networkWatchers/lenses/write | Vytvoří přehledu |
> | Akce | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostika konfiguraci sítě. |
> | Akce | Microsoft.Network/networkWatchers/nextHop/action | Zadaný cíl a cílovou IP adresu vrátí typ dalšího segmentu směrování a další Doufáme, že IP adresa. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/delete | Odstraní zachytávání paketů |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Získá informace o vlastnostech a stav prostředku zachycení paketů. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/read | Získat definici zachytávání paketů |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Zastavte spuštěné relace zachytávání paketů. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/write | Vytvoří zachytávání paketů |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/delete | Odstraní PingMesh |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/read | Získat podrobnosti o PingMesh |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/start/action | Spustit PingMesh mezi zadaným virtuálních počítačů |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Zastavit PingMesh mezi zadaným virtuálních počítačů |
> | Akce | Microsoft.Network/networkWatchers/pingMeshes/write | Vytvoří PingMesh |
> | Akce | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Monitorování připojení mezi koncovými body zadaný dotaz batch |
> | Akce | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Získá stav toku protokolování pro prostředek. |
> | Akce | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Získá řešení problémů s výsledkem z dříve spuštěné nebo aktuálně spuštění operace pro řešení potíží. |
> | Akce | Microsoft.Network/networkWatchers/read | Získat definici sledovací proces sítě |
> | Akce | Microsoft.Network/networkWatchers/securityGroupView/action | Zobrazte pravidla skupiny zabezpečení sítě nakonfigurovaná a efektivní použít na virtuálním počítači. |
> | Akce | Microsoft.Network/networkWatchers/topology/action | Získá úroveň zobrazení síťových prostředků a jejich vztahy ve skupině prostředků. |
> | Akce | Microsoft.Network/networkWatchers/troubleshoot/action | Spustí se řešení potíží s prostředkem sítě v Azure. |
> | Akce | Microsoft.Network/networkWatchers/write | Sledovací proces sítě vytvoří nebo aktualizuje existující sledovací proces sítě |
> | Akce | Microsoft.Network/operations/read | Získat dostupné operace |
> | Akce | Microsoft.Network/p2sVpnGateways/delete | Odstraní P2SVpnGateway. |
> | Akce | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Vytvořit profil sítě Vpn pro P2SVpnGateway |
> | Akce | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Získá připojení P2S Vpn povoluje pro P2SVpnGateway |
> | Akce | Microsoft.Network/p2sVpnGateways/read | Získá P2SVpnGateway. |
> | Akce | Microsoft.Network/p2sVpnGateways/write | Vloží P2SVpnGateway. |
> | Akce | Microsoft.Network/privateLinkServices/delete | Odstraní prostředek služby soukromého odkazu. |
> | Akce | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/delete | Odstraní koncový bod připojení k rozhraní. |
> | Akce | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/read | Získá definice rozhraní koncového bodu připojení. |
> | Akce | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/write | Vytvoří nové připojení koncového bodu rozhraní nebo aktualizuje existující koncový bod připojení rozhraní. |
> | Akce | Microsoft.Network/privateLinkServices/read | Získá prostředek služby soukromého odkazu. |
> | Akce | Microsoft.Network/privateLinkServices/write | Vytvoří novou službu privátní propojení nebo aktualizuje existující službu soukromého odkazu. |
> | Akce | Microsoft.Network/publicIPAddresses/delete | Odstraní veřejnou Ip adresu. |
> | Akce | Microsoft.Network/publicIPAddresses/join/action | Připojí veřejná ip adresa |
> | Akce | Microsoft.Network/publicIPAddresses/read | Získá definici veřejnou ip adresu. |
> | Akce | Microsoft.Network/publicIPAddresses/write | Vytvoří veřejnou Ip adresu nebo aktualizuje stávající veřejnou Ip adresu.  |
> | Akce | Microsoft.Network/publicIPPrefixes/delete | Odstraní veřejných předpon adres Ip |
> | Akce | Microsoft.Network/publicIPPrefixes/join/action | Připojí PublicIPPrefix |
> | Akce | Microsoft.Network/publicIPPrefixes/read | Získá definice veřejných předpon adres Ip |
> | Akce | Microsoft.Network/publicIPPrefixes/write | Vytvoří předponu veřejné Ip nebo aktualizuje existující veřejných předpon adres Ip |
> | Akce | Microsoft.Network/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.Network/routeFilters/delete | Odstraní definici filtru trasy |
> | Akce | Microsoft.Network/routeFilters/join/action | Filtr tras připojuje |
> | Akce | Microsoft.Network/routeFilters/read | Získá definice filtru trasy |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/delete | Odstraní definici pravidla filtru trasy |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/read | Získá definici pravidla filtru trasy |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/write | Pravidlo filtru tras vytvoří nebo aktualizuje existující pravidlo filtru tras |
> | Akce | Microsoft.Network/routeFilters/write | Filtr tras vytvoří nebo aktualizuje existující rotue filtr |
> | Akce | Microsoft.Network/routeTables/delete | Odstraní definici tabulky směrování |
> | Akce | Microsoft.Network/routeTables/join/action | Připojí směrovací tabulky |
> | Akce | Microsoft.Network/routeTables/read | Získá definice tabulky směrování |
> | Akce | Microsoft.Network/routeTables/routes/delete | Odstraní definici trasy |
> | Akce | Microsoft.Network/routeTables/routes/read | Získá definice trasy |
> | Akce | Microsoft.Network/routeTables/routes/write | Vytvoří trasu nebo aktualizuje existující trasy |
> | Akce | Microsoft.Network/routeTables/write | Vytvoří směrovací tabulku nebo aktualizuje existující rotue tabulky |
> | Akce | Microsoft.Network/securegateways/delete | Odstranit zabezpečené brány |
> | Akce | Microsoft.Network/securegateways/read | Získat zabezpečené brány |
> | Akce | Microsoft.Network/securegateways/write | Vytvoří nebo aktualizuje zabezpečená brána |
> | Akce | Microsoft.Network/serviceEndpointPolicies/delete | Odstraní zásady koncového bodu služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/join/action | Připojí zásady koncového bodu služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Připojí podsítě na zásady koncových bodů služeb |
> | Akce | Microsoft.Network/serviceEndpointPolicies/read | Získá popis zásad koncového bodu služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Odstraní definice zásad koncového bodu služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Získá %{decription/ definice zásad koncového bodu služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Definice zásad koncového bodu služby vytvoří nebo aktualizuje existující definice zásad koncového bodu služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/write | Zásady koncového bodu služby vytvoří nebo aktualizuje existující zásady koncového bodu služby |
> | Akce | Microsoft.Network/trafficManagerGeographicHierarchies/read | Získá hierarchii Geographic Traffic Manageru obsahující oblasti, ve kterých je možné s geografickou metodu směrování provozu |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Odstraní koncový bod Azure z existujícího profilu Traffic Manageru. Traffic Manager přestane směrování provozu do odstraněné koncového bodu Azure. |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Získá koncový bod Azure, který patří do profilu Traffic Manageru, včetně všechny vlastnosti tohoto koncového bodu Azure. |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Přidat nový koncový bod Azure v existující profil Traffic Manageru nebo aktualizovat vlastnosti existující koncový bod Azure v tomto profilu služby Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/delete | Odstraňte profil služby Traffic Manager. Všechna nastavení spojená s profilem Traffic Manageru se ztratí a profil už umožňuje směrovat provoz. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Odstraní externí koncový bod z existujícího profilu Traffic Manageru. Traffic Manager přestane směrování provozu do odstraněné externí koncový bod. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Získá externí koncový bod, který patří do profilu Traffic Manageru, včetně všech vlastností tuto externí koncový bod. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Přidat novou externí koncový bod v existující profil Traffic Manageru nebo aktualizovat vlastnosti existujících externí koncový bod v tomto profilu služby Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Získá Heat mapa Traffic Manager pro daný profil Traffic Manageru, který obsahuje data počty a latence dotazu podle umístění a zdrojové IP. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Odstraní vnořený koncový bod z existujícího profilu Traffic Manageru. Traffic Manager přestane směrování provozu do odstranil koncový bod vnořené. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Získá koncový bod vnořené, který patří do profilu Traffic Manageru, včetně všechny vlastnosti tohoto koncového bodu vnořené. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Přidat nový koncový bod vnořené v existující profil Traffic Manageru nebo aktualizovat vlastnosti existujícího vnořený koncový bod v tomto profilu služby Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/read | Získání konfigurace profilu Traffic Manageru. To zahrnuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncového bodu a seznam koncových bodů směrují tento profil Traffic Manageru. |
> | Akce | Microsoft.Network/trafficManagerProfiles/write | Vytvořit profil služby Traffic Manager nebo změnit konfiguraci stávající profilu Traffic Manageru.<br>To zahrnuje povolení nebo zakázání profilu a úprava nastavení DNS, nastavení směrování provozu nebo nastavení monitorování koncového bodu.<br>Koncové body směrovaný tímto profil Traffic Manageru můžete třeba přidat, odebrat, povolit nebo zakázat. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Odstraní klíč úroveň předplatného použitých pro shromažďování metrik v reálném čase uživatele. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/read | Získání úrovně předplatného klíče použité pro shromažďování metrik v reálném čase uživatele. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/write | Vytvoří nový klíč úrovni předplatného se použije pro shromažďování metrik v reálném čase uživatele. |
> | Akce | Microsoft.Network/unregister/action | Zruší registraci předplatného |
> | Akce | Microsoft.Network/virtualHubs/delete | Odstraní virtuální rozbočovač |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Odstraní připojení HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Získání připojení HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Vytvořit nebo aktualizovat připojení HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/read | Získejte virtuální rozbočovač |
> | Akce | Microsoft.Network/virtualHubs/write | Vytvořit nebo aktualizovat virtuální rozbočovač |
> | Akce | Microsoft.Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/virtualNetworkGateways/delete | Odstraní virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generoval balíček VpnClient pro virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | Generování VpnProfile balíčku pro VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Získá virtualNetworkGateway Inzerovat trasy |
> | Akce | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Získá stav partnerského protokolu bgp virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Získá trasy virtualnetworkgateway se naučili |
> | Akce | Microsoft.Network/virtualnetworkgateways/getvpnclientconnectionhealth/Action | Získejte na stav připojení klienta Vpn pro VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Získejte Vpnclient Ipsec parametry pro klienta VirtualNetworkGateway P2S. |
> | Akce | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Získá adresu URL balíčku profilu vpn předem generovaného klienta |
> | Akce | Microsoft.Network/virtualNetworkGateways/read | Získá VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/reset/Action | Obnoví virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | Obnovte sdílený klíč klienta VPN VirtualNetworkGateway P2S klienta. |
> | Akce | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Nastavte parametry pro klienta VirtualNetworkGateway P2S Vpnclient Ipsec. |
> | Akce | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Seznam podporovaných zařízení Vpn |
> | Akce | Microsoft.Network/virtualNetworkGateways/write | Vytvoří nebo aktualizuje VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualNetworks/BastionHosts/action | Získá refrences Bastion Host ve virtuální síti. |
> | Akce | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Zkontrolujte, jestli je Ip adresa k dispozici v zadané virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/delete | Odstraní virtuální síť |
> | Akce | Microsoft.Network/virtualNetworks/peer/action | Partnerský vztah virtuální sítě s jinou virtuální sítí |
> | Akce | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/delete | Odstraní podsíť virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Prostředků, jako je například účet úložiště nebo databáze SQL se připojí k podsíti. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/read | Získá definici podsítě virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Získá odkazy na všechny virtuální počítače v podsíti virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/write | Vytvoří podsíť virtuální sítě nebo aktualizuje existující podsíti virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/usages/read | Získat použití IP pro každou podsíť ve virtuální síti |
> | Akce | Microsoft.Network/virtualNetworks/virtualMachines/read | Získá odkazy na všechny virtuální počítače ve virtuální síti |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Odstraní partnerského vztahu virtuálních sítí |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Získá definici partnerského vztahu virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Vytvoří partnerský vztah virtuální sítě nebo aktualizuje existující virtuální sítě partnerský vztah |
> | Akce | Microsoft.Network/virtualNetworks/write | Vytvoří virtuální síť nebo aktualizuje existující virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworkTaps/delete | Odstranit klepnutím na virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/join/action | Připojí jedním klepnutím na virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/read | Získat klepnutím na virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/write | Vytvořit nebo aktualizovat klepnutím na virtuální síť |
> | Akce | Microsoft.Network/virtualWans/delete | Odstraní virtuální sítě Wan |
> | Akce | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/delete | Odstraní virtuální sítě Wan P2SVpnServerConfiguration |
> | Akce | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Získá virtuální sítě Wan P2SVpnServerConfiguration definice |
> | Akce | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/write | Vytvoří virtuální síť Wan P2SVpnServerConfiguration nebo aktualizuje existující virtuální sítě Wan P2SVpnServerConfiguration |
> | Akce | Microsoft.Network/virtualWans/read | A získejte virtuální síť Wan |
> | Akce | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Získá podporované VirtualWan zprostředkovatele zabezpečení. |
> | Akce | Microsoft.Network/virtualWans/virtualHubs/read | Získá všechny virtuální rozbočovače, které odkazují na virtuální sítě Wan. |
> | Akce | Microsoft.Network/virtualwans/vpnconfiguration/action | Získá konfiguraci sítě Vpn |
> | Akce | Microsoft.Network/virtualWans/vpnSites/read | Získá všechny servery VPN, které odkazují na virtuální sítě Wan. |
> | Akce | Microsoft.Network/virtualWans/write | Vytvořit nebo aktualizovat virtuální sítě Wan |
> | Akce | Microsoft.Network/vpnGateways/delete | Odstraní Brána VPN. |
> | Akce | Microsoft.Network/vpngateways/listvpnconnectionshealth/Action | Získá stav připojení pro všechny nebo podmnožinu připojení Brána VPN |
> | Akce | Microsoft.Network/vpnGateways/read | Získá Brána VPN. |
> | Akce | Microsoft.Network/vpngateways/reset/Action | Obnoví Brána VPN |
> | Akce | microsoft.network/vpnGateways/vpnConnections/delete | Odstraní připojení VpnConnection. |
> | Akce | microsoft.network/vpnGateways/vpnConnections/read | Získá připojení VpnConnection. |
> | Akce | microsoft.network/vpnGateways/vpnConnections/write | Vloží připojení VpnConnection. |
> | Akce | Microsoft.Network/vpnGateways/write | Vloží Brána VPN. |
> | Akce | Microsoft.Network/vpnsites/delete | Odstraní prostředek serveru Vpn. |
> | Akce | Microsoft.Network/vpnsites/read | Získá prostředek serveru Vpn. |
> | Akce | Microsoft.Network/vpnsites/write | Vytvoří nebo aktualizuje prostředek serveru Vpn. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Zkontroluje, jestli je ve službě NotificationHub k dispozici název prostředku oboru názvů, nebo ne. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Odstraňte autorizační pravidlo Namespace. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Autorizační pravidlo oboru názvů – opětovné vygenerování primárního nebo sekundárního klíče. Zadejte klíč, který je nutné znovu vygenerovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Vytvořit Namespace úrovně autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla, primární a sekundární klíč. |
> | Akce | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Zkontroluje, jestli je v oboru názvů k dispozici název NotificationHub, nebo ne. |
> | Akce | Microsoft.NotificationHubs/Namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Získá seznam autorizačních pravidel Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Odstranit autorizační pravidla Centra oznámení |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Získá připojovací řetězec k Centru oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Získá seznam autorizačních pravidel Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Autorizační pravidlo Centra oznámení – opětovné vygenerování primárního nebo sekundárního klíče. Zadejte klíč, který je nutné znovu vygenerovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Vytvoření autorizační pravidla centra oznámení a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla, primární a sekundární klíč. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Poslat testovací nabízené oznámení |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Odstraní prostředek Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Získat seznam metrik Namespace popisů prostředků |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Získáte přihlašovací údaje systému oznámení platformy centra oznámení všechny. To zahrnuje, přihlašovací údaje služby nabízených oznámení Windows, MPNS, APNS, GCM a Baidu |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Získá seznam popisů prostředků Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Vytvoření centra oznámení a aktualizovat jeho vlastnosti. Jeho vlastností patří hlavně přihlašovací údaje systému oznámení platformy. Autorizační pravidla a hodnota TTL |
> | Akce | Microsoft.NotificationHubs/Namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/write | Vytvoření prostředku Namespace a aktualizovat jeho vlastnosti. Značky a kapacita Namespace jsou vlastnosti, které je možné aktualizovat. |
> | Akce | Microsoft.NotificationHubs/operationResults/read | Vrátí výsledky operace pro poskytovatele služby Notification Hubs. |
> | Akce | Microsoft.NotificationHubs/operations/read | Vrátí seznam podporovaných operací pro poskytovatele služby Notification Hubs. |
> | Akce | Microsoft.NotificationHubs/register/action | Zaregistruje předplatné u poskytovatele prostředků NotificationHubs a povolí vytváření oborů názvů a NotificationHubs. |
> | Akce | Microsoft.NotificationHubs/unregister/action | Zruší registraci předplatného u poskytovatele prostředků NotificationHubs a povolí vytváření oborů názvů a služby NotificationHubs. |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OffAzure/HyperVSites/clusters/read | Získá vlastnosti clusteru Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/clusters/read | Získá vlastnosti clusteru Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/clusters/write | Vytvoří nebo aktualizuje cluster Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/clusters/write | Vytvoří nebo aktualizuje cluster Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/delete | Odstraní lokalitu Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/delete | Odstraní lokalitu Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/hosts/read | Získá vlastnosti hostitele Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/hosts/read | Získá vlastnosti hostitele Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/hosts/write | Vytvoří nebo aktualizuje hostitele Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/hosts/write | Vytvoří nebo aktualizuje hostitele Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/jobs/read | Získá vlastnosti úlohy Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/jobs/read | Získá vlastnosti úlohy Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/machines/read | Získá vlastnosti počítače Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/machines/read | Získá vlastnosti počítače Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/machines/stop/action | Zastaví počítačů Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/machines/stop/action | Zastaví počítačů Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Získá vlastnosti stav operace Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Získá vlastnosti stav operace Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/read | Získá vlastnosti do lokality Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/read | Získá vlastnosti do lokality Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/refresh/action | Aktualizuje objekty v rámci lokality Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/refresh/action | Aktualizuje objekty v rámci lokality Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Získá vlastnosti technologie Hyper-V, účty spustit jako |
> | Akce | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Získá vlastnosti technologie Hyper-V, účty spustit jako |
> | Akce | Microsoft.OffAzure/HyperVSites/usage/read | Získá využití do lokality Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/usage/read | Získá využití do lokality Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/write | Vytvoří nebo aktualizuje lokalita Hyper-V |
> | Akce | Microsoft.OffAzure/HyperVSites/write | Vytvoří nebo aktualizuje lokalita Hyper-V |
> | Akce | Microsoft.OffAzure/Operations/read | Načte zpřístupněné operace. |
> | Akce | Microsoft.OffAzure/VMwareSites/delete | Odstraní lokalitu VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/delete | Odstraní lokalitu VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/jobs/read | Získá vlastnosti úlohy VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/jobs/read | Získá vlastnosti úlohy VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/read | Získá vlastnosti počítače VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/read | Získá vlastnosti počítače VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/stop/action | Zastaví počítače VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/machines/stop/action | Zastaví počítače VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Získá vlastnosti stav operace VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Získá vlastnosti stav operace VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/read | Získá vlastnosti lokality VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/read | Získá vlastnosti lokality VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/refresh/action | Aktualizuje objekty v rámci lokality VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/refresh/action | Aktualizuje objekty v rámci lokality VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Získá vlastnosti VMware účty spustit jako |
> | Akce | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Získá vlastnosti VMware účty spustit jako |
> | Akce | Microsoft.OffAzure/VMwareSites/usage/read | Získá využití lokality VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/usage/read | Získá využití lokality VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/vcenters/read | Získá vlastnosti serveru VMware vCenter |
> | Akce | Microsoft.OffAzure/VMwareSites/vcenters/read | Získá vlastnosti serveru VMware vCenter |
> | Akce | Microsoft.OffAzure/VMwareSites/vcenters/write | Vytvoří nebo aktualizuje serveru VMware vCenter |
> | Akce | Microsoft.OffAzure/VMwareSites/vcenters/write | Vytvoří nebo aktualizuje serveru VMware vCenter |
> | Akce | Microsoft.OffAzure/VMwareSites/write | Vytvoří nebo aktualizuje lokalita VMware |
> | Akce | Microsoft.OffAzure/VMwareSites/write | Vytvoří nebo aktualizuje lokalita VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OperationalInsights/linkTargets/read | Vypíše existující účty, které nejsou přidružené předplatnému Azure. Chcete-li propojit toto předplatné Azure s pracovním prostorem, použijte vrátila tato operace ve vlastnosti id zákazníka operace vytvořit pracovní prostor id zákazníka. |
> | Akce | Microsoft.operationalinsights/Operations/Read | Obsahuje seznam všech dostupných operací OperationalInsights Rest API. |
> | Akce | Microsoft.OperationalInsights/register/action | Registrace předplatného u poskytovatele prostředků. |
> | Akce | Microsoft.OperationalInsights/workspaces/analytics/query/action | Hledejte pomocí nového modulu. |
> | Akce | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Získejte schéma vyhledávání V2. |
> | Akce | Microsoft.OperationalInsights/workspaces/api/query/action | Hledejte pomocí nového modulu. |
> | Akce | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Získejte schéma vyhledávání V2. |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Odstranit obor konfigurace |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Získat obor konfigurace |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Nastavit obor konfigurace |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/delete | Odstranění zdrojů dat v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/read | Získání zdrojů dat v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/write | Vytvoření nebo aktualizace zdrojů dat v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/delete | Odstraní pracovní prostor. Pokud se pracovní prostor propojený s pracovním prostorem existující v okamžiku vytvoření pracovní prostor propojený na neodstranil. |
> | Akce | Microsoft.OperationalInsights/workspaces/gateways/delete | Odebere s bránou nakonfigurovanou pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Vygeneruje registrační certifikát pro pracovní prostor. Tento certifikát slouží k připojení Microsoft System Center Operation Manageru k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Vypne intelligence pack pro daný pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Zapne intelligence pack pro daný pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Obsahuje seznam všech sad intelligence Pack, které jsou viditelné pro daného pracovního prostoru a také uvádí, zda sada povolený nebo zakázaný pro tento pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Odstraňte propojené služby v daném pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/read | Získejte propojené služby v daném pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/write | Vytvořte nebo aktualizujte propojené služby v daném pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/listKeys/action | Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/listKeys/read | Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/managementGroups/read | Získá názvy a metadata skupin pro správu System Center Operations Manager připojené k tomuto pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Získá definice metrik v pracovním prostoru |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Odstraňte nastavení oznámení daného uživatele pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Získáte nastavení oznámení daného uživatele pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Nastavte nastavení oznámení daného uživatele pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/purge/action | Odstranit zadaná data z pracovního prostoru |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Přečte data z tabulky ADAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Přečte data z tabulky ADReplicationResult. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Přečte data z tabulky ADSecurityAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Alert/read | Přečte data z tabulky Alert. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Přečte data z tabulky AlertHistory. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Čtení dat z tabulky AppCenterError |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Přečte data z tabulky ApplicationInsights. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Čtení dat z tabulky mají |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Přečte data z tabulky AzureActivity. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Přečte data z tabulky AzureMetrics. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Přečte data z tabulky BoundPort. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Přečte data z tabulky CommonSecurityLog. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Přečte data z tabulky ComputerGroup. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Přečte data z tabulky ConfigurationChange. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Přečte data z tabulky ConfigurationData. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Přečte data z tabulky ContainerImageInventory. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Přečte data z tabulky ContainerInventory. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Přečte data z tabulky ContainerLog. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Přečte data z tabulky ContainerServiceLog. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Přečte data z tabulky DeviceAppCrash. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Přečte data z tabulky DeviceAppLaunch. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Přečte data z tabulky DeviceCalendar. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Přečte data z tabulky DeviceCleanup. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Přečte data z tabulky DeviceConnectSession. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Přečte data z tabulky DeviceEtw. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Přečte data z tabulky DeviceHardwareHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Přečte data z tabulky DeviceHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Přečte data z tabulky DeviceHeartbeat. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Přečte data z tabulky DeviceSkypeHeartbeat. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Přečte data z tabulky DeviceSkypeSignIn. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Přečte data z tabulky DeviceSleepState. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Přečte data z tabulky DHAppFailure. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Přečte data z tabulky DHAppReliability. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Přečte data z tabulky DHDriverReliability. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Přečte data z tabulky DHLogonFailures. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Přečte data z tabulky DHLogonMetrics. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Přečte data z tabulky DHOSCrashData. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Přečte data z tabulky DHOSReliability. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Přečte data z tabulky DHWipAppLearning. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Přečte data z tabulky DnsEvents. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Přečte data z tabulky DnsInventory. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Přečte data z tabulky ETWEvent. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Event/read | Přečte data z tabulky Event. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Přečte data z tabulky ExchangeAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Přečte data z tabulky ExchangeOnlineAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Přečte data z tabulky Heartbeat. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Přečte data z tabulky IISAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Přečte data z tabulky InboundConnection. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Čtení dat z tabulky KubeEvents |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Přečte data z tabulky KubeNodeInventory. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Přečte data z tabulky KubePodInventory. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Čtení dat z tabulky KubeServices |
> | Akce | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Přečte data z tabulky LinuxAuditLog. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Přečte data z tabulky MAApplication. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Přečte data z tabulky MAApplicationHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Přečte data z tabulky MAApplicationHealthAlternativeVersions. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Přečte data z tabulky MAApplicationHealthIssues. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Přečte data z tabulky MAApplicationInstance. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Přečte data z tabulky MAApplicationInstanceReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Přečte data z tabulky MAApplicationReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Přečte data z tabulky MADeploymentPlan. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Přečte data z tabulky MADevice. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Čtení dat z tabulky MADeviceNotEnrolled |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Přečte data z tabulky MADevicePnPHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Přečte data z tabulky MADevicePnPHealthAlternativeVersions. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Přečte data z tabulky MADevicePnPHealthIssues. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Přečte data z tabulky MADeviceReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Přečte data z tabulky MADriverInstanceReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Přečte data z tabulky MADriverReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Přečte data z tabulky MAOfficeAddin. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Přečte data z tabulky MAOfficeAddinHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Přečte data z tabulky MAOfficeAddinHealthIssues. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Přečte data z tabulky MAOfficeAddinInstance. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Přečte data z tabulky MAOfficeAddinInstanceReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Přečte data z tabulky MAOfficeAddinReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Přečte data z tabulky MAOfficeApp. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Přečte data z tabulky MAOfficeAppHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Přečte data z tabulky MAOfficeAppInstance. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Přečte data z tabulky MAOfficeAppReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Přečte data z tabulky MAOfficeBuildInfo. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Přečte data z tabulky MAOfficeCurrencyAssessment. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Přečte data z tabulky MAOfficeCurrencyAssessmentDailyCounts. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Přečte data z tabulky MAOfficeDeploymentStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Přečte data z tabulky MAOfficeMacroHealth. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Přečte data z tabulky MAOfficeMacroHealthIssues. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Přečte data z tabulky MAOfficeMacroIssueInstanceReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Přečte data z tabulky MAOfficeMacroIssueReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Přečte data z tabulky MAOfficeMacroSummary. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Přečte data z tabulky MAOfficeSuite. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Přečte data z tabulky MAOfficeSuiteInstance. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Přečte data z tabulky MAProposedPilotDevices. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Přečte data z tabulky MAWindowsBuildInfo. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Přečte data z tabulky MAWindowsCurrencyAssessment. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Přečte data z tabulky MAWindowsCurrencyAssessmentDailyCounts. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Přečte data z tabulky MAWindowsDeploymentStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Přečte data z tabulky MAWindowsSysReqInstanceReadiness. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Přečte data z tabulky NetworkMonitoring. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Přečte data z tabulky OfficeActivity. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Operation/read | Přečte data z tabulky Operation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Přečte data z tabulky OutboundConnection. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Perf/read | Přečte data z tabulky Perf. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Přečte data z tabulky ProtectionStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/read | Spouštění dotazů nad daty v pracovním prostoru |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Přečte data z tabulky ReservedAzureCommonFields. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Přečte data z tabulky ReservedCommonFields. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Přečte data z tabulky SCCMAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Přečte data z tabulky SCOMAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Přečte data z tabulky SecurityAlert. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Přečte data z tabulky SecurityBaseline. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Přečte data z tabulky SecurityBaselineSummary. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Přečte data z tabulky SecurityDetection. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Přečte data z tabulky SecurityEvent. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Přečte data z tabulky ServiceFabricOperationalEvent. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Přečte data z tabulky ServiceFabricReliableActorEvent. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Přečte data z tabulky ServiceFabricReliableServiceEvent. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Přečte data z tabulky SfBAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Přečte data z tabulky SfBOnlineAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Přečte data z tabulky SharePointOnlineAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Čtení dat z tabulky SigninLogs |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Přečte data z tabulky SPAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Přečte data z tabulky SQLAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Přečte data z tabulky SQLQueryPerformance. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Přečte data z tabulky Syslog. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Přečte data z tabulky SysmonEvent. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Přečte data z jakéhokoli vlastního protokolu. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Přečte data z tabulky UAApp. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Přečte data z tabulky UAComputer. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Přečte data z tabulky UAComputerRank. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Přečte data z tabulky UADriver. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Přečte data z tabulky UADriverProblemCodes. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Přečte data z tabulky UAFeedback. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Přečte data z tabulky UAHardwareSecurity. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Přečte data z tabulky UAIESiteDiscovery. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Přečte data z tabulky UAOfficeAddIn. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Přečte data z tabulky UAProposedActionPlan. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Přečte data z tabulky UASysReqIssue. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Přečte data z tabulky UAUpgradedComputer. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Update/read | Přečte data z tabulky Update. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Přečte data z tabulky UpdateRunProgress. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Přečte data z tabulky UpdateSummary. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/Usage/read | Přečte data z tabulky Usage. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Čtení dat z tabulky VMBoundPort |
> | Akce | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Čtení dat z tabulky VMConnection |
> | Akce | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Přečte data z tabulky W3CIISLog. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Přečte data z tabulky WaaSDeploymentStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Přečte data z tabulky WaaSInsiderStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Přečte data z tabulky WaaSUpdateStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Přečte data z tabulky WDAVStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Přečte data z tabulky WDAVThreat. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Přečte data z tabulky WindowsClientAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Čtení dat z tabulky WindowsEvent |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Přečte data z tabulky WindowsFirewall. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Přečte data z tabulky WindowsServerAssessmentRecommendation. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WireData/read | Přečte data z tabulky WireData. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Čtení dat z tabulky WorkloadMonitoringPerf |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Přečte data z tabulky WUDOAggregatedStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Přečte data z tabulky WUDOStatus. |
> | Akce | Microsoft.OperationalInsights/workspaces/read | Získá existující pracovní prostor |
> | Akce | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Znovu vygeneruje sdílený klíč zadaný pracovní prostor |
> | Akce | Microsoft.operationalinsights/workspaces/Rules/Read | Získání všech pravidel upozornění. |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Odstraní uložený vyhledávací dotaz |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/read | Získá uložený vyhledávací dotaz |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Results/Read | Získat uložené výsledky hledání. Zastaralé |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Schedules/Actions/DELETE | Hledání naplánované akce odstranění. |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Schedules/Actions/Read | Získáte akce naplánovaných hledání. |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Schedules/Actions/Write | Vytvořit nebo aktualizovat akce naplánovaných hledání. |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Schedules/DELETE | Odstraňte naplánované prohledávání. |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Schedules/Read | Získejte naplánovaných prohledávání. |
> | Akce | Microsoft.operationalinsights/workspaces/savedsearches/Schedules/Write | Vytvořit nebo aktualizovat naplánovaných prohledávání. |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/write | Vytvoří uložený vyhledávací dotaz |
> | Akce | Microsoft.OperationalInsights/workspaces/schema/read | Získá schéma vyhledávání pro pracovní prostor.  Schéma vyhledávání zahrnuje zveřejněná pole a jejich typy. |
> | Akce | Microsoft.OperationalInsights/workspaces/search/action | Spouští vyhledávací dotaz |
> | Akce | Microsoft.operationalinsights/workspaces/Search/Read | Získání výsledků hledání. Zastaralé. |
> | Akce | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Odstraní konfiguraci úložiště. Tak se ukončí Microsoft Operational Insights ve čtení dat z účtu úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Získá konfiguraci úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Vytvoří novou konfiguraci úložiště. Tyto konfigurace se použijí k vyžádání dat z umístění, do existující účet úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Získat protokol o selhání upgradu překlad vyhledávání pracovního prostoru |
> | Akce | Microsoft.OperationalInsights/workspaces/usages/read | Získá data využití pro pracovní prostor, včetně množství dat čtených pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/write | Vytvoří nový pracovní prostor nebo odkazy na existující pracovní prostor ID zákazníka z existujícího pracovního prostoru. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OperationsManagement/managementAssociations/delete | Odstranit existující přidružení správy |
> | Akce | Microsoft.OperationsManagement/managementAssociations/read | Načíst existující přidružení správy |
> | Akce | Microsoft.OperationsManagement/managementAssociations/write | Vytvořit nové přidružení správy |
> | Akce | Microsoft.OperationsManagement/managementConfigurations/delete | Odstranit existující konfiguraci správy |
> | Akce | Microsoft.OperationsManagement/managementConfigurations/read | Načíst existující konfiguraci správy |
> | Akce | Microsoft.OperationsManagement/managementConfigurations/write | Vytvořit novou konfiguraci správy |
> | Akce | Microsoft.OperationsManagement/register/action | Registrace předplatného u poskytovatele prostředků. |
> | Akce | Microsoft.OperationsManagement/solutions/delete | Odstranit existující řešení OMS |
> | Akce | Microsoft.OperationsManagement/solutions/read | Získat existující řešení OMS |
> | Akce | Microsoft.OperationsManagement/solutions/write | Vytvořit nové řešení OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.policyinsights do

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.PolicyInsights/asyncOperationResults/read | Získá výsledek asynchronní operace. |
> | Akce | Microsoft.PolicyInsights/policyEvents/queryResults/action | Pošle dotaz na informace o událostech zásad. |
> | Akce | Microsoft.PolicyInsights/policyEvents/queryResults/read | Pošle dotaz na informace o událostech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/queryResults/action | Pošle dotaz na informace o stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/queryResults/read | Pošle dotaz na informace o stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/summarize/action | Pošle dotaz na souhrnné informace o posledních stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/summarize/read | Pošle dotaz na souhrnné informace o posledních stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Aktivuje nové hodnocení dodržování předpisů pro vybraný obor. |
> | Akce | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Pošle dotaz na informace o prostředcích, které vyžadují zásady DeployIfNotExists. |
> | Akce | Microsoft.PolicyInsights/register/action | Zaregistruje poskytovatele prostředků Policy Insights a povolí v něm akce. |
> | Akce | Microsoft.PolicyInsights/remediations/cancel/action | Zruší probíhající nápravy zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/delete | Odstraní nápravy zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/listDeployments/read | Vypíše nasazení vyžadovaná nápravou zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/read | Získá nápravy zásad. |
> | Akce | Microsoft.PolicyInsights/remediations/write | Vytvoří nebo aktualizuje nápravy zásad. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Portal/dashboards/delete | Odebere z předplatného řídicí panel. |
> | Akce | Microsoft.Portal/dashboards/read | Čte řídicí panely pro předplatné. |
> | Akce | Microsoft.Portal/dashboards/write | Umožňuje přidat nebo upravit řídicí panely u předplatného. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Kontroluje, že vyhrazenou kapacitu Power BI je uvedený název platný a nepoužívá. |
> | Akce | Microsoft.PowerBIDedicated/capacities/delete | Power BI odstraní vyhrazenou kapacitu. |
> | Akce | Microsoft.PowerBIDedicated/capacities/read | Načte informace o zadanou vyhrazenou kapacitu Power BI. |
> | Akce | Microsoft.PowerBIDedicated/capacities/resume/action | Obnoví kapacitu. |
> | Akce | Microsoft.PowerBIDedicated/capacities/skus/read | Načíst dostupné informace o SKU pro kapacitu |
> | Akce | Microsoft.PowerBIDedicated/capacities/suspend/action | Pozastaví kapacitu. |
> | Akce | Microsoft.PowerBIDedicated/capacities/write | Vytvoří nebo aktualizuje zadanou vyhrazenou kapacitu Power BI. |
> | Akce | Microsoft.PowerBIDedicated/locations/operationresults/read | Načte informace o výsledku zadané operace. |
> | Akce | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Načte informace o stavu zadané operace. |
> | Akce | Microsoft.PowerBIDedicated/operations/read | Načte informace o operacích |
> | Akce | Microsoft.PowerBIDedicated/register/action | Zaregistruje poskytovatele prostředků vyhrazené pro Power BI. |
> | Akce | Microsoft.PowerBIDedicated/skus/read | Načte informace o SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Akce | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je vnitřní operace, které používá služba |
> | Akce | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Akce | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování pro trezory služby Recovery Services |
> | Akce | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověření funkcí |
> | Akce | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci |
> | Akce | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Akce | Microsoft.RecoveryServices/register/action | Zaregistruje předplatné pro daného poskytovatele prostředků. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupconfig/read | Trezor služby vrátí konfiguraci pro Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupconfig/write | Trezor služby konfigurace aktualizací pro Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery správy zálohování zaregistrované s trezorem. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Odstranit záložní záměr ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Získat záložní záměr ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvořit záložní záměr ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Získat všechny kontejnery s podporou ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Odstraní registrovaný kontejner. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Provést dotaz pro úlohy uvnitř kontejneru |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Získat všechny položky v kontejneru |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Provede zálohování chráněné položky. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Odstraní chráněnou položku. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti objektu chráněné položky. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zřízení okamžitého obnovení položky pro chráněnou položku |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Obnoví body obnovení pro chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odvolání okamžitého obnovení položky pro chráněnou položku |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvořit položku chráněnou zálohováním. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Vytvoří registrovaný kontejner. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualizuje seznam kontejnerů. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Zrušit úlohu |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úloh. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportovat úlohy |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace exportování úlohy. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Vrátí metadata správy zálohování trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí operace zálohování trezoru služby stavu pro obnovení. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Odstranit zásady ochrany. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Získáte stav operace zásad. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Vytvoří zásady ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Vrátí seznam chránitelných položek. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Seznam všech záloh záměry ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Vrátí bezpečnostní kód PIN informace pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Trezor služby Services vrátí konfiguraci úložiště pro obnovení. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Trezor služby aktualizuje konfiguraci úložiště pro Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery služby Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Ověření operace na chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů prostředku nebo úložišti. |
> | Akce | Microsoft.RecoveryServices/Vaults/delete | Operace odstranit úložiště odstraní zadaný prostředek Azure typu "úložiště. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá upozornění pro trezor služby Recovery services. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřeší upozornění. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Získá konfiguraci oznámení trezoru služeb zotavení. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Nakonfiguruje e-mailová oznámení pro trezor služby Recovery services. |
> | Akce | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Operaci odregistrovat kontejner může použít k odregistraci kontejneru. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operaci získat výsledky operace je možné získat stav operace a výsledku asynchronně odeslané operace. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery, operace se dá použít k získání kontejnerů zaregistrovaných u. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operaci zaregistrovat kontejner služby lze použít k registraci kontejneru u služeb zotavení. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Čtení nastavení výstrahy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Vytvořit nebo aktualizovat nastavení výstrahy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Zkontroluje konzistenci prostředí infrastruktury. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Odstranit všechny prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Nasadit Image procesového serveru |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrace do AAD prostředků infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Načíst všechny prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Znovu přidružit bránu |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Odebrat prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Obnovit certifikát pro prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Přečtěte si žádné logické sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Odstranit všechna mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Přečtěte si všechna mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Vytvořit nebo aktualizovat žádné mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Zjistit Chránitelnou položku |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Odstranit kontejner ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Načíst Chránitelné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Použít bod obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Odstranit všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Potvrzení převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Plánované převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Číst všechny body obnovení replikace |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Odebrat chráněnou položku |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Opravit replikaci |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Znovu zapnout ochranu položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Odeslat názor |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Čtení všech cílových výpočetních velikostí |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testovací převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Vyčištění testovacího převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizace služby Mobility |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Vytvořit nebo aktualizovat všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Odstranit všechny mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Číst všechny mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Odebrat mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Vytvořit nebo aktualizovat libovolný mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kontejner pro přepnutí ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Vytvořit nebo aktualizovat všechny kontejnery ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Odstranit všechny zprostředkovatele služeb zotavení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Číst všechny zprostředkovatele služeb zotavení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aktualizujte zprostředkovatele |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Odebrat zprostředkovatele Recovery Services |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Vytvořit nebo aktualizovat všechny zprostředkovatele služeb zotavení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Odstranit všechna mapování klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Číst všechny mapování klasifikací úložišť |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Vytvořit nebo aktualizovat libovolný mapování klasifikací úložišť |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Odstranit všechny vCenters |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny vCenters |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Vytvořit nebo aktualizovat libovolný vCenters |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Vytvořit nebo aktualizovat všechny prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Zrušit úlohu |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/read | Číst všechny úlohy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Restartovat úlohu |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Pokračovat v úloze |
> | Akce | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Přečtěte si všechna mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Přečtěte si žádné sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Odstraňte všechny zásady |
> | Akce | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
> | Akce | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Vytvořit nebo aktualizovat všechny zásady |
> | Akce | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Číst všechny mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Odstranit všechny plány obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánovaného převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Zpětná replikace plánu obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plán obnovení převzetí služeb při selhání testu |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plán obnovení testovacího převzetí služeb při selhání čištění |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Vytvořit nebo aktualizovat všechny plány obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Číst všechny zprostředkovatele služeb zotavení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Číst všechny mapování klasifikací úložišť |
> | Akce | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationUsages/read | Čtení všech trezorů replikace |
> | Akce | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Číst libovolný stav replikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Aktualizovat stav trezoru |
> | Akce | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Číst všechny vCenters |
> | Akce | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí informace o tokenu pro trezor služby Recovery Services. |
> | Akce | Microsoft.RecoveryServices/vaults/usages/read | Čtení všech trezorů |
> | Akce | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci Token trezoru můžete použít k získání tokenu trezoru pro trezor úrovně back-endové operace. |
> | Akce | Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Relay/checkNameAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. |
> | Akce | Microsoft.Relay/checkNamespaceAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. použijte místo něj Checknameavailability. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/action | Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je PUT. Použijte prosím volání PUT se místo toho aktualizovat autorizační pravidlo Namespace... Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/delete | Odstraňte autorizační pravidlo Namespace. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/write | Vytvořit Namespace úrovně autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla, primární a sekundární klíč. |
> | Akce | Microsoft.Relay/namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Získá klíče pravidel ověřování pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat pravidla ověřování primárního oboru názvů zotavení po havárii |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních. |
> | Akce | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tato operace dá vyvolat jen přes primární obor názvů. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operace, která aktualizuje HybridConnection. Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte prosím volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operace, která odstraní autorizační pravidla HybridConnection |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Získání připojovacího řetězce k HybridConnection |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Získá seznam autorizačních pravidel HybridConnection |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Vytvořit HybridConnection autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/Delete | Operace odstranění prostředku HybridConnection |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/read | Získá seznam popisů prostředků HybridConnection |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/write | Vytvoření nebo aktualizace HybridConnection vlastnosti. |
> | Akce | Microsoft.Relay/namespaces/messagingPlan/read | Získá plán pro zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti zveřejněné přes prostředek MessagingPlan přesunuly do (nadřazeného) prostředku Namespace v pozdějších verzích rozhraní API...<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.Relay/namespaces/messagingPlan/write | Aktualizuje se plán pro zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti zveřejněné přes prostředek MessagingPlan přesunuly do (nadřazeného) prostředku Namespace v pozdějších verzích rozhraní API...<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.Relay/namespaces/operationresults/read | Získá stav operace Namespace. |
> | Akce | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získat seznam metrik Namespace popisů prostředků |
> | Akce | Microsoft.Relay/namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Odebrat obor názvů služby ACS |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operace, která aktualizuje WcfRelay. Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte prosím volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operace, která odstraní autorizační pravidla WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Získání připojovacího řetězce k WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Získá seznam autorizačních pravidel WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Vytvořit WcfRelay autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/Delete | Operace odstranění prostředku WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/read | Získá seznam popisů prostředků WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/write | Vytvoření nebo aktualizace WcfRelay vlastnosti. |
> | Akce | Microsoft.Relay/namespaces/write | Vytvoření prostředku Namespace a aktualizovat jeho vlastnosti. Značky a kapacita Namespace jsou vlastnosti, které je možné aktualizovat. |
> | Akce | Microsoft.Relay/operations/read | Získat operace |
> | Akce | Microsoft.Relay/register/action | Zaregistruje předplatné u poskytovatele prostředků Relay a povolí vytváření prostředků Relay. |
> | Akce | Microsoft.Relay/unregister/action | Zaregistruje předplatné u poskytovatele prostředků Relay a povolí vytváření prostředků Relay. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Získá stav dostupnosti pro zadaný prostředek. |
> | Akce | Microsoft.ResourceHealth/AvailabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Akce | Microsoft.ResourceHealth/events/read | Načíst události služby Service Health pro dané předplatné |
> | Akce | Microsoft.Resourcehealth/healthevent/action | Označuje změnu ve stavu zadaného prostředku. |
> | Akce | Microsoft.Resourcehealth/healthevent/Activated/action | Označuje změnu ve stavu zadaného prostředku. |
> | Akce | Microsoft.Resourcehealth/healthevent/InProgress/action | Označuje změnu ve stavu zadaného prostředku. |
> | Akce | Microsoft.Resourcehealth/healthevent/Pending/action | Označuje změnu ve stavu zadaného prostředku. |
> | Akce | Microsoft.Resourcehealth/healthevent/Resolved/action | Označuje změnu ve stavu zadaného prostředku. |
> | Akce | Microsoft.Resourcehealth/healthevent/Updated/action | Označuje změnu ve stavu zadaného prostředku. |
> | Akce | Microsoft.ResourceHealth/impactedResources/read | Načíst ovlivněné prostředky pro dané předplatné |
> | Akce | Microsoft.ResourceHealth/Operations/read | Získá operace, které jsou k dispozici pro Microsoft Resource Health. |
> | Akce | Microsoft.ResourceHealth/register/action | Zaregistruje předplatné do služby Microsoft Resource Health. |
> | Akce | Microsoft.ResourceHealth/unregister/action | Zruší registrace předplatného pro Microsoft Resource Health. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Resources/checkPolicyCompliance/action | Kontrola stavu dodržování zásad daného prostředku |
> | Akce | Microsoft.Resources/checkResourceName/action | Zkontroluje správnost názvu prostředku. |
> | Akce | Microsoft.Resources/deployments/cancel/action | Zruší nasazení. |
> | Akce | Microsoft.Resources/deployments/delete | Odstraní nasazení. |
> | Akce | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Akce | Microsoft.Resources/deployments/read | Načte nebo vypíše nasazení. |
> | Akce | Microsoft.Resources/deployments/validate/action | Ověří nasazení. |
> | Akce | Microsoft.Resources/deployments/write | Vytvoří nebo aktualizuje nasazení. |
> | Akce | Microsoft.Resources/links/delete | Odstraní odkaz na prostředek. |
> | Akce | Microsoft.Resources/links/read | Načte nebo vypíše odkazy na prostředek. |
> | Akce | Microsoft.Resources/links/write | Vytvoří nebo aktualizuje odkaz na prostředek. |
> | Akce | Microsoft.Resources/marketplace/purchase/action | Umožňuje koupit prostředek z Marketplace. |
> | Akce | Microsoft.Resources/providers/read | Načte seznam zprostředkovatelů. |
> | Akce | Microsoft.Resources/resources/read | Načte seznam prostředků na základě filtrů. |
> | Akce | Microsoft.Resources/subscriptions/locations/read | Získá výpis podporovaných umístění. |
> | Akce | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Akce | Microsoft.Resources/subscriptions/providers/read | Načte nebo vypíše zprostředkovatele prostředků. |
> | Akce | Microsoft.Resources/subscriptions/read | Získá výpis předplatných. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/delete | Odstraní skupinu prostředků a všechny její prostředky. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Získá nebo vypíše stavy operace nasazení. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Načte nebo vypíše nasazení. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Vytvoří nebo aktualizuje nasazení. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Přesune prostředky z jedné skupiny prostředků do jiné. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Akce | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Načte prostředky pro skupinu prostředků. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Ověří přesun prostředků z jedné skupiny prostředků do jiné. |
> | Akce | Microsoft.Resources/subscriptions/resourceGroups/write | Vytvoří nebo aktualizuje skupinu prostředků. |
> | Akce | Microsoft.Resources/subscriptions/resources/read | Získá prostředky předplatného. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/delete | Odstraní značku předplatného. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/read | Načte nebo vypíše značky předplatného. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Odstraní hodnotu značky předplatného. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Načte nebo vypíše hodnoty značky předplatného. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Přidá hodnotu značky předplatného. |
> | Akce | Microsoft.Resources/subscriptions/tagNames/write | Přidá značku předplatného. |
> | Akce | Microsoft.Resources/tenants/read | Získá výpis klientů. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Scheduler/jobcollections/delete | Odstraní kolekci úloh. |
> | Akce | Microsoft.Scheduler/jobcollections/disable/action | Vypne kolekci úloh. |
> | Akce | Microsoft.Scheduler/jobcollections/enable/action | Zapne kolekci úloh. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/delete | Odstraní úlohu. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Vygeneruje definici Aplikace logiky na základě úlohy Scheduleru. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Načte historii úlohy. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/read | Načte úlohu. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/run/action | Spustí úlohu. |
> | Akce | Microsoft.Scheduler/jobcollections/jobs/write | Vytvoří nebo aktualizuje úlohu. |
> | Akce | Microsoft.Scheduler/jobcollections/read | Získat kolekci úloh |
> | Akce | Microsoft.Scheduler/jobcollections/write | Vytvoří nebo aktualizuje kolekci úloh. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Search/checkNameAvailability/action | Zkontroluje dostupnost názvu služby. |
> | Akce | Microsoft.Search/operations/read | Obsahuje seznam všech dostupných operací Microsoft.Search zprostředkovatele. |
> | Akce | Microsoft.Search/register/action | Zaregistruje předplatné u poskytovatele prostředků hledání a umožňuje vytvoření vyhledávací služby. |
> | Akce | Microsoft.Search/searchServices/createQueryKey/action | Vytvoří klíč dotazu. |
> | Akce | Microsoft.Search/searchServices/delete | Odstraní vyhledávací službu. |
> | Akce | Microsoft.Search/searchServices/deleteQueryKey/delete | Odstraní klíč dotazu. |
> | Akce | Microsoft.Search/searchServices/listAdminKeys/action | Načte klíče správce. |
> | Akce | Microsoft.Search/searchServices/listQueryKeys/read | Vrátí seznam klíčů rozhraní API dotazu pro danou službu Azure Search. |
> | Akce | Microsoft.Search/searchServices/read | Načte službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/regenerateAdminKey/action | Znovu vygeneruje klíč správce. |
> | Akce | Microsoft.Search/searchServices/start/action | Spustí službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/stop/action | Zastaví službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/write | Vytvoří nebo aktualizuje vyhledávací službu. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Security/advancedThreatProtectionSettings/read | Získá nastavení rozšířené ochrany před internetovými útoky pro prostředek |
> | Akce | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualizuje Upřesnit nastavení ochrany před internetovými útoky pro prostředek |
> | Akce | Microsoft.Security/alerts/read | Získá všechny výstrahy zabezpečení |
> | Akce | Microsoft.Security/applicationWhitelistings/read | Získá whitelistings aplikace |
> | Akce | Microsoft.Security/applicationWhitelistings/write | Vytvoří nového seznamu povolených aplikací nebo aktualizuje nějakou existující |
> | Akce | Microsoft.Security/complianceResults/read | Získá výsledky dodržování shody pro prostředek |
> | Akce | Microsoft.Security/informationProtectionPolicies/read | Získá zásady ochrany informací pro prostředek |
> | Akce | Microsoft.Security/informationProtectionPolicies/write | Aktualizace zásad ochrany informací pro prostředek |
> | Akce | Microsoft.Security/locations/alerts/activate/action | Aktivovat upozornění zabezpečení |
> | Akce | Microsoft.Security/locations/alerts/dismiss/action | Zavřít upozornění zabezpečení |
> | Akce | Microsoft.Security/locations/alerts/read | Získá všechny výstrahy zabezpečení |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Odstraní zásady přístupu k síti just-in-time |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Zahájí požadavek zásady přístupu k síti just-in-time |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Získá zásad přístupu k síti just-in-time |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Vytvoří nové zásady přístupu k síti just-in-time nebo aktualizuje nějakou existující |
> | Akce | Microsoft.Security/locations/read | Získá umístění dat zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/activate/action | Aktivovat doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/dismiss/action | Zavřít doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/read | Získá všechna dostupná doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/resolve/action | Řešení doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/start/action | Spustit na doporučení zabezpečení |
> | Akce | Microsoft.Security/policies/read | Získá zásady zabezpečení |
> | Akce | Microsoft.Security/policies/write | Aktualizuje zásady zabezpečení |
> | Akce | Microsoft.Security/pricings/delete | Odstraní nastavení cen pro obor |
> | Akce | Microsoft.Security/pricings/read | Získá nastavení cen pro obor |
> | Akce | Microsoft.Security/pricings/write | Aktualizace nastavení cen pro obor |
> | Akce | Microsoft.Security/register/action | Zaregistruje předplatné pro Azure Security Center |
> | Akce | Microsoft.Security/securityContacts/delete | Odstraní kontaktů zabezpečení |
> | Akce | Microsoft.Security/securityContacts/read | Získá adresu kontaktu zabezpečení |
> | Akce | Microsoft.Security/securityContacts/write | Aktualizace kontaktů zabezpečení |
> | Akce | Microsoft.Security/securitySolutions/delete | Odstraní řešení zabezpečení |
> | Akce | Microsoft.Security/securitySolutions/read | Získá řešení zabezpečení |
> | Akce | Microsoft.Security/securitySolutions/write | Vytvoří nové řešení zabezpečení nebo aktualizuje nějakou existující |
> | Akce | Microsoft.Security/securitySolutionsReferenceData/read | Získá odkaz na data řešení zabezpečení |
> | Akce | Microsoft.Security/securityStatuses/read | Získá stavy stavu zabezpečení prostředků Azure |
> | Akce | Microsoft.Security/securityStatusesSummaries/read | Získá souhrny stavů zabezpečení pro obor |
> | Akce | Microsoft.Security/settings/read | Získá nastavení oboru |
> | Akce | Microsoft.Security/settings/write | Aktualizace nastavení oboru |
> | Akce | Microsoft.Security/tasks/read | Získá všechna dostupná doporučení zabezpečení |
> | Akce | Microsoft.Security/unregister/action | Zruší registraci předplatného ze služby Azure Security Center |
> | Akce | Microsoft.Security/webApplicationFirewalls/delete | Odstraní firewallu webových aplikací |
> | Akce | Microsoft.Security/webApplicationFirewalls/read | Získá webu brány firewall aplikací |
> | Akce | Microsoft.Security/webApplicationFirewalls/write | Vytvoří nový firewall webových aplikací nebo aktualizuje nějakou existující |
> | Akce | Microsoft.Security/workspaceSettings/connect/action | Změnit nastavení obnovení nastavení pracovního prostoru |
> | Akce | Microsoft.Security/workspaceSettings/delete | Odstraní nastavení pracovního prostoru |
> | Akce | Microsoft.Security/workspaceSettings/read | Získá nastavení pracovního prostoru |
> | Akce | Microsoft.Security/workspaceSettings/write | Aktualizace nastavení pracovního prostoru |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/delete | Odstranění nastavení diagnostiky |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/read | Čtení nastavení diagnostiky |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/write | Zápis nastavení diagnostiky |
> | Akce | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Čtení kategorie nastavení diagnostiky |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ServiceBus/checkNameAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. |
> | Akce | Microsoft.ServiceBus/checkNamespaceAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. použijte místo něj Checknameavailability. |
> | Akce | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla zadané virtuální sítě v poskytovateli prostředků ServiceBus. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/action | Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je PUT. Použijte prosím volání PUT se místo toho aktualizovat autorizační pravidlo Namespace... Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Odstraňte autorizační pravidlo Namespace. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/write | Vytvořit Namespace úrovně autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla, primární a sekundární klíč. |
> | Akce | Microsoft.ServiceBus/namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Získá klíče pravidel ověřování pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat pravidla ověřování primárního oboru názvů zotavení po havárii |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Odstraní konfiguraci zotavení po havárii přidruženou k oboru názvů. Tato operace dá vyvolat jen přes primární obor názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Odstraní filtr Event Gridu přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Získá filtr Event Gridu přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event Gridu přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventhubs/read | Získá seznam popisů prostředků centra událostí |
> | Akce | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Odstranit prostředek filtru IP |
> | Akce | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Získat prostředek filtru IP |
> | Akce | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Vytvořit prostředek filtru IP |
> | Akce | Microsoft.ServiceBus/namespaces/messagingPlan/read | Získá plán pro zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti zveřejněné přes prostředek MessagingPlan přesunuly do (nadřazeného) prostředku Namespace v pozdějších verzích rozhraní API...<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.ServiceBus/namespaces/messagingPlan/write | Aktualizuje se plán pro zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralé.<br>Vlastnosti zveřejněné přes prostředek MessagingPlan přesunuly do (nadřazeného) prostředku Namespace v pozdějších verzích rozhraní API...<br>Tato operace není podporována v rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.ServiceBus/namespaces/migrate/action | Operace migrace oboru názvů |
> | Akce | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Odstraní konfiguraci migrace. |
> | Akce | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Získá konfiguraci migrace, která označuje stav migrace a čekající operace replikace. |
> | Akce | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Obrátí migraci oboru názvů Standard na Premium. |
> | Akce | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Přiřadí službu DNS přidruženou k oboru názvů Standard do oboru názvů Premium, čímž se dokončí migrace a zastaví se synchronizace prostředků z oboru názvů Standard do oboru názvů Premium. |
> | Akce | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Vytvoří nebo konfiguraci migrace aktualizace. Tato akce spustí synchronizaci prostředky z standardu pro obor názvů úrovně premium |
> | Akce | Microsoft.ServiceBus/namespaces/operationresults/read | Získá stav operace Namespace. |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Získá seznam popisů prostředků Namespace nastavení diagnostiky |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Získá seznam popisů prostředků Namespace nastavení diagnostiky |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Získání seznamu protokolů Namespace popisů prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získat seznam metrik Namespace popisů prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operace pro aktualizaci fronty. Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte prosím volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operace, která odstraní autorizační pravidla fronty |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Získání připojovacího řetězce do fronty |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Získání seznamu fronty autorizační pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Vytvoření fronty autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/Delete | Operace odstranění fronty prostředku |
> | Akce | Microsoft.ServiceBus/namespaces/queues/read | Získá seznam popisů prostředků fronty |
> | Akce | Microsoft.ServiceBus/namespaces/queues/write | Vytvoření nebo aktualizaci fronty vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Odebrat obor názvů služby ACS |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operace aktualizace tématu. Tato operace není podporována v rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte prosím volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operace, která odstraní autorizační pravidla tématu |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Získání připojovacího řetězce do tématu |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Získat seznam tématu autorizační pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Vytvoření tématu autorizační pravidla a aktualizovat jeho vlastnosti. Je možné aktualizovat přístupová práva autorizační pravidla. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/Delete | Operace odstranění prostředku tématu |
> | Akce | Microsoft.ServiceBus/namespaces/topics/read | Získá seznam popisů prostředků tématu |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operace odstranění prostředku TopicSubscription |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Získá seznam popisů prostředků TopicSubscription |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operace, která odstraní prostředek pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Získá seznam popisů prostředků pravidlo |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Vytvoření nebo aktualizace pravidla vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Vytvoření nebo aktualizace TopicSubscription vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/write | Vytvoření nebo aktualizace tématu vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Odstranit prostředek pravidla virtuální sítě |
> | Akce | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Získat prostředek pravidla virtuální sítě |
> | Akce | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Vytvořit prostředek pravidla virtuální sítě |
> | Akce | Microsoft.ServiceBus/namespaces/write | Vytvoření prostředku Namespace a aktualizovat jeho vlastnosti. Značky a kapacita Namespace jsou vlastnosti, které je možné aktualizovat. |
> | Akce | Microsoft.ServiceBus/operations/read | Získat operace |
> | Akce | Microsoft.ServiceBus/register/action | Zaregistruje předplatné u poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |
> | Akce | Microsoft.ServiceBus/sku/read | Získá seznam popisů prostředků Sku |
> | Akce | Microsoft.ServiceBus/sku/regions/read | Získá seznam popisů prostředků SkuRegions |
> | Akce | Microsoft.ServiceBus/unregister/action | Zaregistruje předplatné u poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ServiceFabric/clusters/applications/delete | Umožňuje odstranit libovolnou aplikaci. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/read | Umožňuje číst libovolnou aplikaci |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/delete | Umožňuje odstranit libovolnou službu. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Umožňuje číst libovolný oddíl. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Umožňuje číst libovolnou repliku. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/read | Umožňuje číst libovolnou službu. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Umožňuje načíst stav libovolné služby. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/services/write | Umožňuje vytvořit nebo aktualizovat libovolnou službu. |
> | Akce | Microsoft.ServiceFabric/clusters/applications/write | Umožňuje vytvořit nebo aktualizovat libovolnou aplikaci. |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Umožňuje odstranit libovolný typ aplikace. |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/read | Umožňuje přečíst libovolný typ aplikace. |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Umožňuje odstranit libovolnou verzi typu aplikace. |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Umožňuje přečíst libovolnou verzi typu aplikace. |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Umožňuje vytvořit nebo aktualizovat libovolnou verzi typu aplikace. |
> | Akce | Microsoft.ServiceFabric/clusters/applicationTypes/write | Umožňuje vytvořit nebo aktualizovat libovolný typ aplikace. |
> | Akce | Microsoft.ServiceFabric/clusters/delete | Umožňuje odstranit libovolný cluster. |
> | Akce | Microsoft.ServiceFabric/clusters/nodes/read | Umožňuje číst libovolný uzel. |
> | Akce | Microsoft.ServiceFabric/clusters/read | Umožňuje číst libovolný cluster. |
> | Akce | Microsoft.ServiceFabric/clusters/statuses/read | Umožňuje načíst stav libovolného clusteru. |
> | Akce | Microsoft.ServiceFabric/clusters/write | Umožňuje vytvořit nebo aktualizovat libovolný cluster. |
> | Akce | Microsoft.ServiceFabric/locations/clusterVersions/read | Načíst libovolnou verzi clusteru |
> | Akce | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Umožňuje přečíst libovolnou verzi clusteru pro konkrétní prostředí. |
> | Akce | Microsoft.ServiceFabric/locations/operationresults/read | Umožňuje přečíst libovolné výsledky operace. |
> | Akce | Microsoft.ServiceFabric/locations/operations/read | Umožňuje přečíst libovolnou operaci podle umístění. |
> | Akce | Microsoft.ServiceFabric/operations/read | Umožňuje přečíst libovolnou dostupnou operaci. |
> | Akce | Microsoft.ServiceFabric/register/action | Umožňuje zaregistrovat libovolnou akci. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.SignalRService/checknameavailability/action | Kontroluje, jestli je název k dispozici pro použití s využitím nové služby SignalR |
> | Akce | Microsoft.SignalRService/register/action | Zaregistrovat poskytovatele prostředků "Microsoft.SignalRService" s předplatným |
> | Akce | Microsoft.SignalRService/SignalR/delete | Odstranit celý SignalR |
> | Akce | Microsoft.SignalRService/SignalR/read | Zobrazit nastavení a konfigurace služby SignalR na portálu management portal nebo prostřednictvím rozhraní API |
> | Akce | Microsoft.SignalRService/SignalR/write | Upravit nastavení a konfigurace na portálu management portal nebo prostřednictvím rozhraní API služby SignalR |
> | Akce | Microsoft.SignalRService/unregister/action | Zruší registraci poskytovatele prostředků "Microsoft.SignalRService" s předplatným |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Solutions/applicationDefinitions/delete | Odebere definici aplikace. |
> | Akce | Microsoft.Solutions/applicationDefinitions/read | Načte seznam definic aplikací. |
> | Akce | Microsoft.Solutions/applicationDefinitions/write | Přidá nebo upraví definici aplikace. |
> | Akce | Microsoft.Solutions/applications/delete | Odebere aplikaci. |
> | Akce | Microsoft.Solutions/applications/read | Načte seznam aplikací. |
> | Akce | Microsoft.Solutions/applications/write | Vytvoří aplikaci. |
> | Akce | Microsoft.Solutions/jitRequests/delete | Odebere požadavek JitRequest. |
> | Akce | Microsoft.Solutions/jitRequests/read | Načte seznam požadavků JitRequest. |
> | Akce | Microsoft.Solutions/jitRequests/write | Vytvoří požadavek JitRequest. |
> | Akce | Microsoft.Solutions/locations/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.Solutions/register/action | Provede registraci do Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Sql/checkNameAvailability/action | Ověřte, zda server je uvedený název dostupné ke zřizování po celém světě pro daný odběr. |
> | Akce | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Načíst výsledek objektu blob rozšířený server operace nastavení zásad auditování |
> | Akce | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Načíst výsledek objektu blob server operace nastavení zásad auditování |
> | Akce | Microsoft.Sql/locations/capabilities/read | Získá možnosti pro toto předplatné v daném umístění |
> | Akce | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/locations/databaseOperationResults/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Získá probíhajících operací na server odstranil |
> | Akce | Microsoft.Sql/locations/deletedServerOperationResults/read | Získá probíhajících operací na server odstranil |
> | Akce | Microsoft.Sql/locations/deletedServers/read | Vrátí seznam odstraněných servery nebo získá vlastnosti pro zadaný server odstranil. |
> | Akce | Microsoft.Sql/locations/deletedServers/recover/action | Obnovení odstraněné serveru |
> | Akce | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Získá azure asynchronní operace pro asynchronní operace elastického fondu |
> | Akce | Microsoft.Sql/locations/elasticPoolOperationResults/read | Získá výsledek operace elastického fondu. |
> | Akce | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Načíst výsledek objektu blob rozšířený server operace nastavení zásad auditování |
> | Akce | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Načíst výsledek objektu blob rozšířený server operace nastavení zásad auditování |
> | Akce | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Získá stav operace pravidel brány firewall. |
> | Akce | Microsoft.Sql/locations/firewallRulesOperationResults/read | Získá stav operace pravidel brány firewall. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/delete | Odstraní existující instance skupiny převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Provede plánované převzetí služeb při selhání ve skupině převzetí služeb při selhání pro existující instance. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Provede Vynucené převzetí služeb při selhání ve skupině převzetí služeb při selhání pro existující instance. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/read | Vrátí seznam instancí převzetí služeb při selhání skupiny nebo načte vlastnosti pro skupinu převzetí služeb při selhání zadanou instanci. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/write | Vytvoří instanci skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti a značky pro skupinu převzetí služeb při selhání určené instance. |
> | Akce | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Vrátí podrobnosti o konkrétní rozhraní koncového bodu Azure asynchronní operace |
> | Akce | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Vrátí podrobnosti o operaci zadané rozhraní koncového bodu profilu |
> | Akce | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Získá stav operace agenta úlohy. |
> | Akce | Microsoft.Sql/locations/jobAgentOperationResults/read | Získá výsledek operace úlohy agenta. |
> | Akce | Microsoft.Sql/locations/longTermRetentionBackups/read | Seznamy dlouhodobého uchovávání záloh pro každou databázi na každém serveru v umístění |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Seznamy dlouhodobého uchovávání záloh pro každou databázi na serveru |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Odstraní dlouhodobého uchování zálohy |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Seznamy dlouhodobého uchovávání záloh databáze |
> | Akce | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Dokončení operace obnovení spravované databáze |
> | Akce | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Získá probíhajících operací na spravovanou databázovou transparentní šifrování dat |
> | Akce | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Získá probíhajících operací na spravovanou databázovou transparentní šifrování dat |
> | Akce | Microsoft.Sql/locations/read | Získá umístění k dispozici pro daný odběr |
> | Akce | Microsoft.Sql/locations/syncAgentOperationResults/read | Načíst výsledek operace prostředků agenta synchronizace |
> | Akce | Microsoft.Sql/locations/syncDatabaseIds/read | Načtení ID databáze synchronizace pro konkrétní oblast a předplatné |
> | Akce | Microsoft.Sql/locations/syncGroupOperationResults/read | Načíst výsledek operace synchronizace skupiny prostředků |
> | Akce | Microsoft.Sql/locations/syncMemberOperationResults/read | Načíst výsledek operace prostředků člen synchronizace |
> | Akce | Microsoft.Sql/locations/usages/read | Získá kolekci metriky využití pro toto předplatné v umístění |
> | Akce | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Vrátí podrobnosti o pravidlech zadané virtuální sítě azure asynchronní operace  |
> | Akce | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Vrátí podrobnosti o operaci pravidla zadané virtuální sítě  |
> | Akce | Microsoft.Sql/managedInstances/administrators/delete | Odstraní stávající správce spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/administrators/read | Získá seznam správců spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/administrators/write | Vytvoří nebo aktualizuje správce spravované instance se zadanými parametry. |
> | Akce | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Získá zásady uchovávání informací krátkodobém horizontu pro spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualizuje krátkodobém horizontu zásady uchovávání informací pro spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Označení citlivosti seznamu na danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/delete | Odstraní existující spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro spravovanou instanci databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/read | Získá existující spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Označení citlivosti seznamu na danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Odstranit označení citlivosti daného sloupce |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Získat označení citlivosti daného sloupce |
> | Akce | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Vytvořit nebo aktualizovat označení citlivosti daného sloupce |
> | Akce | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Načíst podrobnosti o zásadu detekce hrozeb databáze nakonfigurované na dané spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Změnit databázi zásadu detekce hrozeb u daného spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/securityEvents/read | Načte události zabezpečení spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Označení citlivosti seznamu na danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Načíst podrobnosti o databázi transparentního šifrování dat u dané spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Změnit databázi transparentního šifrování dat pro danou spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Odstranit posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Načíst zásady posouzení ohrožení zabezpečení na givendatabase |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Odstranit posouzení ohrožení zabezpečení pravidlo směrný plán pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Získejte základní pravidlo posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Změňte základní pravidlo posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Převeďte existující výsledku skenování lidské čitelném formátu. Pokud už existuje, nic se stane |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Spusťte posouzení zabezpečení databáze. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Vrátí seznam ohrožení zabezpečení databáze záznamy skenování hodnocení nebo získat kontrolu záznam pro ID zadaného kontroly. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Změnit posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/write | Vytvoří novou databázi nebo aktualizuje existující databázi. |
> | Akce | Microsoft.Sql/managedInstances/delete | Odstraní existující spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/encryptionProtector/read | Vrátí seznam hodnot ochrany pomocí šifrování serveru nebo načte vlastnosti pro zadaný server ochrana šifrování. |
> | Akce | Microsoft.Sql/managedInstances/encryptionProtector/write | Aktualizací vlastností pro zadaný šifrování Ochrana serveru. |
> | Akce | Microsoft.Sql/managedInstances/keys/delete | Odstraní existující klíč spravované Instance Azure SQL. |
> | Akce | Microsoft.Sql/managedInstances/keys/read | Vrátí seznam spravovanou instanci klíče nebo načte vlastnosti klíče určené spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/keys/write | Vytvoří klíč se zadanými parametry nebo aktualizovat vlastnosti a značky pro klíč zadaný spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/metricDefinitions/read | Získat definice metriky pro spravovanou instanci |
> | Akce | Microsoft.Sql/managedInstances/metrics/read | Získat metriky spravované instance |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro spravované instance |
> | Akce | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro spravované instance |
> | Akce | Microsoft.Sql/managedInstances/read | Vrátí seznam spravované instance nebo získá vlastnosti pro zadané spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Získá zásady uchovávání informací krátkodobém horizontu vynechaných spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualizuje zásady uchovávání informací krátkodobém horizontu vynechaných spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Vrátí že seznam obnovitelné vyřadit spravované databáze. |
> | Akce | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Načíst podrobnosti o zásadu detekce hrozeb spravovaný server nakonfigurovaný na daném spravovaného serveru |
> | Akce | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Změnit zásadu detekce hrozeb spravované serverem pro danou spravovaný server |
> | Akce | Microsoft.Sql/managedInstances/tdeCertificates/action | Transparentní šifrování dat vytvoří nebo aktualizuje certifikát |
> | Akce | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Odstranit posouzení ohrožení zabezpečení pro dané spravované instance |
> | Akce | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Načíst zásady posouzení ohrožení zabezpečení na dané spravované instance |
> | Akce | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Změnit posouzení ohrožení zabezpečení pro dané spravované instance |
> | Akce | Microsoft.Sql/managedInstances/write | Vytvoří spravovanou instanci se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadané spravované instance. |
> | Akce | Microsoft.Sql/operations/read | Získá dostupné operace REST |
> | Akce | Microsoft.Sql/register/action | Zaregistruje předplatné u poskytovatele prostředků Microsoft SQL Database a povolí vytváření databází Microsoft SQL. |
> | Akce | Microsoft.Sql/servers/administratorOperationResults/read | Získá probíhajících operací ve Správci serveru |
> | Akce | Microsoft.Sql/servers/administrators/delete | Odstranit správce serveru |
> | Akce | Microsoft.Sql/servers/administrators/read | Načíst podrobnosti správce serveru |
> | Akce | Microsoft.Sql/servers/administrators/write | Vytvořit nebo aktualizovat Správce serveru |
> | Akce | Microsoft.Sql/servers/advisors/read | Vrátí seznam poradci pro server k dispozici |
> | Akce | Microsoft.Sql/servers/advisors/recommendedActions/read | Vrátí seznam doporučených akcích zadané služby advisor pro server |
> | Akce | Microsoft.Sql/servers/advisors/recommendedActions/write | Použije doporučenou akci na serveru |
> | Akce | Microsoft.Sql/servers/advisors/write | Aktualizace automaticky spustit stav advisor na úrovni serveru. |
> | Akce | Microsoft.Sql/servers/auditingPolicies/read | Načíst podrobnosti o výchozí server tabulky auditování zásady nakonfigurované na daném serveru |
> | Akce | Microsoft.Sql/servers/auditingPolicies/write | Změnit výchozí server auditování tabulek pro daný server |
> | Akce | Microsoft.Sql/servers/auditingSettings/operationResults/read | Načíst výsledek objektu blob server operace nastavení zásad auditování |
> | Akce | Microsoft.Sql/servers/auditingSettings/read | Načíst podrobnosti o zásady auditování objektů blob serveru nakonfigurovaný na daném serveru |
> | Akce | Microsoft.Sql/servers/auditingSettings/write | Změnit auditování objektů blob serveru pro daný server |
> | Akce | Microsoft.Sql/servers/automaticTuning/read | Vrátí nastavení automatického ladění serveru |
> | Akce | Microsoft.Sql/servers/automaticTuning/write | Aktualizuje nastavení automatického ladění serveru a vrátí aktualizované nastavení |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Odstraní existující trezor záloh archivace. |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Tato operace slouží k získání trezoru zálohování dlouhodobé uchovávání. Vrátí informace o trezoru zaregistrovaný k tomuto serveru |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Tato operace slouží k registraci zálohování dlouhodobým uchováváním informací trezor k serveru |
> | Akce | Microsoft.Sql/servers/communicationLinks/delete | Odstraní existující vazbu komunikace serveru. |
> | Akce | Microsoft.Sql/servers/communicationLinks/read | Vrátí seznam komunikační propojení zadaný server. |
> | Akce | Microsoft.Sql/servers/communicationLinks/write | Vytvořit nebo aktualizovat spoj serveru. |
> | Akce | Microsoft.Sql/servers/connectionPolicies/read | Vrátí seznam zásad připojení serveru ze zadaného serveru. |
> | Akce | Microsoft.Sql/servers/connectionPolicies/write | Vytvořit nebo aktualizovat zásady připojení serveru. |
> | Akce | Microsoft.Sql/servers/databases/advisors/read | Vrátí seznam dostupných pro databázi Poradce |
> | Akce | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Vrátí seznam doporučené akce zadané pro databázi Poradce |
> | Akce | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Použije doporučenou akci na databázi |
> | Akce | Microsoft.Sql/servers/databases/advisors/write | Aktualizace automaticky spustit stav advisor na úrovni databáze. |
> | Akce | Microsoft.Sql/servers/databases/auditingPolicies/read | Načtení podrobností tabulky auditování zásady, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingPolicies/write | Změňte zásady auditu tabulky pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingSettings/read | Načíst podrobnosti o zásady auditování objektů blob, nakonfigurovaného na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingSettings/write | Změnit zásady auditování objektů blob pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu databáze objektů blob |
> | Akce | Microsoft.Sql/servers/databases/automaticTuning/read | Vrátí nastavení automatického ladění pro databázi |
> | Akce | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizuje nastavení automatického ladění pro databázi a vrátí aktualizované nastavení |
> | Akce | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Vrátí seznam zásad zálohování archivace zadané databáze. |
> | Akce | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Vytvořit nebo aktualizovat zásadu zálohování archivace databáze. |
> | Akce | Microsoft.Sql/servers/databases/connectionPolicies/read | Načíst podrobnosti o připojení zásady nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/connectionPolicies/write | Změnit zásady připojení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Označení citlivosti seznamu na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Vrátí seznam zásad maskování dat z databáze. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Odstranit pravidlo zásad pro danou databázi maskování dat |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Načíst podrobnosti o zásady nakonfigurované na danou databázi pravidlo maskování dat |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Změna pravidla zásad pro danou databázi maskování dat |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Změna zásad pro danou databázi maskování dat |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Vrátí informace o kroku distribuovaného dotazu dotazu datový sklad pro vybraný krok ID |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Vrátí dotaz datového skladu distribuci informací pro ID vybraného dotazu |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Načte aktivit uživatelů, které instance SQL Data Warehouse, která obsahuje dotazy běží a je pozastavené |
> | Akce | Microsoft.Sql/servers/databases/delete | Odstraní existující databázi. |
> | Akce | Microsoft.Sql/servers/databases/export/action | Export databáze Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Načíst podrobnosti o rozšířený objekt blob auditu zásady, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Změňte zásady auditu rozšířený objekt blob pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/extensions/read | Získá kolekci rozšíření pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/extensions/write | Změňte příponu pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Načíst zásady geografického zálohování pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Vytvořit nebo aktualizovat zásady geobackup databáze |
> | Akce | Microsoft.Sql/servers/databases/importExportOperationResults/read | Získá probíhá operace importu/exportu |
> | Akce | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Získá seznam časová období údržby dostupná pro vybrané databáze. |
> | Akce | Microsoft.Sql/servers/databases/maintenanceWindows/read | Získá údržby nastavení systému windows pro vybrané databáze. |
> | Akce | Microsoft.Sql/servers/databases/maintenanceWindows/write | Nastaví údržby nastavení systému windows pro vybrané databáze. |
> | Akce | Microsoft.Sql/servers/databases/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.Sql/servers/databases/metrics/read | Vrátit metriky pro databáze |
> | Akce | Microsoft.Sql/servers/databases/move/action | Přejmenovat Azure SQL Database |
> | Akce | Microsoft.Sql/servers/databases/operationResults/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/servers/databases/operations/cancel/action | Azure SQL Database Zruší probíhající asynchronní operaci, která ještě není dokončená. |
> | Akce | Microsoft.Sql/servers/databases/operations/read | Vrátí seznam operací provedených v databázi |
> | Akce | Microsoft.Sql/servers/databases/pause/action | Pozastavení databáze datového skladu Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro databáze |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Vrátí kolekci texty dotazu, které odpovídají zadaným parametrům. |
> | Akce | Microsoft.Sql/servers/databases/queryStore/read | Vrátí aktuální hodnoty pro nastavení Store dotazů pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/queryStore/write | Nastavení aktualizace Query Store pro databázi |
> | Akce | Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti pro určenou databázi. |
> | Akce | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Označení citlivosti seznamu na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/delete | Ukončení relace replikace vynuceně a s možnou ztrátou dat |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Převzetí služeb při selhání po synchronizaci všech změní z primárního serveru, provádění této databáze do replikace relationship\u0027s primární a provádění vzdáleného primární do sekundární |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Převzetí služeb při selhání ihned s možnou ztrátou dat, provádění této databáze do replikace relationship\u0027s primární a provádění vzdáleného primární do sekundární |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/read | Vrátí seznam replikace odkazy nebo načte vlastnosti zadaného replikačních připojení. |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Ukončení relace replikace vynuceně nebo po synchronizaci s partnerem |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aktualizovat režim replikace pro odkaz na synchronní nebo asynchronní režimu |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/action | Vytvoří nový bod obnovení |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/delete | Odstraní bod obnovení pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/read | Vrátí body databáze obnovení. |
> | Akce | Microsoft.Sql/servers/databases/resume/action | Obnovuje se chod databáze datového skladu Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/schemas/read | Načíst seznam schémata databáze |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Načíst seznam sloupců tabulky. |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Odstranit označení citlivosti daného sloupce |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Získat označení citlivosti daného sloupce |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Vytvořit nebo aktualizovat označení citlivosti daného sloupce |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/read | Načíst seznam tabulek databáze |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Načíst seznam doporučení indexu na databázi |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Použití doporučení indexu |
> | Akce | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Načíst podrobnosti o zásadu detekce hrozeb databáze nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Změnit databázi zásadu detekce hrozeb pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/securityMetrics/read | Získá kolekci metriky zabezpečení databáze |
> | Akce | Microsoft.Sql/servers/databases/sensitivityLabels/read | Označení citlivosti seznamu na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Vrátí návrh o škálování databáze navýšení nebo snížení kapacity na základě statistik provádění dotazů pro zlepšení výkonu nebo snížení nákladů |
> | Akce | Microsoft.Sql/servers/databases/skus/read | Získá kolekci skladové položky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Zrušit synchronizaci skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/delete | Odstraní stávající skupině synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Vrátí seznam synchronizace schémata databáze centra |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/logs/read | Vrátí seznam protokolů skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/read | Vrátí seznam synchronizace skupiny nebo načte vlastnosti zadaného synchronizace skupiny. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Aktualizovat schéma databáze Centrum synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu synchronizace centra |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Odstraní existující člen synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Vrátí seznam členy synchronizace nebo získá vlastnosti pro člena zadané synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Aktualizovat schéma synchronizace členů |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu synchronizace členů |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Vrátí seznam synchronizace člen databázových schématech |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Vytvoří člen synchronizace se zadanými parametry nebo aktualizuje vlastnosti pro člena zadané synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Aktivační událost synchronizace skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/write | Vytvoří se skupina synchronizace se zadanými parametry nebo aktualizuje vlastnosti zadaného synchronizace skupiny. |
> | Akce | Microsoft.Sql/servers/databases/topQueries/queryText/action | Vrátí text příkazů jazyka Transact-SQL pro ID vybraného dotazu |
> | Akce | Microsoft.Sql/servers/databases/topQueries/read | Vrátí agregované statistické údaje pro vybraný dotaz ve vybraném časovém období |
> | Akce | Microsoft.Sql/servers/databases/topQueries/statistics/read | Vrátí agregované statistické údaje pro vybraný dotaz ve vybraném časovém období |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Získá probíhajících operací na transparentní šifrování dat |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Načíst stav a podrobnosti o funkci zabezpečení transparentní data šifrování pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Změnit stav šifrování transparentní dat |
> | Akce | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Upgrade databáze datového skladu Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/usages/read | Získá informace o použití Azure SQL Database |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Odstranit posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Načíst zásady posouzení ohrožení zabezpečení na givendatabase |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Odstranit posouzení ohrožení zabezpečení pravidlo směrný plán pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Získejte základní pravidlo posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Změňte základní pravidlo posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Převeďte existující výsledku skenování lidské čitelném formátu. Pokud už existuje, nic se stane |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Spusťte posouzení zabezpečení databáze. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Vrátí seznam ohrožení zabezpečení databáze záznamy skenování hodnocení nebo získat kontrolu záznam pro ID zadaného kontroly. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Změnit posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Spusťte posouzení zabezpečení databáze. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Načíst výsledky posouzení ohrožení zabezpečení databáze operaci zpracování |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Načíst podrobnosti o posouzení ohrožení zabezpečení, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Změnit posouzení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/write | Vytvoří databázi se zadanými parametry nebo aktualizovat vlastnosti a značky pro určenou databázi. |
> | Akce | Microsoft.Sql/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Odstraní existující konfigurace zotavení po havárii pro daný server |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Vynucené převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Získá kolekci po havárii konfiguracích zotavení, které obsahují tento server |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Změna konfigurace zotavení po havárii serveru |
> | Akce | Microsoft.Sql/servers/elasticPoolEstimates/read | Vrátí seznam již vytvořené pro tento server odhady elastického fondu |
> | Akce | Microsoft.Sql/servers/elasticPoolEstimates/write | Vytvoří nový odhad elastického fondu pro seznam databází, které jsou k dispozici |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/read | Vrátí seznam poradci pro elastický fond k dispozici |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Vrátí seznam doporučených akcích zadané služby advisor pro elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Použije doporučenou akci v elastickém fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/write | Aktualizace automaticky spustit stav advisor na úrovni elastického fondu. |
> | Akce | Microsoft.Sql/servers/elasticPools/databases/read | Získá seznam databází pro elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/delete | Odstranění existujícího elastického fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Načíst aktivity a podrobnosti o daném elastického databázového fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Získání aktivity a informace o dané databázi, která je součástí fondu elastické databáze |
> | Akce | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro fondy elastických databází |
> | Akce | Microsoft.Sql/servers/elasticPools/metrics/read | Vrátit metriky pro fondy elastických databází |
> | Akce | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Zruší elastický fond Azure SQL čekající na asynchronní operaci, která ještě není dokončená. |
> | Akce | Microsoft.Sql/servers/elasticPools/operations/read | Vrátí seznam operací provedených v elastickém fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro fondy elastických databází |
> | Akce | Microsoft.Sql/servers/elasticPools/read | Načíst podrobnosti o elastického fondu na daném serveru |
> | Akce | Microsoft.Sql/servers/elasticPools/skus/read | Načte kolekci dostupných skladových položek pro elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/write | Vytvořit nové nebo změnit vlastnosti existujícího elastického fondu |
> | Akce | Microsoft.Sql/servers/encryptionProtector/read | Vrátí seznam hodnot ochrany pomocí šifrování serveru nebo načte vlastnosti pro zadaný server ochrana šifrování. |
> | Akce | Microsoft.Sql/servers/encryptionProtector/write | Aktualizací vlastností pro zadaný šifrování Ochrana serveru. |
> | Akce | Microsoft.Sql/servers/extendedAuditingSettings/read | Načíst podrobnosti o objektu blob rozšířený server auditování zásady nakonfigurované na daném serveru |
> | Akce | Microsoft.Sql/servers/extendedAuditingSettings/write | Změnit server rozšířených auditování objektů blob pro daný server |
> | Akce | Microsoft.Sql/servers/failoverGroups/delete | Odstraní existující skupinu převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/failover/action | Provede plánované převzetí služeb při selhání ve stávající skupině převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Provede Vynucené převzetí služeb při selhání ve stávající skupině převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/read | Vrátí seznam převzetí služeb při selhání skupiny nebo načte vlastnosti zadaného převzetí služeb při selhání skupiny. |
> | Akce | Microsoft.Sql/servers/failoverGroups/write | Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti a značky pro skupinu převzetí služeb při selhání zadané. |
> | Akce | Microsoft.Sql/servers/firewallRules/delete | Odstraní stávající pravidlo brány firewall serveru. |
> | Akce | Microsoft.Sql/servers/firewallRules/read | Vrátí seznam brány firewall serveru, pravidla nebo načte vlastnosti pro zadaný server, pravidlo brány firewall. |
> | Akce | Microsoft.Sql/servers/firewallRules/write | Vytvoří pravidlo brány firewall serveru se zadanými parametry, aktualizovat vlastnosti zadaného pravidla nebo přepsat všechny existující pravidla nového pravidla brány firewall serveru. |
> | Akce | Microsoft.Sql/servers/import/action | Vytvořit novou databázi na serveru a nasadit schéma a data z balíčku DacPac |
> | Akce | Microsoft.Sql/servers/importExportOperationResults/read | Získá probíhá operace importu/exportu |
> | Akce | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Odstraní zadané rozhraní profil koncového bodu |
> | Akce | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Vrací vlastnosti pro profil zadaný rozhraní koncového bodu |
> | Akce | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Vytvoří profil koncového bodu rozhraní se zadanými parametry nebo aktualizuje vlastnosti a značky pro koncový bod zadané rozhraní |
> | Akce | Microsoft.Sql/servers/jobAgents/delete | Odstraní úlohu agenta služby Azure SQL DB |
> | Akce | Microsoft.Sql/servers/jobAgents/read | Získá úloha agenta služby Azure SQL DB |
> | Akce | Microsoft.Sql/servers/jobAgents/write | Vytvoří nebo aktualizuje úlohu agenta služby Azure SQL DB |
> | Akce | Microsoft.Sql/servers/keys/delete | Odstraní existující klíč serveru. |
> | Akce | Microsoft.Sql/servers/keys/read | Vrátí seznam serveru klíče nebo načte vlastnosti pro klíč zadaný server. |
> | Akce | Microsoft.Sql/servers/keys/write | Vytvoří klíč se zadanými parametry nebo aktualizovat vlastnosti a značky pro klíč zadaný server. |
> | Akce | Microsoft.Sql/servers/operationResults/read | Získá probíhajících operací serveru |
> | Akce | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro servery |
> | Akce | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Načíst metriky pro doporučené elastických databázových fondů pro daný server |
> | Akce | Microsoft.Sql/servers/recommendedElasticPools/read | Načíst doporučení pro fondy elastických databází a snížení nákladů na zlepšení výkonu na základě využití prostředků historica |
> | Akce | Microsoft.Sql/servers/recoverableDatabases/read | Tato operace slouží k obnovení databáze k poslední známé dobré bodu zálohy pro zotavení po havárii živé databáze. Vrátí informace o poslední dobré zálohy, ale doesn\u0027t skutečně obnovení databáze. |
> | Akce | Microsoft.Sql/servers/replicationLinks/read | Vrátí seznam replikace odkazy nebo načte vlastnosti zadaného replikačních připojení. |
> | Akce | Microsoft.Sql/servers/restorableDroppedDatabases/read | Získáte seznam databází, které byly zahozeny na daném serveru, které jsou stále v rámci zásady uchovávání informací. |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Načíst výsledky operace zápisu zásad detekce hrozeb serveru |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/read | Načíst podrobnosti o zásadu detekce hrozeb server nakonfigurovaný na daném serveru |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/write | Změna serveru zásadu detekce hrozeb pro daný server |
> | Akce | Microsoft.Sql/servers/serviceObjectives/read | Načíst seznam cílů na úrovni služby (označované také jako úrovní výkonu) k dispozici na daném serveru |
> | Akce | Microsoft.Sql/servers/syncAgents/delete | Odstraní existující agenta synchronizace. |
> | Akce | Microsoft.Sql/servers/syncAgents/generateKey/action | Generovat klíč registrace agenta synchronizace |
> | Akce | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Vrátí seznam agenta připojeného databáze synchronizace |
> | Akce | Microsoft.Sql/servers/syncAgents/read | Vrátí seznam agenti synchronizace nebo získá vlastnosti pro agenta zadaného synchronizace. |
> | Akce | Microsoft.Sql/servers/syncAgents/write | Vytvoří agenta synchronizace se zadanými parametry nebo aktualizace pro agenta synchronizace zadané vlastnosti. |
> | Akce | Microsoft.Sql/servers/tdeCertificates/action | Transparentní šifrování dat vytvoří nebo aktualizuje certifikát |
> | Akce | Microsoft.Sql/servers/usages/read | Vrátit se kvóta DTU serveru a aktuální consuption DTU všem databázím na serveru |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/read | Vrátí seznam virtuálních sítí, pravidla nebo načte vlastnosti pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální sítě. |
> | Akce | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Odstranit posouzení ohrožení zabezpečení pro daný server |
> | Akce | Microsoft.Sql/servers/vulnerabilityAssessments/read | Načíst zásady posouzení ohrožení zabezpečení na daném serveru |
> | Akce | Microsoft.Sql/servers/vulnerabilityAssessments/write | Změnit posouzení ohrožení zabezpečení pro daný server |
> | Akce | Microsoft.Sql/servers/write | Vytvoří server se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |
> | Akce | Microsoft.Sql/unregister/action | Zruší registraci předplatného u poskytovatele prostředků Microsoft SQL Database a povolí vytváření databází Microsoft SQL. |
> | Akce | Microsoft.Sql/virtualClusters/read | Vrátí seznam virtuálních clusterů nebo získá vlastnosti pro zadanou virtuální cluster. |
> | Akce | Microsoft.Sql/virtualClusters/write | Aktualizuje virtuální cluster značky. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Storage/checknameavailability/read | Zkontroluje, že název účtu je platný a nepoužívá se. |
> | Akce | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Upozorní službu Microsoft.Storage, že se odstraňuje virtuální síť nebo podsítě. |
> | Akce | Microsoft.Storage/locations/usages/read | Vrací limit a aktuální počet použití pro zdroje v zadaném odběru |
> | Akce | Microsoft.Storage/operations/read | Zadá dotaz na stav asynchronní operace. |
> | Akce | Microsoft.Storage/register/action | Zaregistruje předplatné pro zprostředkovatele prostředku úložiště a povolí vytvoření účtů úložiště. |
> | Akce | Microsoft.Storage/skus/read | Vypíše SKU podporovaná v Microsoft.Storage. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Vrátí výsledek přidávání obsahu objektu blob. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstraňování objektu blob. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Vrátí výsledek odstranění automatického snímku. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů blob. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu objektu blob. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Vymazat blokování kontejneru objektů blob z právních důvodů |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Vrátí výsledek odstraňování kontejneru. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Odstranit zásady neměnnosti kontejneru objektů blob |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Rozšířit zásady neměnnosti kontejneru objektů blob |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Zamknout zásady neměnnosti kontejneru objektů blob |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Získat zásady neměnnosti kontejneru objektů blob |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Vložit zásady neměnnosti kontejneru objektů blob |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí kontejner nebo seznam kontejnerů. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Nastavit blokování kontejneru objektů blob z právních důvodů |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/containers/write | Vrátí výsledek vložení nebo vypůjčení kontejneru objektů blob. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Vrátí klíč delegování uživatele pro Blob service. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/read | Vrátí vlastnosti nebo statistiky služby Blob Service. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/write | Vrátí výsledek vložení vlastností služby Blob Service. |
> | Akce | Microsoft.Storage/storageAccounts/delete | Odstraní existující účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/lastsynctime/read | Vrátí čas poslední synchronizace účtu úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/listAccountSas/action | Vrátí token SAS určeného účtu úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/listServiceSas/action | Vrátí token SAS služby pro určený účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Vrátí výsledek odstranění fronty. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Vrátí výsledek přidání zprávy. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Vrátí výsledek odstranění zprávy. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Vrátí výsledek zpracování zprávy. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Vrátí zprávu. |
> | Hodnotu DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Vrátí výsledek zápisu zprávy. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/write | Vrátí výsledek zápisu fronty. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/read | Vrátí vlastnosti nebo statistiky služby Queue. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/write | Vrátí výsledky nastavení vlastností služby Queue. |
> | Akce | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Akce | Microsoft.Storage/storageAccounts/regeneratekey/action | Obnoví přístupové klíče pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Odvolá všechny klíče delegování uživatele pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky účtu úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/write | Vytvoří účet úložiště se zadanými parametry, aktualizuje vlastnosti a značky nebo přidá vlastní doménu pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/usages/read | Vrací limit a aktuální počet použití pro zdroje v zadaném odběru |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | microsoft.storagesync/locations/checkNameAvailability/action | Kontroluje, že název služby synchronizace úložiště je platný a nepoužívá. |
> | Akce | Microsoft.storagesync/Locations/workflows/Operations/Read | Umožňuje získat stav asynchronní operace. |
> | Akce | microsoft.storagesync/storageSyncServices/delete | Odstranit všechny služby synchronizace úložiště |
> | Akce | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro služby synchronizace úložiště |
> | Akce | microsoft.storagesync/storageSyncServices/read | Číst všechny služby synchronizace úložiště |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/delete | Odstranit libovolný zaregistrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro registrovaného serveru |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/read | Číst všechny registrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/write | Vytvořit nebo aktualizovat libovolný zaregistrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Odstranit všechny koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Získá stav operace asynchronního zálohování a obnovení |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Volejte tuto akci po dokončení zálohování |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Volejte tuto akci po obnovení |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Volejte tuto akci před zálohováním |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Volejte tuto akci před obnovení |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Číst všechny koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Obnovení prezenčního signálu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Vytvořit nebo aktualizovat všechny koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/delete | Odstraňovat žádné skupiny synchronizace. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro skupiny synchronizace. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/read | Přečtěte si žádné skupiny synchronizace. |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Odstranit všechny koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Číst všechny koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Tato akce vrátit soubory na server volání |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Vytvořit nebo aktualizovat všechny koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/write | Vytvořit nebo aktualizovat všechny skupiny synchronizace |
> | Akce | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Umožňuje získat stav asynchronní operace. |
> | Akce | microsoft.storagesync/storageSyncServices/workflows/operations/read | Umožňuje získat stav asynchronní operace. |
> | Akce | microsoft.storagesync/storageSyncServices/workflows/read | Pracovní postupy pro čtení |
> | Akce | microsoft.storagesync/storageSyncServices/write | Vytvořit nebo aktualizovat všechny služby synchronizace úložiště |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/delete | Odstraní záznamy řízení přístupu |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/read | Obsahuje seznam nebo získá záznamy řízení přístupu |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/write | Vytvořit nebo aktualizovat záznamy řízení přístupu |
> | Akce | Microsoft.StorSimple/managers/alerts/read | Obsahuje seznam nebo získá upozornění |
> | Akce | Microsoft.StorSimple/managers/backups/read | Obsahuje seznam nebo získá zálohovacího skladu |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/delete | Odstraní stávající nastavení šířky pásma (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/read | Seznam nastavení šířky pásma (pouze 8000 Series) |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/write | Vytvoří nový nebo aktualizuje nastavení šířky pásma (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/certificates/write | Vytvoření nebo aktualizace certifikátů |
> | Akce | Microsoft.StorSimple/Managers/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů prostředku nebo úložišti. |
> | Akce | Microsoft.StorSimple/managers/clearAlerts/action | Vymažte všechna upozornění přidružený ke Správci zařízení. |
> | Akce | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Seznam cloudového zařízení podporované konfigurace |
> | Akce | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje zařízení |
> | Akce | Microsoft.StorSimple/managers/delete | Odstraní Správci zařízení |
> | Akce | Microsoft.StorSimple/Managers/delete | Operace odstranit úložiště odstraní zadaný prostředek Azure typu "úložiště. |
> | Akce | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/alertSettings/read | Obsahuje seznam nebo získá nastavení výstrah |
> | Akce | Microsoft.StorSimple/managers/devices/alertSettings/write | Vytvořit nebo aktualizovat nastavení výstrahy |
> | Akce | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizace pro službu šifrovacího klíče zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Vytvořit ruční zálohy na vyžádání vytvořit zálohu všech svazků, které jsou chráněné zásadami. |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Odstraní existující zásady zálohování (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/read | Seznam zálohování zásady (pouze 8000 Series) |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Odstraní existující plány |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Seznam plánů |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Vytvoří novou nebo aktualizovat plány |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/write | Vytvoří novou nebo aktualizovat zásady zálohování (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/backups/delete | Odstraní zálohovací sklad |
> | Akce | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klonovat sdílenou složku nebo svazek pomocí elementu zálohování. |
> | Akce | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/backups/read | Obsahuje seznam nebo získá zálohovacího skladu |
> | Akce | Microsoft.StorSimple/managers/devices/backups/restore/action | Obnovení všech svazků ze zálohovacího skladu. |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Odstraní plán zálohování skupiny |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Obsahuje seznam nebo získá skupiny plánu zálohování |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Vytvoření nebo aktualizace skupiny plánu zálohování |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/delete | Odstraní nastavení protokolu Chap |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/read | Obsahuje seznam nebo získá nastavení protokolu Chap |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/write | Vytvořit nebo aktualizovat nastavení protokolu Chap |
> | Akce | Microsoft.StorSimple/managers/devices/deactivate/action | Deaktivuje zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/delete | Odstraní zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/disks/read | Obsahuje seznam nebo získá disky |
> | Akce | Microsoft.StorSimple/managers/devices/download/action | Stáhnout aktualizace pro zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/failover/action | Převzetí služeb při selhání zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/failoverTargets/read | Obsahuje seznam nebo získá cíle převzetí služeb při selhání zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Vytvořit zálohu souborového serveru. |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/delete | Odstraní souborových serverů |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Obsahuje seznam nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Obsahuje seznam nebo načíst definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/read | Obsahuje seznam nebo získá souborových serverů |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Odstraní sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Obsahuje seznam nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Obsahuje seznam nebo načíst definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Obsahuje seznam nebo získá sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Vytvořit nebo aktualizovat sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/write | Vytvořit nebo aktualizovat na souborových serverech |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Změnit stav napájení řadiče skupin součástí hardwaru |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Seznam skupiny součástí hardwaru |
> | Akce | Microsoft.StorSimple/managers/devices/install/action | Nainstalujte aktualizace na zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/installUpdates/action | Nainstaluje aktualizace na zařízení (pouze řady 8000). |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Vytvořit zálohu serveru iSCSI. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Odstraní servery iSCSI |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Odstraní disky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Obsahuje seznam nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Obsahuje seznam nebo načíst definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Obsahuje seznam nebo získá disky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Vytvořit nebo aktualizovat disky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Obsahuje seznam nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Obsahuje seznam nebo načíst definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/read | Obsahuje seznam nebo získá servery iSCSI |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/write | Vytvořit nebo aktualizovat servery iSCSI |
> | Akce | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Zrušit běžící úlohu |
> | Akce | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/jobs/read | Obsahuje seznam nebo získá úlohy |
> | Akce | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Seznam skupin převzetí služeb při selhání pro stávající zařízení (pouze řady 8000). |
> | Akce | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Seznam cílů převzetí služeb při selhání zařízení (pouze řady 8000). |
> | Akce | Microsoft.StorSimple/managers/devices/metrics/read | Obsahuje seznam nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Obsahuje seznam nebo načíst definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Potvrzuje úspěšné migrace a potvrďte ho. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Seznam potvrdit stav migrace |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Načte stav potvrzení migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Načte stav úlohy migrace odhadu. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Načte stav pro migraci. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importovat zdroje konfigurace pro migraci |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Seznam odhad migrace |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Vypíše stav migrace |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Spustit migraci pomocí zdroj konfigurace |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Spusťte úlohu pro odhad doby trvání procesu migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/read | Obsahuje seznam nebo získá nastavení sítě |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/write | Vytvoří novou nebo aktualizovat nastavení sítě |
> | Akce | Microsoft.StorSimple/managers/devices/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Seznam veřejný šifrovací klíč Správce zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publikujte balíček pro podporu zařízení pro řešení potíží s Microsoft Support. |
> | Akce | Microsoft.StorSimple/managers/devices/read | Obsahuje seznam nebo získá zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Vyhledejte aktualizace zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/read | Seznam nastavení zabezpečení |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizujte certifikát vzdálené správy pro zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aktualizujte nastavení zabezpečení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/write | Vytvoří nový nebo aktualizuje nastavení zabezpečení |
> | Akce | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Odešlete zkušební e-mail s upozorněním příjemců e-mailu nakonfigurovaná. |
> | Akce | Microsoft.StorSimple/managers/devices/shares/read | Obsahuje seznam nebo získá sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/read | Obsahuje seznam nebo získá nastavení času |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/write | Vytvoří novou nebo aktualizovat nastavení času |
> | Akce | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/devices/updateSummary/read | Obsahuje seznam nebo získá shrnutí aktualizací |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Odstraní existující kontejnery svazků (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Seznam metrik |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Seznam definic metrik |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/read | Seznam kontejnerů svazků (pouze 8000 Series) |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Odstraní existující svazky |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Seznam metrik |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Seznam definic metrik |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Vypsat výsledek operace |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Seznam svazků |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Vytvoří nový nebo aktualizuje svazky |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/write | Vytvoří nový nebo aktualizuje kontejnery svazků (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/volumes/read | Seznam svazků |
> | Akce | Microsoft.StorSimple/managers/devices/write | Vytvořit nebo aktualizovat zařízení |
> | Akce | Microsoft.StorSimple/managers/encryptionSettings/read | Obsahuje seznam nebo získá nastavení šifrování |
> | Akce | Microsoft.StorSimple/managers/extendedInformation/delete | Odstraní informace o rozšířených trezoru |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/delete | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/managers/extendedInformation/read | Obsahuje seznam nebo získá rozšířené informace trezoru |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/managers/extendedInformation/write | Vytvořit nebo aktualizovat informace o rozšířených trezoru |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/managers/features/read | Seznam funkcí |
> | Akce | Microsoft.StorSimple/managers/fileservers/read | Obsahuje seznam nebo získá souborových serverů |
> | Akce | Microsoft.StorSimple/managers/getActivationKey/action | Získáte aktivační klíč pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/getEncryptionKey/action | Získání šifrovacího klíče pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/iscsiservers/read | Obsahuje seznam nebo získá servery iSCSI |
> | Akce | Microsoft.StorSimple/managers/jobs/read | Obsahuje seznam nebo získá úlohy |
> | Akce | Microsoft.StorSimple/managers/listActivationKey/action | Získá klíč aktivaci Správce zařízení StorSimple. |
> | Akce | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Seznam veřejný šifrovací klíče nástroje Správce zařízení StorSimple. |
> | Akce | Microsoft.StorSimple/managers/metrics/read | Obsahuje seznam nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/metricsDefinitions/read | Obsahuje seznam nebo načíst definice metrik |
> | Akce | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrace modelu Classic na Resource Manager z Mangers |
> | Akce | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Vypíše konfiguraci migrace zdroje (pouze 8000 Series) |
> | Akce | Microsoft.StorSimple/managers/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Vytvoření nového cloudového zařízení. |
> | Akce | Microsoft.StorSimple/managers/read | Obsahuje seznam nebo získá správce zařízení |
> | Akce | Microsoft.StorSimple/Managers/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Akce | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Znovu vygenerovat registrační certifikát pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/regenerateActivationKey/action | Znovu vygenerovat aktivační kód pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Odstraní přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/read | Obsahuje seznam nebo získá přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/write | Vytvořit nebo aktualizovat přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageDomains/delete | Odstraní domény úložišť |
> | Akce | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Obsahuje seznam nebo načte výsledky operace |
> | Akce | Microsoft.StorSimple/managers/storageDomains/read | Obsahuje seznam nebo získá domény úložišť |
> | Akce | Microsoft.StorSimple/managers/storageDomains/write | Vytvořit nebo aktualizovat domény úložišť |
> | Akce | Microsoft.StorSimple/managers/write | Vytvořit nebo aktualizovat Správce zařízení |
> | Akce | Microsoft.StorSimple/Managers/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/register/action | Zaregistrovat poskytovatele Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.StreamAnalytics/locations/quotas/Read | Kvóta předplatného Stream Analytics pro čtení |
> | Akce | Microsoft.StreamAnalytics/operations/Read | Operace čtení Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/Register/action | Zaregistrovat předplatné u poskytovatele prostředků služby Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Delete | Odstranění úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Odstranit funkci úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Přečíst výsledky operace pro funkci úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Číst Stream Analytics úlohy – funkce |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Načíst výchozí definici funkce úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics úlohy – funkce |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Zápis funkce úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Odstranit vstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Přečíst výsledky operace pro vstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Vstup úlohy Stream Analytics pro čtení |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Vstup úlohy Stream Analytics vzorku |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Vstup úlohy Stream Analytics testu |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Zápis vstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Přečíst definice metrik |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Přečíst výsledky operace pro úlohu Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Odstranit výstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Přečíst výsledky operace pro výstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Výstup úlohy Stream Analytics pro čtení |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Výstup úlohy Stream Analytics testu |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Zapisovat výstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Přečíst nastavení diagnostiky. |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Zapsat nastavení diagnostiky. |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro streamingjobs |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro streamingjobs |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Read | Úlohy Stream Analytics pro čtení |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Start/action | Spuštění úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Zastavení úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Odstranit transformace úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Transformace úlohy Stream Analytics pro čtení |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Zápis transformace úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Write | Zápis úlohy Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Subscription/CreateSubscription/action | Vytvořit předplatné Azure |
> | Akce | Microsoft.Subscription/SubscriptionDefinitions/read | Získáte definici předplatného Azure v rámci skupiny pro správu. |
> | Akce | Microsoft.Subscription/SubscriptionDefinitions/write | Vytvoření definice předplatného Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

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
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Odstraní zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Získá vlastnosti zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Vytvoří nové zásady přístupu pro prostředí nebo aktualizuje existující zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/delete | Odstraní prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Odstraní zdroji události. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/read | Získá vlastnosti zdroje událostí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/write | Vytvoří nový zdroj událostí pro prostředí nebo aktualizuje existující zdroje událostí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/read | Získá vlastnosti prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Odstraní odkaz datové sady. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Získá vlastnosti referenční datové sady. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Vytvoří nový referenční sady dat pro prostředí nebo aktualizuje existující referenční sady dat. |
> | Akce | Microsoft.TimeSeriesInsights/environments/status/read | Získání stavu prostředí a stav jeho přidružené operace, jako je příjem příchozích dat. |
> | Akce | Microsoft.TimeSeriesInsights/environments/write | Vytvoří nové prostředí nebo aktualizuje stávající prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/register/action | Zaregistruje předplatné u poskytovatele prostředků služby Time Series Insights a povolí vytváření prostředí Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.VisualStudio/Account/Delete | Odstranit účet |
> | Akce | Microsoft.VisualStudio/Account/Extension/Read | Přečtěte si účet a rozšíření |
> | Akce | Microsoft.VisualStudio/Account/Project/Read | Čtení účtu a projektu |
> | Akce | Microsoft.VisualStudio/Account/Project/Write | Nastavení účtu a projektu |
> | Akce | Microsoft.VisualStudio/Account/Read | Účet pro čtení |
> | Akce | Microsoft.VisualStudio/Account/Write | Nastavení účtu |
> | Akce | Microsoft.VisualStudio/Extension/Delete | Odstranit rozšíření |
> | Akce | Microsoft.VisualStudio/Extension/Read | Rozšíření pro čtení |
> | Akce | Microsoft.VisualStudio/Extension/Write | Rozšíření sady |
> | Akce | Microsoft.VisualStudio/Project/Delete | Odstranit projekt |
> | Akce | Microsoft.VisualStudio/Project/Read | Projekt pro čtení |
> | Akce | Microsoft.VisualStudio/Project/Write | Nastavení projektu |
> | Akce | Microsoft.VisualStudio/Register/Action | Zaregistrovat předplatné Azure s poskytovatelem Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Web/apimanagementaccounts/apiacls/Read | Získejte Apiacls účty rozhraní Api Management. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/apiacls/DELETE | Odstraňte rozhraní Api Management účty rozhraní API Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Read | Získání rozhraní Api Management účty rozhraní API Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Aktualizace rozhraní Api Management účty rozhraní API Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/connectionacls/Read | Získání rozhraní Api Management účty rozhraní API Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Potvrďte souhlas kódu rozhraní Api Management účty rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/DELETE | Odstraňte rozhraní Api Management účty rozhraní API připojení Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Read | Získání rozhraní Api Management účty rozhraní API připojení Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Aktualizace rozhraní Api Management účty rozhraní API připojení Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Odstraňte připojení Api Management účty rozhraní API. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Získáte odkazy na souhlas pro Api Management účty rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Seznam klíčů rozhraní Api Management účty rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Seznam tajných kódů připojení rozhraní Api Management účty rozhraní API. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/Read | Získáte rozhraní Api Management účty rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Aktualizujte připojení Api Management účty rozhraní API. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Odstraní účty rozhraní API služby Api Management. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/DELETE | Odstranit rozhraní Api Management účty rozhraní API lokalizované definice. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Správa Api účty rozhraní API lokalizované definice. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Účty rozhraní API pro správu rozhraní Api aktualizace lokalizované definice. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Read | Získáte účty rozhraní API služby Api Management. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Write | Aktualizace rozhraní API služby Api Management účty. |
> | Akce | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Získejte Connectionacls účty rozhraní Api Management. |
> | Akce | Microsoft.Web/availablestacks/Read | Získáte dostupné balíčky. |
> | Akce | Microsoft.Web/certificates/Delete | Odstranění existujícího certifikátu. |
> | Akce | Microsoft.Web/certificates/Read | Získáte seznam certifikátů. |
> | Akce | Microsoft.Web/certificates/Write | Přidat nový certifikát nebo aktualizuje stávající. |
> | Akce | Microsoft.Web/checknameavailability/Read | Zkontrolujte, jestli je k dispozici název prostředku. |
> | Akce | Microsoft.Web/classicmobileservices/Read | Získejte Classic mobilní služby. |
> | Akce | Microsoft.Web/connectionGateways/Delete | Odstraní připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Join/Action | Spojí připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/ListStatus/Action | Zobrazí stav připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Move/Action | Přesune připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Read | Získá seznam připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Write | Vytvoří nebo aktualizuje připojení brány. |
> | Akce | Microsoft.Web/Connections/confirmconsentcode/Action | Potvrzení kódu souhlasu připojení. |
> | Akce | Microsoft.Web/connections/Delete | Odstraní připojení. |
> | Akce | Microsoft.Web/connections/Join/Action | Spojí připojení. |
> | Akce | Microsoft.Web/Connections/listconsentlinks/Action | Odkazy na souhlas seznamu pro připojení. |
> | Akce | Microsoft.Web/connections/Move/Action | Přesune připojení. |
> | Akce | Microsoft.Web/connections/Read | Získá seznam připojení. |
> | Akce | Microsoft.Web/connections/Write | Vytvoří nebo aktualizuje připojení. |
> | Akce | Microsoft.Web/customApis/Delete | Odstraní vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Definice rozhraní API extrahuje ze souboru WSDL. |
> | Akce | Microsoft.Web/customApis/Join/Action | Spojí vlastního rozhraní API. |
> | Akce | Microsoft.Web/customApis/listWsdlInterfaces/Action | Seznam rozhraní WSDL pro vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/Move/Action | Přesune vlastního rozhraní API. |
> | Akce | Microsoft.Web/customApis/Read | Získání seznamu vlastních rozhraní API. |
> | Akce | Microsoft.Web/customApis/Write | Vytvoří nebo aktualizuje vlastní rozhraní API. |
> | Akce | Microsoft.Web/deletedSites/Read | Získání vlastností webové aplikace odstraněna |
> | Akce | Microsoft.Web/deploymentlocations/Read | Získáte umístění nasazení. |
> | Akce | Microsoft.Web/geoRegions/Read | Získá seznam geografických oblastech. |
> | Akce | Microsoft.Web/hostingenvironments/capacities/Read | Získat hostování prostředí kapacity. |
> | Akce | Microsoft.Web/hostingEnvironments/Delete | Odstranění služby App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/detectors/Read | Získat hostování detektory prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/Diagnostics/Read | Získat hostování prostředí diagnostiky. |
> | Akce | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Získejte koncové body sítě všechny příchozí závislosti. |
> | Akce | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Získat hostování prostředí definice metrik. |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Získat hostování prostředí definice metrik u fondů. |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Získat hostování prostředí u fondů metriky. |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Získá vlastnosti front-endový fond ve službě App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Získat hostování prostředí u fondů SKU. |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/usages/Read | Získat hostování prostředí u fondů použití. |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Vytvořit nový front-endový fond ve službě App Service Environment nebo aktualizaci existující |
> | Akce | Microsoft.Web/hostingenvironments/Operations/Read | Získat hostování prostředí operace. |
> | Akce | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Získejte koncové body sítě všechny odchozí závislosti. |
> | Akce | Microsoft.Web/hostingEnvironments/Read | Získání vlastností služby App Service Environment |
> | Akce | Microsoft.Web/hostingEnvironments/reboot/Action | Restartujte všechny počítače ve službě App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/Resume/Action | Pokračovat, který je hostitelem prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/serverfarms/Read | Získejte hostování prostředí plány služby App Service. |
> | Akce | Microsoft.Web/hostingenvironments/Sites/Read | Získejte hostování prostředí webové aplikace. |
> | Akce | Microsoft.Web/hostingenvironments/suspend/Action | Pozastavit, který je hostitelem prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/usages/Read | Použití prostředí získat hostování. |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Získat hostování definice metrik Workerpools prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Získat hostování prostředí Workerpools metriky. |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/Read | Získání vlastností fondu pracovních procesů ve službě App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Získat hostování prostředí Workerpools SKU. |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/usages/Read | Získat hostování prostředí Workerpools použití. |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/Write | Vytvoření nového fondu pracovních procesů ve službě App Service Environment nebo aktualizaci existující |
> | Akce | Microsoft.Web/hostingEnvironments/Write | Vytvoření nové služby App Service Environment nebo aktualizaci existující |
> | Akce | Microsoft.Web/ishostingenvironmentnameavailable/Read | Pokud je k dispozici název prostředí hostování se. |
> | Akce | Microsoft.Web/ishostnameavailable/Read | Zkontrolujte, jestli název hostitele je k dispozici. |
> | Akce | Microsoft.Web/isusernameavailable/Read | Zkontrolujte, jestli uživatelské jméno je k dispozici. |
> | Akce | Microsoft.Web/listSitesAssignedToHostName/Read | Získáte názvy lokalit, které jsou přiřazeny k názvu hostitele. |
> | Akce | Microsoft.Web/Locations/apioperations/Read | Získáte operace umístění rozhraní API. |
> | Akce | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Získání umístění instalace brány připojení. |
> | Akce | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Virtuální síť nebo podsítě odstranění oznámení pro umístění. |
> | Akce | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Extrahuje definice rozhraní Api z WSDL pro umístění. |
> | Akce | Microsoft.Web/Locations/listwsdlinterfaces/Action | Seznam rozhraní WSDL pro umístění. |
> | Akce | Microsoft.Web/Locations/managedapis/apioperations/Read | Získáte operace rozhraní API spravované umístění. |
> | Akce | Microsoft.Web/locations/managedapis/Join/Action | Spojí spravovaném rozhraní API. |
> | Akce | Microsoft.Web/Locations/managedapis/Read | Získání umístění spravovaných rozhraní API. |
> | Akce | Microsoft.Web/Operations/Read | Získáte operace. |
> | Akce | Microsoft.Web/publishingusers/Read | Získat publikují se uživatelé. |
> | Akce | Microsoft.Web/publishingusers/Write | Aktualizace publikují se uživatelé. |
> | Akce | Microsoft.Web/recommendations/Read | Získá seznam doporučení pro předplatná. |
> | Akce | Microsoft.Web/Register/Action | Registrace poskytovatele prostředků Microsoft.Web pro předplatné. |
> | Akce | Microsoft.Web/resourcehealthmetadata/Read | Získáte Resource Health Metadata. |
> | Akce | Microsoft.Web/serverfarms/Capabilities/Read | Získejte možnosti plánů služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Delete | Odstranit existující plán služby App Service |
> | Akce | Microsoft.Web/serverfarms/firstpartyapps/Settings/DELETE | Odstraňte první strany aplikace nastavení plánů služby App Service. |
> | Akce | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | Získáte první strany aplikace nastavení plánů služby App Service. |
> | Akce | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Aktualizujte nastavení první strany aplikace plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/DELETE | Odstranění služby App Service plány hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | Získejte služby App Service plány hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | Získejte plány služby App Service hybridní připojení obory názvů propojení webové aplikace. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | Získejte omezení plánu služby App Service plány hybridní připojení. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | Získejte přenosy připojení hybridní plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/metricdefinitions/Read | Získáte definice metriky plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Metrics/Read | Získáte metriky plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/operationresults/Read | Načte výsledky operace plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Read | Získá vlastnosti na plán služby App Service |
> | Akce | Microsoft.Web/serverfarms/restartSites/Action | Restartujte všechny webové aplikace v plánu App Service |
> | Akce | Microsoft.Web/serverfarms/Sites/Read | Získání aplikací webových plánů služby App Service. |
> | Akce | Microsoft.Web/serverfarms/skus/Read | Získáte položky SKU plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/usages/Read | Získáte použití plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/gateways/Write | Aktualizace brány připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Získáte připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/DELETE | Odstraňte tras připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/Read | Získejte tras připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/Write | Aktualizace tras připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/workers/reboot/Action | Restartování pracovních procesů plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Write | Nový plán služby App Service umožňuje vytvořit nebo aktualizovat existující |
> | Akce | Microsoft.Web/Sites/analyzecustomhostname/Read | Analyzujte vlastní název hostitele. |
> | Akce | Microsoft.Web/sites/applySlotConfig/Action | Použití konfigurace webové aplikace slotu z cílového slotu na aktuální webové aplikace |
> | Akce | Microsoft.Web/sites/backup/Action | Vytvořit novou zálohu webové aplikace |
> | Akce | Microsoft.Web/Sites/Backup/Read | Získejte zálohy Web Apps. |
> | Akce | Microsoft.Web/Sites/Backup/Write | Aktualizace zálohy Web Apps. |
> | Akce | Microsoft.Web/Sites/backups/Action | Zjistí existující zálohu aplikace, která se dá obnovit z objektu blob ve službě Azure storage. |
> | Akce | Microsoft.Web/Sites/backups/DELETE | Odstraňte zálohy Web Apps. |
> | Akce | Microsoft.Web/Sites/backups/list/Action | Seznam zálohy Web Apps. |
> | Akce | Microsoft.Web/sites/backups/Read | Získá vlastnosti zálohování webové aplikace |
> | Akce | Microsoft.Web/Sites/backups/Restore/Action | Obnovte zálohy Web Apps. |
> | Akce | Microsoft.Web/Sites/backups/Write | Aktualizace zálohy Web Apps. |
> | Akce | Microsoft.Web/Sites/config/DELETE | Odstraňte konfiguraci webové aplikace. |
> | Akce | Microsoft.Web/sites/config/list/Action | Seznam webové aplikace citlivá nastavení zabezpečení, jako je například publikování přihlašovací údaje, nastavení aplikace a připojovacích řetězců |
> | Akce | Microsoft.Web/sites/config/Read | Získat nastavení konfigurace webové aplikace |
> | Akce | Microsoft.Web/Sites/config/snapshots/Read | Získáte snímky webové konfigurace aplikace. |
> | Akce | Microsoft.Web/sites/config/Write | Aktualizovat nastavení konfigurace webové aplikace |
> | Akce | Microsoft.Web/Sites/containerlogs/Action | Získat ZIP protokoly kontejneru pro webovou aplikaci. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/DELETE | Odstranění webové aplikace průběžné webové úlohy. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/Read | Získejte Web Apps průběžné webové úlohy. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Spuštění webové aplikace průběžné webové úlohy. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/stop/Action | Zastavení webové aplikace průběžné webové úlohy. |
> | Akce | Microsoft.Web/sites/Delete | Odstranit existující webové aplikace |
> | Akce | Microsoft.Web/Sites/Deployments/DELETE | Odstraňte nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Deployments/log/Read | Získání protokolu nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Deployments/Read | Získání nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Deployments/Write | Aktualizace nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/detectors/Read | Získejte detektory webové aplikace. |
> | Akce | microsoft.web/sites/diagnostics/analyses/execute/Action | Spuštění analýzy webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/analyses/Read | Získáte analýzy webových aplikací diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Získání diagnostiky webových aplikací pro aplikace ASP.NET Core. |
> | Akce | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Získejte Web Apps diagnostiky Autoheal. |
> | Akce | Microsoft.Web/Sites/Diagnostics/Deployment/Read | Získání diagnostiky aplikací nasazení webu. |
> | Akce | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Získání nasazení webové aplikace diagnostiky. |
> | Akce | microsoft.web/sites/diagnostics/detectors/execute/Action | Spuštění webové aplikace diagnostiky detektoru. |
> | Akce | Microsoft.Web/Sites/Diagnostics/detectors/Read | Získejte detektor webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Získejte Web Apps diagnostiky neúspěšné žádosti za identifikátor Uri. |
> | Akce | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Získejte webové analýzy FREB diagnostiky aplikace. |
> | Akce | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Získejte webové aplikace diagnostiky protokolu analyzátoru. |
> | Akce | Microsoft.Web/Sites/Diagnostics/Read | Získejte webových aplikací diagnostiky kategorií. |
> | Akce | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Získejte dostupnosti webové aplikace Diagnostika modulu Runtime. |
> | Akce | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Získání stavu webové služby diagnostiky aplikace. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Získáte webové aplikace diagnostiky webu procesoru analýzu. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Získejte chyby webové diagnostiky aplikací lokality. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Získejte webové aplikace diagnostiky webu latence. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Získáte webové aplikace diagnostiky webu paměti analýzu. |
> | Akce | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Získáte webové aplikace diagnostiky webu restartování nastavení aktualizaci. |
> | Akce | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Získejte webové aplikace diagnostiky webu restartování uživatelem iniciované. |
> | Akce | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Získejte prohození webové aplikace diagnostiky webu. |
> | Akce | Microsoft.Web/Sites/Diagnostics/ThreadCount/Read | Získáte počet vláken webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Získejte Workeravailability webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Získejte recyklaci pracovního procesu webové diagnostiky aplikace. |
> | Akce | Microsoft.Web/Sites/domainownershipidentifiers/Read | Získejte Web identifikátory vlastnictví domény aplikace. |
> | Akce | Microsoft.Web/Sites/domainownershipidentifiers/Write | Aktualizujte identifikátory vlastnictví webové domény aplikace. |
> | Akce | Microsoft.Web/Sites/Functions/Action | Funkce Web Apps. |
> | Akce | Microsoft.Web/Sites/Functions/DELETE | Odstranění webové aplikace funkcí. |
> | Akce | Microsoft.Web/Sites/Functions/listsecrets/Action | Seznam tajných kódů webové aplikace funkcí. |
> | Akce | Microsoft.Web/Sites/Functions/masterkey/Read | Získejte Masterkey funkce Web Apps. |
> | Akce | Microsoft.Web/Sites/Functions/Read | Získáte funkce Web Apps. |
> | Akce | Microsoft.Web/Sites/Functions/token/Read | Get webové aplikace funkcí Token. |
> | Akce | Microsoft.Web/Sites/Functions/Write | Aktualizace funkcí Web Apps. |
> | Akce | Microsoft.Web/Sites/hostnamebindings/DELETE | Odstranění vazby názvu hostitele aplikací Web. |
> | Akce | Microsoft.Web/Sites/hostnamebindings/Read | Získejte webové vazby názvu hostitele aplikací. |
> | Akce | Microsoft.Web/Sites/hostnamebindings/Write | Update Web Apps Hostname Bindings. |
> | Akce | Microsoft.Web/sites/hostruntime/host/_master/read | Získat aplikaci Function App hlavní klíč pro správce operací |
> | Akce | Microsoft.Web/sites/hostruntime/host/action | Provádění aplikace Function App akce modulu runtime, jako je synchronizace aktivačních událostí, přidejte funkce, volání funkce, odstranit funkce atd. |
> | Akce | Microsoft.Web/Sites/hybridconnection/DELETE | Odstranění webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnection/Read | Získejte webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnection/Write | Aktualizace webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Odstranění webové aplikace hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Seznam klíčů webové aplikace hybridní připojení obory názvů přenosy. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Získejte webové aplikace hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Aktualizace webové aplikace hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/Sites/hybridconnectionrelays/Read | Získejte předávací službu webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/instances/Deployments/DELETE | Odstraňte nasazení instancí webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Deployments/Read | Získání nasazení instancí webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Extensions/log/Read | Získejte Web Apps instancí rozšíření protokolu. |
> | Akce | Microsoft.Web/Sites/instances/Extensions/Read | Získáte rozšíření instancí webových aplikací. |
> | Akce | Microsoft.Web/Sites/instances/Processes/DELETE | Odstraňte procesy instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Processes/Read | Načíst procesy instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Processes/Threads/Read | Získejte Web Apps instancí procesů vlákna. |
> | Akce | Microsoft.Web/Sites/instances/Read | Získání instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Seznam synchronizace funkce triggeru stav webové aplikace. |
> | Akce | Microsoft.Web/Sites/metricdefinitions/Read | Získáte definice metriky aplikací Web. |
> | Akce | Microsoft.Web/Sites/Metrics/Read | Získá metriky webové aplikace. |
> | Akce | Microsoft.Web/Sites/metricsdefinitions/Read | Získáte definice metriky aplikací Web. |
> | Akce | Microsoft.Web/Sites/migratemysql/Action | Migrace MySql webové aplikace. |
> | Akce | Microsoft.Web/Sites/migratemysql/Read | Získejte Web Apps migraci MySql. |
> | Akce | Microsoft.Web/Sites/networktrace/Action | Síťové trasování webové aplikace. |
> | Akce | Microsoft.Web/Sites/newpassword/Action | Newpassword webové aplikace. |
> | Akce | Microsoft.Web/Sites/operationresults/Read | Načte výsledky operace webové aplikace. |
> | Akce | Microsoft.Web/Sites/Operations/Read | Získání operací webové aplikace. |
> | Akce | Microsoft.Web/Sites/perfcounters/Read | Získáte čítače výkonu webové aplikace. |
> | Akce | Microsoft.Web/Sites/premieraddons/DELETE | Odstraňte doplňky Premier webové aplikace. |
> | Akce | Microsoft.Web/Sites/premieraddons/Read | Získejte doplňky Premier webové aplikace. |
> | Akce | Microsoft.Web/Sites/premieraddons/Write | Aktualizace webové aplikace Premier doplňků. |
> | Akce | Microsoft.Web/Sites/privateaccess/Read | Získejte data týkající se povolení přístupu k soukromé lokality a autorizované virtuálních sítí s přístupem k webu. |
> | Akce | Microsoft.Web/Sites/Processes/Read | Načíst procesy webové aplikace. |
> | Akce | Microsoft.Web/Sites/publiccertificates/DELETE | Odstranění webové aplikace veřejné certifikáty. |
> | Akce | Microsoft.Web/Sites/publiccertificates/Read | Získejte webové aplikace veřejné certifikáty. |
> | Akce | Microsoft.Web/Sites/publiccertificates/Write | Aktualizace webové aplikace veřejné certifikáty. |
> | Akce | Microsoft.Web/sites/publish/Action | Publikování webové aplikace |
> | Akce | Microsoft.Web/sites/publishxml/Action | Získání souboru xml profilu publikování webové aplikace |
> | Akce | Microsoft.Web/Sites/publishxml/Read | Získat publikování webových aplikací XML. |
> | Akce | Microsoft.Web/sites/Read | Získání vlastností webové aplikace |
> | Akce | Microsoft.Web/Sites/recommendationhistory/Read | Zobrazit historii doporučení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Recommendations/disable/Action | Zakážete doporučení pro webové aplikace. |
> | Akce | Microsoft.Web/sites/recommendations/Read | Získá seznam doporučení pro webovou aplikaci. |
> | Akce | Microsoft.Web/Sites/Recover/Action | Obnovení webové aplikace. |
> | Akce | Microsoft.Web/sites/resetSlotConfig/Action | Resetování konfigurace webové aplikace |
> | Akce | Microsoft.Web/Sites/resourcehealthmetadata/Read | Získáte Metadata stavu webové aplikace prostředku. |
> | Akce | Microsoft.Web/sites/restart/Action | Restartujte webovou aplikaci |
> | Akce | Microsoft.Web/Sites/Restore/Read | Získejte obnovení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Restore/Write | Obnovení webové aplikace. |
> | Akce | Microsoft.Web/Sites/restorefrombackupblob/Action | Obnovení webové aplikace ze záložního objektu Blob. |
> | Akce | Microsoft.Web/Sites/restorefromdeletedwebapp/Action | Obnovení webové aplikace z aplikace odstranil. |
> | Akce | Microsoft.Web/Sites/restoresnapshot/Action | Obnovení webové aplikace snímky. |
> | Akce | Microsoft.Web/Sites/siteextensions/DELETE | Odstraňte rozšíření webové aplikace webu. |
> | Akce | Microsoft.Web/Sites/siteextensions/Read | Získáte rozšíření webové aplikace webu. |
> | Akce | Microsoft.Web/Sites/siteextensions/Write | Aktualizujte rozšíření webové aplikace webu. |
> | Akce | Microsoft.Web/Sites/slots/analyzecustomhostname/Read | Získejte Web Apps sloty analyzovat vlastní název hostitele. |
> | Akce | Microsoft.Web/sites/slots/applySlotConfig/Action | Použití konfigurace webové aplikace slotu z cílového slotu na aktuální pozici. |
> | Akce | Microsoft.Web/sites/slots/backup/Action | Vytvořte novou zálohu Slot webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Backup/Read | Získání slotů zálohy Web Apps. |
> | Akce | Microsoft.Web/Sites/slots/Backup/Write | Aktualizace zálohy Web Apps sloty. |
> | Akce | Microsoft.Web/Sites/slots/backups/Action | Zjistěte zálohy sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/backups/DELETE | Odstraňte zálohy sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/backups/list/Action | Sloty zálohy seznamu Web Apps. |
> | Akce | Microsoft.Web/sites/slots/backups/Read | Získá vlastnosti zálohování se sloty webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/backups/Restore/Action | Obnovte zálohy Web Apps sloty. |
> | Akce | Microsoft.Web/Sites/slots/config/DELETE | Odstraňte konfiguraci sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/config/list/Action | Seznam Slot webové aplikace citlivá nastavení zabezpečení, jako je například publikování přihlašovací údaje, nastavení aplikace a připojovacích řetězců |
> | Akce | Microsoft.Web/sites/slots/config/Read | Získat nastavení konfigurace pro Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/config/Write | Aktualizovat nastavení konfigurace pro Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/containerlogs/Action | Získat ZIP protokoly kontejneru pro Slot webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/DELETE | Odstranění webové aplikace sloty průběžné webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/Read | Získejte Web Apps sloty průběžné webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/Start/Action | Spuštění webové aplikace sloty průběžné webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/stop/Action | Zastavení webové aplikace sloty průběžné webové úlohy. |
> | Akce | Microsoft.Web/sites/slots/Delete | Odstranění existujícího slotu webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Deployments/DELETE | Odstranění webové aplikace sloty nasazení. |
> | Akce | Microsoft.Web/Sites/slots/Deployments/log/Read | Získáte webové aplikace sloty nasazení protokol. |
> | Akce | Microsoft.Web/Sites/slots/Deployments/Read | Získání nasazení sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Deployments/Write | Webové aplikace sloty nasazení aktualizací. |
> | Akce | Microsoft.Web/Sites/slots/detectors/Read | Získejte detektory sloty webové aplikace. |
> | Akce | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Spuštění webové aplikace sloty diagnostiky analýzy. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/analyses/Read | Získejte Web Apps sloty diagnostiky analýzy. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/aspnetcore/Read | Získání diagnostiky sloty webové aplikace pro aplikace ASP.NET Core. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/autoheal/Read | Získejte Web Apps sloty diagnostiky Autoheal. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/Deployment/Read | Získání nasazení diagnostiky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/Deployments/Read | Získá webové aplikace sloty diagnostiky nasazení. |
> | Akce | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Spuštění webové aplikace sloty diagnostiky detektoru. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/detectors/Read | Získejte Web Apps sloty diagnostiky detektoru. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/frebanalysis/Read | Získejte Web Apps sloty diagnostiky FREB analýzy. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/loganalyzer/Read | Získejte webové aplikace sloty diagnostiky protokolu analyzátoru. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/Read | Získání diagnostiky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/runtimeavailability/Read | Získejte Web Apps sloty diagnostiky běhovou dostupnost. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/servicehealth/Read | Získání stavu služby webových aplikací sloty diagnostiky. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitecpuanalysis/Read | Získejte analýza webu procesoru sloty Diagnostika webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitecrashes/Read | Získejte webové aplikace sloty diagnostiky webu chyb. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitelatency/Read | Získejte webové aplikace sloty diagnostiky webu latence. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitememoryanalysis/Read | Získejte analýza webu paměti sloty diagnostiky webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/siterestartsettingupdate/Read | Získejte aktualizace nastavení restartování Diagnostika webové aplikace sloty webu. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/siterestartuserinitiated/Read | Získejte webové aplikace sloty diagnostiky webu restartování iniciované uživatelem. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/siteswap/Read | Získejte aktualizace diagnostiky webu prohození webové aplikace sloty. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/ThreadCount/Read | Získáte počet vláken pro Diagnostika webové aplikace sloty. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Získejte Web Apps sloty diagnostiky Workeravailability. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/workerprocessrecycle/Read | Získejte recyklace pracovního procesu procesu sloty Diagnostika webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/domainownershipidentifiers/Read | Získejte Web Apps sloty domény vlastnictví identifikátory. |
> | Akce | Microsoft.Web/Sites/slots/Functions/Read | Získáte funkce sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hostnamebindings/DELETE | Odstranění vazby názvu hostitele sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hostnamebindings/Read | Získá vazby názvu hostitele sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hostnamebindings/Write | Aktualizujte vazby názvu hostitele sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnection/DELETE | Odstraňte hybridní připojení. sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnection/Read | Získejte hybridní připojení. sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnection/Write | Aktualizace webové aplikace sloty hybridní připojení. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/DELETE | Odstranění webové aplikace sloty hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/Write | Aktualizace webové aplikace sloty hybridní připojení obory názvů Relay. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnectionrelays/Read | Získejte předávací službu webové aplikace sloty hybridní připojení. |
> | Akce | Microsoft.Web/Sites/slots/instances/Deployments/Read | Získá webové aplikace sloty instance nasazení. |
> | Akce | Microsoft.Web/Sites/slots/instances/Processes/DELETE | Odstranění webové aplikace sloty instancí procesů. |
> | Akce | Microsoft.Web/Sites/slots/instances/Processes/Read | Načíst webové aplikace sloty instance procesy. |
> | Akce | Microsoft.Web/Sites/slots/instances/Read | Získání instance sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/metricdefinitions/Read | Získáte definice metriky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Metrics/Read | Získáte metriky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/migratemysql/Read | Získejte Web Apps sloty migraci MySql. |
> | Akce | Microsoft.Web/Sites/slots/networktrace/Action | Sloty síťové trasování webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/newpassword/Action | Sloty Newpassword webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/operationresults/Read | Načte výsledky operace sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Operations/Read | Získání operací sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/perfcounters/Read | Získáte čítače výkonu sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/phplogging/Read | Získejte Phplogging sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/premieraddons/DELETE | Odstranění webové aplikace sloty Premier doplňky. |
> | Akce | Microsoft.Web/Sites/slots/premieraddons/Read | Získejte Web Apps sloty Premier doplňky. |
> | Akce | Microsoft.Web/Sites/slots/premieraddons/Write | Aktualizace webové aplikace sloty Premier doplňků. |
> | Akce | Microsoft.Web/Sites/slots/publiccertificates/DELETE | Odstraňte veřejné certifikáty sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/publiccertificates/Read | Získáte veřejné certifikáty sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/publiccertificates/Write | Vytvořit nebo aktualizovat veřejné certifikáty sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/publish/Action | Publikování Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/publishxml/Action | Získání souboru xml profilu publikování pro Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/Read | Získá vlastnosti slotu nasazení webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Recover/Action | Obnovte sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/resetSlotConfig/Action | Resetování konfigurace pro slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/resourcehealthmetadata/Read | Získáte webové aplikace sloty Resource Health Metadata. |
> | Akce | Microsoft.Web/sites/slots/restart/Action | Restartujte Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Restore/Read | Získejte obnovení sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Restore/Write | Obnovte sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/restorefrombackupblob/Action | Obnovení ze záložního objektu Blob Slot webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/restorefromdeletedwebapp/Action | Obnovení z aplikace odstranil sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/restoresnapshot/Action | Obnovte snímky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/siteextensions/DELETE | Odstraňte rozšíření webové aplikace sloty webu. |
> | Akce | Microsoft.Web/Sites/slots/siteextensions/Read | Získáte rozšíření webu sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/siteextensions/Write | Aktualizujte rozšíření webu sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/slotsdiffs/Action | Získat rozdíly v konfiguraci webové aplikace a sloty |
> | Akce | Microsoft.Web/sites/slots/slotsswap/Action | Prohození slotů nasazení webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/snapshots/Read | Získáte snímky sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Delete | Odstranění nastavení konfigurace správy zdrojového kódu Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Read | Získat nastavení konfigurace správy zdrojového kódu Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Write | Aktualizovat nastavení konfigurace správy zdrojového kódu Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/start/Action | Spustit Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/stop/Action | Zastavit Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Sync/Action | Sloty webové aplikace synchronizace. |
> | Akce | Microsoft.Web/Sites/slots/triggeredwebjobs/DELETE | Odstraňte aktivovaných WebJobs sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/triggeredwebjobs/Read | Získání aktivovaných WebJobs sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/triggeredwebjobs/Run/Action | Sloty spuštění webové aplikace aktivované webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/usages/Read | Získáte použití sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/DELETE | Odstraňte připojení virtuální sítě sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/gateways/Write | Aktualizace brány připojení webové aplikace sloty virtuální sítě. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/Read | Získáte připojení virtuální sítě sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/Write | Aktualizujte připojení virtuální sítě sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/webjobs/Read | Načtení webových úloh sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/Write | Vytvořit nový Slot webové aplikace nebo aktualizuje stávající |
> | Akce | Microsoft.Web/sites/slotsdiffs/Action | Získat rozdíly v konfiguraci webové aplikace a sloty |
> | Akce | Microsoft.Web/sites/slotsswap/Action | Prohození slotů nasazení webové aplikace |
> | Akce | Microsoft.Web/Sites/snapshots/Read | Získáte snímky webové aplikace. |
> | Akce | Microsoft.Web/sites/sourcecontrols/Delete | Odstranění nastavení konfigurace správy zdrojového kódu webové aplikace |
> | Akce | Microsoft.Web/sites/sourcecontrols/Read | Získat nastavení konfigurace správy zdrojového kódu webové aplikace |
> | Akce | Microsoft.Web/sites/sourcecontrols/Write | Aktualizovat nastavení konfigurace správy zdrojového kódu webové aplikace |
> | Akce | Microsoft.Web/sites/start/Action | Spustí webovou aplikaci |
> | Akce | Microsoft.Web/sites/stop/Action | Zastavit webovou aplikaci |
> | Akce | Microsoft.Web/Sites/Sync/Action | Synchronizace webové aplikace. |
> | Akce | Microsoft.Web/Sites/syncfunctiontriggers/Action | Synchronizace aktivační události funkcí pro webové aplikace. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Odstranění webové aplikace aktivované WebJobs. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/history/Read | Zobrazit historii aktivované webové úlohy webové aplikace. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/Read | Získání aktivovaných webových úloh webové aplikace. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Spuštění webové aplikace aktivované webové úlohy. |
> | Akce | Microsoft.Web/Sites/usages/Read | Získáte použití webových aplikací. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/DELETE | Odstraňte připojení virtuální sítě webových aplikací. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/gateways/Read | Získání brány připojení virtuální sítě webových aplikací. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/gateways/Write | Aktualizace webové aplikace virtuální síť připojení brány. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/Read | Získáte připojení virtuální sítě webových aplikací. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/Write | Aktualizujte připojení virtuální sítě webových aplikací. |
> | Akce | Microsoft.Web/Sites/webjobs/Read | Načíst webové úlohy webové aplikace. |
> | Akce | Microsoft.Web/sites/Write | Vytvořit novou webovou aplikaci nebo aktualizaci existující |
> | Akce | Microsoft.Web/skus/Read | Získáte položky SKU. |
> | Akce | Microsoft.Web/sourcecontrols/Read | Získáte ovládací prvky zdroje. |
> | Akce | Microsoft.Web/sourcecontrols/Write | Aktualizujte ovládací prvky zdroje. |
> | Akce | Microsoft.Web/unregister/Action | Zrušit registraci poskytovatele prostředků Microsoft.Web pro předplatné. |
> | Akce | Microsoft.Web/Validate/Action | Ověření. |
> | Akce | Microsoft.Web/verifyhostingenvironmentvnet/Action | Ověřte hostování prostředí Vnet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.WorkloadMonitor/components/read | Získá součásti pro prostředek |
> | Akce | Microsoft.WorkloadMonitor/componentsSummary/read | Získá seznam komponent |
> | Akce | Microsoft.WorkloadMonitor/monitorInstances/read | Získá instance monitorování pro prostředek |
> | Akce | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Získá souhrnné monitorování instancí |
> | Akce | Microsoft.WorkloadMonitor/monitors/read | Získá monitorování pro prostředek |
> | Akce | Microsoft.WorkloadMonitor/monitors/write | Konfigurace monitorování pro prostředek |
> | Akce | Microsoft.WorkloadMonitor/notificationSettings/read | Získá nastavení oznámení pro prostředek |
> | Akce | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurace nastavení oznámení pro prostředek |
> | Akce | Microsoft.WorkloadMonitor/operations/read | Získá podporované operace |

## <a name="next-steps"></a>Další postup

- [Vlastní role](custom-roles.md)
- [Předdefinované role](built-in-roles.md)
