---
title: Operace zprostředkovatele prostředků v Azure Resource Manager | Microsoft Docs
description: Seznam operací dostupných pro poskytovatele prostředků Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b45295b76b7fe15f1ee6a1feff7052a2b8262476
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267539"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operace zprostředkovatele prostředků v Azure Resource Manager

Tento článek obsahuje seznam operací dostupných pro každý poskytovatel prostředků Azure Resource Manager. Tyto operace lze použít v [vlastní role](custom-roles.md) k poskytování granulární [řízení přístupu na základě role (RBAC)](overview.md) k prostředkům v Azure. Operace řetězce mít následující formát: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Operace poskytovatele prostředků se vždy vyvíjejí. Chcete-li získat nejnovější operace, použijte [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) nebo [seznam operací zprostředkovatele az](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AAD/domainServices/delete | Deletes Domain Services. |
> | Akce | Microsoft.AAD/domainServices/read | Reads Domain Services. |
> | Akce | Microsoft.AAD/domainServices/write | Write Domain Services |
> | Akce | Microsoft.AAD/locations/operationresults/read | Načíst stav asynchronní operace. |
> | Akce | Microsoft.AAD/Operations/read | Lokalizované popisný název pro operaci, protože by měl být zobrazí uživateli. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.aadiam/diagnosticsettings/DELETE | Odstraňuje se nastavení diagnostiky |
> | Akce | Microsoft.aadiam/diagnosticsettings/Read | Načítání nastavení diagnostiky |
> | Akce | Microsoft.aadiam/diagnosticsettings/Write | Zápis nastavení diagnostiky |
> | Akce | Microsoft.aadiam/diagnosticsettingscategories/Read | Načítání nastavení diagnostiky kategorií |
> | Akce | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro klienty |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Addons/operations/read | Získá podporované RP operace. |
> | Akce | Microsoft.Addons/register/action | Zaregistrovat určený odběr Microsoft.Addons |
> | Akce | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Zobrazí aktuální informace plán podpory pro zadaný odběr. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Odebere plán zadaný kanonický podpory |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/read | Zjištění stavu plán zadaný kanonický podpory. |
> | Akce | Microsoft.Addons/supportProviders/supportPlanTypes/write | Přidá zadaný typ plán podpory kanonickém tvaru. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ADHybridHealthService/addsservices/action | Vytvořte novou doménovou strukturu pro klienta. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Získá všechny servery pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/alerts/read | Získá podrobnosti výstrahy pro doménové struktury jako alertid výstrahy vyvolané datum, poslední nalezen, výstrahy popis výstrahy, poslední aktualizované, výstrahy úrovně, výstrahy stavu, výstrahy, řešení potíží s odkazy atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/configuration/read | Získá konfiguraci služby pro doménovou strukturu. Příklad - název doménové struktury, Functionla úrovni, názvy domény role FSMO hlavního serveru, role FSMO hlavního serveru schémat atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/delete | Odstraní služby a jeho je servery spolu se data o stavu. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Získá podrobnosti domén a sítí pro doménovou strukturu. Příklad-stav, aktivní výstrahy vyřešit výstrahy, jako je úroveň funkčnosti doménové struktury, hlavní server infrastruktury, primárního řadiče domény, vlastnosti atd hlavního serveru RID.  |
> | Akce | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Získá nastavení předvoleb uživatele pro doménovou strukturu.<br>Příklad - MetricCounterName ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Nastavení pro atd grafy uživatelského rozhraní. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Získá souhrnné doménové struktury pro dané doménové struktuře jako název doménové struktury, počet domén v rámci této doménové struktury, počet lokalit a podrobnosti lokality atd. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Získá seznam podporovaných metriky pro danou službu.<br>Pro příklad extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby AD FS atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový pro ADDomainService.<br>Spuštění profilu latence, navázat připojení TCP, Statistika agenta soukromých bajtů, statistiky exportu do služby Azure AD pro ADSync service. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | S ohledem služby, toto rozhraní API získá informace o metrikách.<br>Toto rozhraní API můžete například použít k získání informace týkající se: extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby ADFederation atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový ADDomain služby.<br>Spustit profil latence, připojení TCP, Statistika agenta soukromých bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Toto rozhraní API získá seznam všech zařazený nemá ADDomainServices pro klienta premium. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/read | Získá podrobnosti ze služby pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Získá podrobnosti k replikaci pro všechny servery pro zadaný název služby. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Získá počet řadičů domény a jejich chyby replikace, pokud existuje. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Získá dokončit seznamu řadiče domény spolu s replikace podrobnosti dané doménové struktuře. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Přidání instance serveru ke službě. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Během registrace serveru ADDomainService se nazývá toto rozhraní api získat přihlašovací údaje pro registraci nové servery. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Odstraní server pro danou službu a klienta. |
> | Akce | Microsoft.ADHybridHealthService/addsservices/write | Vytvoří nebo aktualizuje instanci ADDomainService pro klienta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/action | Konfigurace aktualizací klienta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/read | Načte konfiguraci klienta. |
> | Akce | Microsoft.ADHybridHealthService/configuration/write | Vytvoří konfiguraci klienta. |
> | Akce | Microsoft.ADHybridHealthService/logs/contents/read | Získá obsah uložený v objektu blob protokoly registrace a instalace agenta. |
> | Akce | Microsoft.ADHybridHealthService/logs/read | Získá protokoly registrace a instalace agenta pro klienta. |
> | Akce | Microsoft.ADHybridHealthService/operations/read | Získá seznam operací systémem podporována. |
> | Akce | Microsoft.ADHybridHealthService/register/action | Registruje zprostředkovatele prostředků ADHybrid stavu služby a povolí vytvoření prostředku ADHybrid Health Service. |
> | Akce | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Získá seznam dostupných oblastí, používá DevOps pro podporu incidentů zákazníka. |
> | Akce | Microsoft.ADHybridHealthService/reports/badpassword/read | Získá seznam pokusů o nesprávné heslo pro všechny uživatele v Active Directory Federation Service. |
> | Akce | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Získá identifikátor URI SAS objektu Blob obsahující stav a případné výsledek nově zařazených do fronty sestavy úlohy frekvence chybné uživatelské jméno a heslo pokusí za UserId za IPAddress na každý den pro daného klienta. |
> | Akce | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Získá seznam DevOps souhlasu ze strany klientů. Obvykle se používá pro zákaznickou podporu. |
> | Akce | Microsoft.ADHybridHealthService/reports/isdevops/read | Získá hodnotu, která určuje, že uvedenými povětrnostními teannt se dá souhlas DevOps nebo ne. |
> | Akce | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Userid(objectid) aktualizací pro vybrané dev ops klienta. |
> | Akce | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Získá vybrané nasazení pro daného klienta. |
> | Akce | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Zadané id klienta získá umístění úložiště klienta. |
> | Akce | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Získá geografické umístění, ze kterého budou mít přístup data. |
> | Akce | Microsoft.ADHybridHealthService/services/action | Aktualizace instance služby v klientovi. |
> | Akce | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrahy pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/alerts/read | Přečte výstrahy pro službu. |
> | Akce | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Dané funkce název ověřuje, pokud služba obsahuje všechno, co k použití této funkce vyžaduje. |
> | Akce | Microsoft.ADHybridHealthService/services/delete | Odstraní instanci služby v klientovi. |
> | Akce | Microsoft.ADHybridHealthService/services/exporterrors/read | Získá chyby exportu pro danou synchronizační služba. |
> | Akce | Microsoft.ADHybridHealthService/services/exportstatus/read | Získá stav exportu pro danou službu. |
> | Akce | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Získá výstrahy zpětnou vazbu pro danou službu a serveru. |
> | Akce | Microsoft.ADHybridHealthService/services/metricmetadata/read | Získá seznam podporovaných metriky pro danou službu.<br>Pro příklad extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby AD FS atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový pro ADDomainService.<br>Spuštění profilu latence, navázat připojení TCP, Statistika agenta soukromých bajtů, statistiky exportu do služby Azure AD pro ADSync service. |
> | Akce | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Toto rozhraní API s ohledem služby, získá průměr metrik pro danou službu.<br>Toto rozhraní API můžete například použít k získání informace týkající se: extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby ADFederation atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový ADDomain služby.<br>Spustit profil latence, připojení TCP, Statistika agenta soukromých bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/metrics/groups/read | S ohledem služby, toto rozhraní API získá informace o metrikách.<br>Toto rozhraní API můžete například použít k získání informace týkající se: extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby ADFederation atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový ADDomain služby.<br>Spustit profil latence, připojení TCP, Statistika agenta soukromých bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Toto rozhraní API s ohledem služby, získá agregovaná zobrazení pro metriky pro danou službu.<br>Toto rozhraní API můžete například použít k získání informace týkající se: extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby ADFederation atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový ADDomain služby.<br>Spustit profil latence, připojení TCP, Statistika agenta soukromých bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Přidat nebo aktualizovat konfiguraci monitorování pro služby. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Získá konfiguraci monitorování pro danou službu. |
> | Akce | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Přidání nebo aktualizace konfigurací monitorování pro služby. |
> | Akce | Microsoft.ADHybridHealthService/services/premiumcheck/read | Toto rozhraní API získá seznam všech služeb zařazený nemá pro klienta premium. |
> | Akce | Microsoft.ADHybridHealthService/services/read | Přečte instance služby v klientovi. |
> | Akce | Microsoft.ADHybridHealthService/services/reports/details/read | Sestava získá prvních 50 uživatelů s chybami nesprávné heslo z posledních 7 dnů |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/action | Vytvoří instanci serveru v rámci služby. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Přečte výstrahy pro server. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Během registrace serveru se nazývá toto rozhraní api získat přihlašovací údaje pro registraci nové servery. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Toto rozhraní API pro daný server, získá seznam datových typů, které odesílání servery a čas posledního pro každý odeslání. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/delete | Odstraní instanci serveru v rámci služby. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Získá podrobnosti o chybě exportovat synchronizace pro danou službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | S ohledem služby, toto rozhraní API získá informace o metrikách.<br>Toto rozhraní API můžete například použít k získání informace týkající se: extranetu uzamčení účtu, celkový počet neúspěšných požadavků, nevyřízené žádosti o tokeny (Proxy), za sekundu žádosti o tokeny služby ADFederation atd.<br>Ověřování NTLM za sekundu, vazby LDAP úspěšné/s, čas vazby LDAP, aktivních podprocesů LDAP, ověřování protokolu Kerberos za sekundu atd ATQ vláken celkový ADDomain služby.<br>Spustit profil latence, připojení TCP, Statistika agenta soukromých bajtů, exportujte statistiky do služby Azure AD pro službu synchronizace. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/read | Přečte instanci serveru v rámci služby. |
> | Akce | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Získá konfiguraci služby pro daného klienta. |
> | Akce | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Získá stav funkce povolených pro daného klienta. |
> | Akce | Microsoft.ADHybridHealthService/services/write | Vytvoří instanci služby v klientovi. |
> | Akce | Microsoft.ADHybridHealthService/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků služby ADHybrid stavu. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Advisor/configurations/read | Získání konfigurace |
> | Akce | Microsoft.Advisor/configurations/write | Vytvoření nebo aktualizace konfigurace |
> | Akce | Microsoft.Advisor/generateRecommendations/action | Vygeneruje doporučení |
> | Akce | Microsoft.Advisor/generateRecommendations/read | Získá generovat stav doporučení |
> | Akce | Microsoft.Advisor/operations/read | Získá operací pro Microsoft Advisor |
> | Akce | Microsoft.Advisor/recommendations/read | Přečte doporučení |
> | Akce | Microsoft.Advisor/recommendations/suppressions/delete | Odstraní potlačení |
> | Akce | Microsoft.Advisor/recommendations/suppressions/read | Získá suppressions |
> | Akce | Microsoft.Advisor/recommendations/suppressions/write | Vytvoření nebo aktualizace suppressions |
> | Akce | Microsoft.Advisor/register/action | Zaregistruje předplatné pro Microsoft Advisor |
> | Akce | Microsoft.Advisor/suppressions/delete | Odstraní potlačení |
> | Akce | Microsoft.Advisor/suppressions/read | Získá suppressions |
> | Akce | Microsoft.Advisor/suppressions/write | Vytvoření nebo aktualizace suppressions |
> | Akce | Microsoft.Advisor/unregister/action | Zrušení registrace předplatného pro Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AlertsManagement/alerts/changestate/action | Změňte stav výstrahy. |
> | Akce | Microsoft.AlertsManagement/alerts/read | Získáte všechny výstrahy pro vstupní filtry. |
> | Akce | Microsoft.AlertsManagement/alertsSummary/read | Získejte souhrn výstrah |
> | Akce | Microsoft.AlertsManagement/Operations/read | Přečte Zadaná operace |
> | Akce | Microsoft.AlertsManagement/smartGroups/changestate/action | Ke změně stavu inteligentní skupiny |
> | Akce | Microsoft.AlertsManagement/smartGroups/read | Získání všech inteligentní skupin pro vstupní filtry |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Kontroluje, zda zadaný Analysis Server je platný název a ne v používání. |
> | Akce | Microsoft.AnalysisServices/locations/operationresults/read | Načte informace o zadanou operaci výsledku. |
> | Akce | Microsoft.AnalysisServices/locations/operationstatuses/read | Načte informace o stavu Zadaná operace. |
> | Akce | Microsoft.AnalysisServices/operations/read | Načte informace o operací |
> | Akce | Microsoft.AnalysisServices/register/action | Registruje zprostředkovatele prostředků služby Analysis Services. |
> | Akce | Microsoft.AnalysisServices/servers/delete | Odstraní Analysis serveru. |
> | Akce | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Zobrazí seznam stav brány přidružené k serveru. |
> | Akce | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro Analysis serveru |
> | Akce | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro Analysis serveru |
> | Akce | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro servery |
> | Akce | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro Analysis serveru |
> | Akce | Microsoft.AnalysisServices/servers/read | Načte informace zadaný Analysis serveru. |
> | Akce | Microsoft.AnalysisServices/servers/resume/action | Obnoví Analysis serveru. |
> | Akce | Microsoft.AnalysisServices/servers/skus/read | Načíst dostupné informace o SKU pro server |
> | Akce | Microsoft.AnalysisServices/servers/suspend/action | Pozastaví Analysis serveru. |
> | Akce | Microsoft.AnalysisServices/servers/write | Vytvoří nebo aktualizuje zadaný Analysis serveru. |
> | Akce | Microsoft.AnalysisServices/skus/read | Načte informace o SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ApiManagement/checkNameAvailability/read | Ověří, zda za předpokladu, že název služby je k dispozici |
> | Akce | Microsoft.ApiManagement/operations/read | Číst všechny operace rozhraní API k dispozici pro prostředek Microsoft.ApiManagement |
> | Akce | Microsoft.ApiManagement/register/action | Registrace předplatného pro poskytovatele prostředků Microsoft.ApiManagement |
> | Akce | Microsoft.ApiManagement/reports/read | Získáte sestavy agregaci časová období, geografické oblasti, vývojáři, produktů, rozhraní API, operace, předplatné a byRequest. |
> | Akce | Microsoft.ApiManagement/service/apis/delete | Odeberte existující rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/delete | Odeberte existující Diagnostika |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Odeberte mapování protokolovacího nástroje se nastavení diagnostiky |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Získání seznamu existující diagnostiky protokolovacích nástrojů |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Mapování protokolovacího nástroje na nastavení diagnostiky |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/read | Získání seznamu diagnostiky nebo získáte podrobnosti diagnostiky |
> | Akce | Microsoft.ApiManagement/service/apis/diagnostics/write | Přidat nové diagnostiky nebo aktualizovat existující diagnostické informace |
> | Akce | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Odebere existující přílohy |
> | Akce | Microsoft.ApiManagement/service/apis/issues/attachments/read | Podrobnosti přílohy získání přílohy problém nebo problém získá API Management |
> | Akce | Microsoft.ApiManagement/service/apis/issues/attachments/write | Přidat přílohu problém rozhraní api |
> | Akce | Microsoft.ApiManagement/service/apis/issues/comments/delete | Odebere existující komentář |
> | Akce | Microsoft.ApiManagement/service/apis/issues/comments/read | Získá komentáře nebo získá API Management problém komentář podrobnosti o problému |
> | Akce | Microsoft.ApiManagement/service/apis/issues/comments/write | Přidejte komentář problém rozhraní api |
> | Akce | Microsoft.ApiManagement/service/apis/issues/delete | Odebere existující problém |
> | Akce | Microsoft.ApiManagement/service/apis/issues/read | Získat problémů, souvisejících s podrobnostmi o problém rozhraní API nebo získá API Management |
> | Akce | Microsoft.ApiManagement/service/apis/issues/write | Přidání rozhraní api problém nebo aktualizovat problém rozhraní api |
> | Akce | Microsoft.ApiManagement/service/apis/operations/delete | Odeberte existující operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policies/delete | Odebrat konfiguraci zásad ze zásad operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policies/read | Získat zásady pro operace rozhraní API nebo podrobnosti o konfiguraci zásad Get pro operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policies/write | Nastavit podrobnosti konfigurace zásady pro operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policy/delete | Odebrat konfiguraci zásad z operace |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policy/read | Získat podrobnosti o konfiguraci zásad pro operaci |
> | Akce | Microsoft.ApiManagement/service/apis/operations/policy/write | Nastavení zásad konfigurace podrobnosti operace |
> | Akce | Microsoft.ApiManagement/service/apis/operations/read | Získání seznamu existující operace rozhraní API nebo získat podrobnosti operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/delete | Odstraňte přidružení stávající značky s existující operace |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/read | Získat značky přidružené podrobnosti operace nebo získat značky |
> | Akce | Microsoft.ApiManagement/service/apis/operations/tags/write | Přidružení stávající značky k existující operace |
> | Akce | Microsoft.ApiManagement/service/apis/operations/write | Operaci nového rozhraní API umožňuje vytvořit nebo aktualizovat existující operace rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/operationsByTags/read | Získejte seznam přidružení operaci nebo značky |
> | Akce | Microsoft.ApiManagement/service/apis/policies/delete | Odebrat konfiguraci zásad ze zásad rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policies/read | Získat zásady rozhraní API nebo Get zásad podrobnosti o konfiguraci pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policies/write | Podrobnosti o konfiguraci zásad nastavení pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policy/delete | Odebrat konfiguraci zásad z rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policy/read | Získat podrobnosti o konfiguraci zásad pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/policy/write | Podrobnosti o konfiguraci zásad nastavení pro rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/products/read | Získat všechny produkty, které je součástí rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/read | Získání seznamu všech registrované rozhraní API nebo Get podrobné informace o rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/releases/delete | Odebere všechny verze rozhraní API nebo odebrat rozhraní API verze |
> | Akce | Microsoft.ApiManagement/service/apis/releases/read | Získání verze podrobnosti rozhraní API nebo Get reelase rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/releases/write | Vytvořit novou verzi rozhraní API nebo aktualizovat existující verzi rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/revisions/delete | Odebere všechny revize rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/revisions/read | Získat revize patřící do rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/delete | Odebere existující schéma |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/document/read | Získat dokument popisující schématu |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/document/write | Aktualizace dokumentace popisující schématu |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/read | Získá všechny schémata pro dané rozhraní API nebo získá schémata používá rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/schemas/write | Nastaví schémata používá rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Odebrání značky popis rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Získání značek popisů v rozsahu tohoto rozhraní API nebo získat značky popis v rozsahu tohoto rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Vytvořit nebo změnit značky popis v oboru rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apis/tags/delete | Odeberte stávající přidružení rozhraní API nebo značky |
> | Akce | Microsoft.ApiManagement/service/apis/tags/read | Získání všech rozhraní API nebo značky přidružení pro rozhraní API nebo Get podrobnosti o rozhraní API nebo značky přidružení |
> | Akce | Microsoft.ApiManagement/service/apis/tags/write | Přidání nového rozhraní API nebo značky přidružení |
> | Akce | Microsoft.ApiManagement/service/apis/write | Nové rozhraní API umožňuje vytvořit nebo aktualizovat existující podrobnosti o rozhraní API |
> | Akce | Microsoft.ApiManagement/service/apisByTags/read | Získejte seznam přidružení rozhraní API nebo značky |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/delete | Odeberte existující VersionSet |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/read | Získání seznamu verze skupiny entit nebo získá podrobnosti VersionSet |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/versions/read | Získání seznamu verze entit |
> | Akce | Microsoft.ApiManagement/service/api-version-sets/write | Vytvořit nový VersionSet nebo aktualizovat existující VersionSet podrobnosti |
> | Akce | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualizuje Microsoft.ApiManagement prostředky spuštěna ve virtuální síti a vyberte aktualizované nastavení sítě. |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/delete | Odeberte existující serveru ověřování |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/read | Získat seznam serverů autorizace nebo získat podrobnosti o serveru ověřování |
> | Akce | Microsoft.ApiManagement/service/authorizationServers/write | Vytvořit nový server autorizace nebo podrobné informace o aktualizaci existujícího serveru autorizace |
> | Akce | Microsoft.ApiManagement/service/backends/delete | Odeberte existující back-end |
> | Akce | Microsoft.ApiManagement/service/backends/read | Získání seznamu back-EndY nebo získat podrobnosti o back-end |
> | Akce | Microsoft.ApiManagement/service/backends/reconnect/action | Vytvořit žádost o opětovné připojení |
> | Akce | Microsoft.ApiManagement/service/backends/write | Přidat nový back-end nebo aktualizovat existující podrobnosti back-end |
> | Akce | Microsoft.ApiManagement/service/backup/action | Zálohování službu správy rozhraní API a zadaného kontejneru. v uživatel zadaný účet úložiště |
> | Akce | Microsoft.ApiManagement/service/certificates/delete | Odebrat existující certifikát |
> | Akce | Microsoft.ApiManagement/service/certificates/read | Získání seznamu certifikátů nebo získat podrobnosti o certifikát |
> | Akce | Microsoft.ApiManagement/service/certificates/write | Přidat nový certifikát |
> | Akce | Microsoft.ApiManagement/service/delete | Odstranění instance služby API Management |
> | Akce | Microsoft.ApiManagement/service/diagnostics/delete | Odeberte existující Diagnostika |
> | Akce | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Odeberte mapování protokolovacího nástroje se nastavení diagnostiky |
> | Akce | Microsoft.ApiManagement/service/diagnostics/loggers/read | Získání seznamu existující diagnostiky protokolovacích nástrojů |
> | Akce | Microsoft.ApiManagement/service/diagnostics/loggers/write | Mapování protokolovacího nástroje na nastavení diagnostiky |
> | Akce | Microsoft.ApiManagement/service/diagnostics/read | Získání seznamu diagnostiky nebo získáte podrobnosti diagnostiky |
> | Akce | Microsoft.ApiManagement/service/diagnostics/write | Přidat nové diagnostiky nebo aktualizovat existující diagnostické informace |
> | Akce | Microsoft.ApiManagement/service/getssotoken/action | Získá token jednotného přihlašování, který lze použít k přihlášení na portál starší verze služby API Management jako správce |
> | Akce | Microsoft.ApiManagement/service/groups/delete | Odeberte existující skupinu |
> | Akce | Microsoft.ApiManagement/service/groups/read | Získání seznamu skupin nebo získá podrobnosti skupiny |
> | Akce | Microsoft.ApiManagement/service/groups/users/delete | Odebrat existující uživatele z existující skupiny |
> | Akce | Microsoft.ApiManagement/service/groups/users/read | Získání seznamu skupin uživatelů |
> | Akce | Microsoft.ApiManagement/service/groups/users/write | Přidat stávajícího uživatele do stávající skupiny |
> | Akce | Microsoft.ApiManagement/service/groups/write | Vytvořit novou skupinu nebo aktualizovat existující skupiny podrobnosti |
> | Akce | Microsoft.ApiManagement/service/identityProviders/delete | Odeberte existující zprostředkovatele Identity |
> | Akce | Microsoft.ApiManagement/service/identityProviders/read | Získání seznamu zprostředkovatelů Identity nebo získáte podrobnosti zprostředkovatele Identity |
> | Akce | Microsoft.ApiManagement/service/identityProviders/write | Vytvořit nový zprostředkovatel Identity nebo aktualizace podrobnosti existujícího poskytovatele Identity |
> | Akce | Microsoft.ApiManagement/service/locations/networkstatus/read | Získá stav přístupu k síti prostředků, na kterých služba závisí na v umístění. |
> | Akce | Microsoft.ApiManagement/service/loggers/delete | Odeberte existující protokolovacího nástroje. |
> | Akce | Microsoft.ApiManagement/service/loggers/read | Získání seznamu protokolovačů nebo získat podrobnosti o protokolovacího nástroje |
> | Akce | Microsoft.ApiManagement/service/loggers/write | Přidat nové protokoly nebo aktualizovat existující podrobnosti protokolovacího nástroje |
> | Akce | Microsoft.ApiManagement/service/managedeployments/action | Změnit SKU nebo jednotky, přidat nebo odebrat místní nasazení služby API Management |
> | Akce | Microsoft.ApiManagement/service/networkstatus/read | Získá stav přístupu k síti prostředků, na kterých služba závisí na. |
> | Akce | Microsoft.ApiManagement/service/notifications/action | Odešle oznámení pro zadaného uživatele |
> | Akce | Microsoft.ApiManagement/service/notifications/read | Získá všechny API Management vydavatele oznámení nebo získat API Management vydavatele podrobnosti oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Odebere existující přidružená oznámení e-mailu |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Získat příjemců e-mailu, které jsou přidružené k rozhraní API správy vydavatele oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Vytvořit nový příjemce e-mailové oznámení. |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Odebere uživatele přidružené k příjemce oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Získat příjemce uživatelé přidružení oznámení. |
> | Akce | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Přidejte uživatele k příjemce oznámení |
> | Akce | Microsoft.ApiManagement/service/notifications/write | Vytvoření nebo aktualizace API Management vydavatele oznámení |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/delete | Odebrat existující OpenID Connect zprostředkovatele |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/read | Získat seznam poskytovatelů OpenID Connect nebo získáte podrobnosti OpenID Connect zprostředkovatele |
> | Akce | Microsoft.ApiManagement/service/openidConnectProviders/write | Vytvořit nové podrobnosti OpenID Connect zprostředkovatele nebo aktualizaci existujícího poskytovatele připojení OpenID |
> | Akce | Microsoft.ApiManagement/service/operationresults/read | Získá aktuální stav operace probíhající dlouhou dobu |
> | Akce | Microsoft.ApiManagement/service/policies/delete | Odebrat konfiguraci zásad ze zásad klienta |
> | Akce | Microsoft.ApiManagement/service/policies/read | Získání zásad klienta nebo Get zásad podrobnosti o konfiguraci pro klienta |
> | Akce | Microsoft.ApiManagement/service/policies/write | Nastavit podrobnosti o konfiguraci zásad pro klienta |
> | Akce | Microsoft.ApiManagement/service/policySnippets/read | Získání všech zásad fragmenty kódu |
> | Akce | Microsoft.ApiManagement/service/portalsettings/read | Získat přihlašovací nastavení pro portál nebo Get přihlašovací nastavení pro tento portál nebo získat nastavení delegování pro portál |
> | Akce | Microsoft.ApiManagement/service/portalsettings/write | Aktualizovat nastavení zaregistrovat nebo nastavení aktualizace zaregistrovat nebo přihlásit k aktualizaci nastavení nebo nastavení aktualizace přihlásit nebo nastavení aktualizovat delegování nebo aktualizovat delegování nastavení |
> | Akce | Microsoft.ApiManagement/service/products/apis/delete | Odeberte existující rozhraní API z existující produktu |
> | Akce | Microsoft.ApiManagement/service/products/apis/read | Získání seznamu rozhraní API, které jsou přidány do existujících produktu |
> | Akce | Microsoft.ApiManagement/service/products/apis/write | Přidání existujícího rozhraní API pro stávající produkt |
> | Akce | Microsoft.ApiManagement/service/products/delete | Odeberte existující produktu |
> | Akce | Microsoft.ApiManagement/service/products/groups/delete | Odstraňte přidružení existující produkt existující skupinu pro vývojáře |
> | Akce | Microsoft.ApiManagement/service/products/groups/read | Získání seznamu skupin vývojáře spojené s produktem |
> | Akce | Microsoft.ApiManagement/service/products/groups/write | Existující produkt přidružit existující skupinu pro vývojáře |
> | Akce | Microsoft.ApiManagement/service/products/policies/delete | Odebrat konfiguraci zásad z produktu zásady |
> | Akce | Microsoft.ApiManagement/service/products/policies/read | Získat zásady produktu nebo Get zásad podrobnosti o konfiguraci produktu |
> | Akce | Microsoft.ApiManagement/service/products/policies/write | Nastavení zásad podrobnosti konfigurace produktu |
> | Akce | Microsoft.ApiManagement/service/products/policy/delete | Odebrat konfiguraci zásad z existující produktu |
> | Akce | Microsoft.ApiManagement/service/products/policy/read | Získání konfigurace zásad existující produktu |
> | Akce | Microsoft.ApiManagement/service/products/policy/write | Nastavení zásad konfigurace pro stávající produkt |
> | Akce | Microsoft.ApiManagement/service/products/read | Získat seznam produktů nebo získat podrobnosti o produktu |
> | Akce | Microsoft.ApiManagement/service/products/subscriptions/read | Získání seznamu předplatných produktu |
> | Akce | Microsoft.ApiManagement/service/products/tags/delete | Odstraňte přidružení stávající značky s existující produktu |
> | Akce | Microsoft.ApiManagement/service/products/tags/read | Získání značek, které jsou přidružené k produktu nebo značka, získat podrobnosti |
> | Akce | Microsoft.ApiManagement/service/products/tags/write | Existující produkt přidružit existující značky |
> | Akce | Microsoft.ApiManagement/service/products/write | Vytvořit nového produktu nebo aktualizovat existující podrobnosti o produktu |
> | Akce | Microsoft.ApiManagement/service/productsByTags/read | Získejte seznam přidružení produktu nebo značky |
> | Akce | Microsoft.ApiManagement/service/properties/delete | Odebere existující vlastnost |
> | Akce | Microsoft.ApiManagement/service/properties/read | Získá seznam všech vlastností nebo získá podrobnosti o zadanou vlastnost |
> | Akce | Microsoft.ApiManagement/service/properties/write | Vytvoří novou vlastnost nebo aktualizuje hodnotu pro zadanou vlastnost |
> | Akce | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro služba API Management |
> | Akce | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro služba API Management |
> | Akce | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly služby API Management |
> | Akce | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro služba API Management |
> | Akce | Microsoft.ApiManagement/service/quotas/periods/read | Získat hodnotu čítače kvóty pro období |
> | Akce | Microsoft.ApiManagement/service/quotas/periods/write | Nastavit aktuální hodnota čítače kvóty |
> | Akce | Microsoft.ApiManagement/service/quotas/read | Získá hodnoty pro kvótu |
> | Akce | Microsoft.ApiManagement/service/quotas/write | Nastavit aktuální hodnota čítače kvóty |
> | Akce | Microsoft.ApiManagement/service/read | Číst metadata pro instanci služby API Management |
> | Akce | Microsoft.ApiManagement/service/reports/read | Získáte sestavy agregovat v časových období nebo sestavě Get agregovat v zeměpisné oblasti nebo sestava Get agregovat vývojáři.<br>nebo získat sestavy agregaci produkty.<br>nebo získat sestavy agregovat v rozhraní API nebo Get sestavu agregaci operace nebo sestavě Get agregovat tímto odběrem.<br>nebo získání žádostí o data pro vytváření sestav |
> | Akce | Microsoft.ApiManagement/service/restore/action | Obnovení ze zadaného kontejneru v uživatel zadaný účet úložiště služby API Management |
> | Akce | Microsoft.ApiManagement/service/subscriptions/delete | Odstraňte odběr. Tato operace slouží k odstranění předplatného |
> | Akce | Microsoft.ApiManagement/service/subscriptions/read | Získat seznam předplatných produktů nebo získat podrobnosti o odběru produktu |
> | Akce | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Znova vygenerovat primární klíč předplatného |
> | Akce | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Znova vygenerovat sekundární klíč předplatného |
> | Akce | Microsoft.ApiManagement/service/subscriptions/write | Přihlášení k odběru stávajícího uživatele k existujícímu produktu nebo aktualizovat existující podrobnosti o předplatném. Tato operace slouží k obnovení odběru |
> | Akce | Microsoft.ApiManagement/service/tagResources/read | Získání seznamu značek přidružených zdrojů. |
> | Akce | Microsoft.ApiManagement/service/tags/delete | Odebrat existující značku |
> | Akce | Microsoft.ApiManagement/service/tags/read | Získání seznamu značek nebo získat podrobnosti značky |
> | Akce | Microsoft.ApiManagement/service/tags/write | Přidat novou značku nebo aktualizovat existující podrobnosti značky |
> | Akce | Microsoft.ApiManagement/service/templates/delete | Obnovit výchozí šablona služby API Management e-mailu |
> | Akce | Microsoft.ApiManagement/service/templates/read | Získá všechny e-mailových šablon nebo získá rozhraní API správy e-mailové šablony podrobnosti |
> | Akce | Microsoft.ApiManagement/service/templates/write | Vytvořit nebo aktualizovat rozhraní API správy e-mailu nebo šablona e-mailu aktualizace API Management |
> | Akce | Microsoft.ApiManagement/service/tenant/delete | Odebrat konfiguraci zásad pro klienta |
> | Akce | Microsoft.ApiManagement/service/tenant/deploy/action | Spustí úlohu nasazení provést změny z větve zadaný git konfigurace v databázi. |
> | Akce | Microsoft.ApiManagement/service/tenant/operationResults/read | Získejte seznam výsledky operace nebo získat výsledek určité operace |
> | Akce | Microsoft.ApiManagement/service/tenant/read | Získat konfiguraci zásad pro klienta nebo klienta Get podrobné informace o přístupu |
> | Akce | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Znova vygenerovat primární přístupový klíč |
> | Akce | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Znova vygenerovat sekundární přístupový klíč |
> | Akce | Microsoft.ApiManagement/service/tenant/save/action | Vytvoří potvrzení s snímku konfigurace pro zadaný větve v úložišti |
> | Akce | Microsoft.ApiManagement/service/tenant/syncState/read | Získat stav poslední synchronizace git |
> | Akce | Microsoft.ApiManagement/service/tenant/validate/action | Ověří změny z větve zadaný git |
> | Akce | Microsoft.ApiManagement/service/tenant/write | Nastavení zásad konfigurace pro klienta nebo podrobné informace o aktualizaci klienta přístup informace |
> | Akce | Microsoft.ApiManagement/service/updatecertificate/action | Nahrajte certifikát SSL pro službu správy rozhraní API |
> | Akce | Microsoft.ApiManagement/service/updatehostname/action | Instalační program, aktualizovat nebo odebrat vlastní názvy domén pro služby API Management |
> | Akce | Microsoft.ApiManagement/service/users/action | Registrace nového uživatele |
> | Akce | Microsoft.ApiManagement/service/users/applications/attachments/delete | Odebere přílohu |
> | Akce | Microsoft.ApiManagement/service/users/applications/attachments/read | Získá aplikaci přílohy nebo získá přílohy |
> | Akce | Microsoft.ApiManagement/service/users/applications/attachments/write | Přidat připojení k aplikaci |
> | Akce | Microsoft.ApiManagement/service/users/applications/delete | Odebere existující aplikace |
> | Akce | Microsoft.ApiManagement/service/users/applications/read | Získání seznamu všechny uživatelské aplikace nebo podrobnosti o aplikace získá API Management |
> | Akce | Microsoft.ApiManagement/service/users/applications/write | Zaregistruje aplikaci API Management nebo aktualizace podrobností o aplikaci |
> | Akce | Microsoft.ApiManagement/service/users/delete | Odebrat uživatelský účet |
> | Akce | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Generování adresy URL jednotné přihlašování. Adresa URL slouží pro přístup k portálu pro správu |
> | Akce | Microsoft.ApiManagement/service/users/groups/read | Získání seznamu skupin uživatelů |
> | Akce | Microsoft.ApiManagement/service/users/keys/read | Získat seznam klíčů uživatele |
> | Akce | Microsoft.ApiManagement/service/users/read | Získat seznam registrovaní uživatelé nebo získat podrobnosti o účtu uživatele |
> | Akce | Microsoft.ApiManagement/service/users/subscriptions/read | Získat seznam uživatelů odběrů |
> | Akce | Microsoft.ApiManagement/service/users/token/action | Získání tokenu tokenu přístupu pro uživatele |
> | Akce | Microsoft.ApiManagement/service/users/write | Zaregistrovat nového uživatele nebo účet podrobnosti aktualizace stávajícího uživatele |
> | Akce | Microsoft.ApiManagement/service/write | Vytvoření nové instance služby API Management |
> | Akce | Microsoft.ApiManagement/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Authorization/checkAccess/action | Zkontroluje, jestli má volající autorizaci provést určitou akci. |
> | Akce | Microsoft.Authorization/classicAdministrators/delete | Umožňuje odebrat správce z předplatného. |
> | Akce | Microsoft.Authorization/classicAdministrators/read | Umožňuje načíst správce předplatného. |
> | Akce | Microsoft.Authorization/classicAdministrators/write | Umožňuje přidat nebo změnit správce předplatného. |
> | Akce | Microsoft.Authorization/elevateAccess/action | Udělí volajícímu přístup Správce uživatelských přístupů v oboru tenanta. |
> | Akce | Microsoft.Authorization/locks/delete | Umožňuje odstranit zámky v zadaném oboru. |
> | Akce | Microsoft.Authorization/locks/read | Umožňuje načíst zámky v zadaném oboru. |
> | Akce | Microsoft.Authorization/locks/write | Umožňuje přidat zámky v zadaném oboru. |
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
> | Akce | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Zapíše požadavek na opětovné vygenerování klíčů Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/certificates/delete | Odstraní prostředek certifikátu automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/certificates/getCount/action | Přečte počet certifikátů |
> | Akce | Microsoft.Automation/automationAccounts/certificates/read | Načte prostředek certifikátu automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/certificates/write | Vytvoří nebo aktualizuje prostředek certifikátu automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/read | Přečte kompilace Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/read | Přečte kompilace Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapíše kompilace Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/compilationjobs/write | Zapíše kompilace Azure Automation DSC |
> | Akce | Microsoft.Automation/automationAccounts/configurations/content/read | Čte obsah média konfigurace |
> | Akce | Microsoft.Automation/automationAccounts/configurations/delete | Odstraní obsah DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/configurations/getCount/action | Přečte počet obsah DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/configurations/read | Získá obsah DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/configurations/write | Zapíše obsah DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connections/delete | Odstraní prostředek připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connections/getCount/action | Přečte počet připojení |
> | Akce | Microsoft.Automation/automationAccounts/connections/read | Načte prostředek připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connections/write | Vytvoří nebo aktualizuje prostředek připojení Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/connectionTypes/delete | Odstraní prostředek typu připojení automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/connectionTypes/read | Získá prostředek typu připojení automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/connectionTypes/write | Vytvoří prostředek typu připojení automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/credentials/delete | Odstraní prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/credentials/getCount/action | Přečte počet přihlašovací údaje |
> | Akce | Microsoft.Automation/automationAccounts/credentials/read | Získá prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/credentials/write | Vytvoří nebo aktualizuje prostředek přihlašovacích údajů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/delete | Odstraní účet Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Nastaví nastavení diagnostiky pro prostředek |
> | Akce | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Odstraní prostředky Hybrid Runbook Worker |
> | Akce | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
> | Akce | Microsoft.Automation/automationAccounts/jobs/output/read | Načte výstup úlohy |
> | Akce | Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Získá obsah runbooku automatizace Azure při provádění úlohy |
> | Akce | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobs/streams/read | Získá stream úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobs/streams/read | Získá stream úloh Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/delete | Odstraní plán úloh Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/read | Získá plán úloh Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úloh Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Získá prostoru propojí s účtem automation. |
> | Akce | Microsoft.Automation/automationAccounts/listKeys/action | Načte klíče pro účet služby automation |
> | Akce | Microsoft.Automation/automationAccounts/logDefinitions/read | Získá dostupné protokoly pro účet služby automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/activities/read | Získá aktivity automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/modules/delete | Odstraní modul Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/modules/getCount/action | Získá počet modulů v rámci účtu Automation. |
> | Akce | Microsoft.Automation/automationAccounts/modules/read | Získá modul automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/modules/write | Vytvoří nebo aktualizuje modul Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Odstraní konfigurace uzlu DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Čte obsah konfigurace uzlu DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Přečte konfigurace uzlu DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Zapíše konfigurace uzlu DSC Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/nodecounts/read | Přečte počet uzlů souhrnu pro zadaný typ. |
> | Akce | Microsoft.Automation/automationAccounts/nodes/delete | Odstraní uzlů Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/nodes/read | Přečte uzlů Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Čte obsah zprávy o Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/nodes/reports/read | Přečte sestavy Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/nodes/write | Vytvoří nebo aktualizuje uzlů Azure Automation DSC. |
> | Akce | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Získá TypeFields služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Získá definice metrik automatizace |
> | Akce | Microsoft.Automation/automationAccounts/read | Získá účet automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/content/read | Získá obsah runbooku automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/delete | Odstraní runbook služby automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Vytvoří obsah konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Získá výsledky operace konceptu runbooku automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Publikuje koncept runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/read | Získá koncept runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Získá testovací úlohu konceptu runbooku automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Obnoví testovací úlohu konceptu runbooku automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Zastaví testovací úlohu konceptu runbooku automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Pozastaví testovací úlohu konceptu runbooku automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Vytvoří testovací úlohu konceptu runbooku automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Vrátit zpět úpravy konceptu runbooku služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Získá počet sad runbook automatizace Azure. |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/runbooks/write | Vytvoří nebo aktualizuje runbook služby automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/schedules/delete | Odstraní prostředek plánování Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/schedules/getCount/action | Získá počet plánů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/schedules/read | Získá prostředek plánování Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/schedules/write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/statistics/read | Získá statistiku objektů služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Získat Azure Automation. aktualizace nasazení počítač |
> | Akce | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Získá oprava úlohu správy aktualizace služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/usages/read | Získá využití služby Azure Automation |
> | Akce | Microsoft.Automation/automationAccounts/variables/delete | Odstraní variabilní prostředek Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/variables/read | Přečte variabilní prostředek Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/variables/write | Vytvoří nebo aktualizuje variabilní prostředek Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/delete | Odstranit úlohu sledovacího procesu služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/read | Získá sledovacích procesů úlohu automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/watchers/start/action | Spustit úlohu sledovacího procesu služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/stop/action | Umožňuje zastavit úlohu sledovacího procesu služby Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/streams/read | Získá stream úloh Azure Automation sledovacích procesů |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Odstranění akcí úlohy sledovacích procesů Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Získá akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Vytvořit akce úlohy sledovacího procesu Azure Automation. |
> | Akce | Microsoft.Automation/automationAccounts/watchers/write | Vytvoří úlohu Azure Automation sledovacích procesů |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/action | Vygeneruje URI pro webhook automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/delete | Odstraní webhook automatizace Azure  |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/read | Přečte webhook automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/webhooks/write | Vytvoří nebo aktualizuje webhook automatizace Azure |
> | Akce | Microsoft.Automation/automationAccounts/write | Vytvoří nebo aktualizuje účet Azure Automation. |
> | Akce | Microsoft.Automation/operations/read | Získá dostupné operace pro prostředky Azure Automation. |
> | Akce | Microsoft.Automation/register/action | Zaregistruje předplatné pro Azure Automation |

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
> | Akce | Microsoft.AzureStack/register/action | Zaregistruje předplatné se poskytovatel prostředků Microsoft.AzureStack |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Odstraní předplatné zákazníka Azure zásobníku |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/read | Získá vlastnosti předplatné Azure zásobníku zákazníka |
> | Akce | Microsoft.AzureStack/registrations/customerSubscriptions/write | Vytvoří nebo aktualizuje předplatné Azure zásobníku zákazníka |
> | Akce | Microsoft.AzureStack/registrations/delete | Odstraní registraci Azure zásobníku |
> | Akce | Microsoft.AzureStack/registrations/getActivationKey/action | Získá nejnovější aktivační klíč Azure zásobníku |
> | Akce | Microsoft.AzureStack/registrations/products/listDetails/action | Načte Rozšířené podrobnosti o produkt Azure Marketplace zásobníku |
> | Akce | Microsoft.AzureStack/registrations/products/read | Získá vlastnosti o produkt Azure Marketplace zásobníku |
> | Akce | Microsoft.AzureStack/registrations/read | Získá vlastnosti zápisu Azure zásobníku |
> | Akce | Microsoft.AzureStack/registrations/write | Vytvoří nebo aktualizuje registraci Azure zásobníku |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Batch/batchAccounts/applications/delete | Odstraní aplikaci |
> | Akce | Microsoft.Batch/batchAccounts/applications/read | Seznam aplikací, nebo načte vlastnosti aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktivuje balíček aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/delete | Odstraní balíček aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/read | Získá vlastnosti balíčku aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/versions/write | Vytvoří nový balíček aplikace nebo aktualizuje existující balíček aplikace |
> | Akce | Microsoft.Batch/batchAccounts/applications/write | Vytvoří novou aplikaci nebo aktualizuje existující aplikace |
> | Akce | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Získá výsledky dlouhotrvající operace certifikát na účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Zruší se nezdařilo vytvoření certifikátu na účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/certificates/delete | Odstraní certifikát z účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/certificates/read | Zobrazí certifikáty na účtu Batch nebo načte vlastnosti certifikátu |
> | Akce | Microsoft.Batch/batchAccounts/certificates/write | Vytvoří nový certifikát účtu Batch nebo aktualizuje existující certifikát |
> | Akce | Microsoft.Batch/batchAccounts/delete | Odstraní účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/listkeys/action | Seznamy přístupové klíče pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/operationResults/read | Získá výsledky dlouhotrvající operace účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/poolOperationResults/read | Získá výsledky dlouhotrvající operace fondu na účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/delete | Odstraní fond z účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Zakáže Automatické škálování pro účet fondu služby Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/read | Zobrazí seznam fondů na účtu Batch nebo načte vlastnosti fondu |
> | Akce | Microsoft.Batch/batchAccounts/pools/stopResize/action | Resize – zastaví na probíhající operace v účtu fondu služby Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Upgraduje operační systém fondu účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/pools/write | Vytvoří nový fond na účtu Batch nebo aktualizuje existující fond |
> | Akce | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro službu Batch |
> | Akce | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro službu Batch |
> | Akce | Microsoft.Batch/batchAccounts/read | Zobrazí seznam účtů Batch nebo načte vlastnosti účtu Batch |
> | Akce | Microsoft.Batch/batchAccounts/regeneratekeys/action | Obnoví přístupové klíče pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronizuje přístupové klíče pro účet úložiště automaticky nakonfigurován pro účet Batch |
> | Akce | Microsoft.Batch/batchAccounts/write | Vytvoří nový účet Batch nebo aktualizuje existující účet Batch |
> | Akce | Microsoft.Batch/locations/checkNameAvailability/action | Ověří, že název účtu není platný a není používán. |
> | Akce | Microsoft.Batch/locations/quotas/read | Získá Batch kvóty předplatného zadané v zadané oblasti Azure |
> | Akce | Microsoft.Batch/register/action | Zaregistruje předplatné pro poskytovatele prostředků Batch a povolí vytvoření účtů Batch |
> | Akce | Microsoft.Batch/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Batch brání vytváření účtů Batch |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.BatchAI/clusters/read | Uvádí Batch AI clustery nebo načte vlastnosti clusteru Batch AI |
> | Akce | Microsoft.BatchAI/fileservers/read | Uvádí Batch AI fileservers nebo získá vlastnosti u souborového serveru Batch AI |
> | Akce | Microsoft.BatchAI/locations/operationresults/read | Získá výsledek operace asynchronní Batch AI v zadané oblasti Azure |
> | Akce | Microsoft.BatchAI/locations/operationstatuses/read | Získá stav asynchronní operace Batch AI v zadané oblasti Azure |
> | Akce | Microsoft.BatchAI/locations/usages/read | Získá dávkové AI použití zadané předplatné v zadané oblasti Azure |
> | Akce | Microsoft.BatchAI/register/action | Zaregistruje předplatné pro poskytovatele prostředků AI Batch a umožňuje vytváření prostředků Batch AI |
> | Akce | Microsoft.BatchAI/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků AI Batch brání vytváření prostředků Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/clusters/delete | Odstraní cluster Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/clusters/read | Uvádí Batch AI clustery nebo načte vlastnosti clusteru Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/clusters/remoteLoginInformation/action | Uvádí vzdálené přihlašovací informace pro cluster s podporou Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/clusters/write | Vytvoří nový cluster Batch AI nebo aktualizuje existující cluster Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/delete | Odstraní prostoru Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/delete | Odstraní experimentu Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/jobs/delete | Odstraní úlohu Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/jobs/listoutputfiles/action | Seznamy výstupní soubory pro úlohy Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/jobs/read | Uvádí úlohy Batch AI nebo načte vlastnosti úlohy Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/jobs/remoteLoginInformation/action | Zobrazí informace o vzdálené přihlášení pro úlohy Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/jobs/terminate/action | Ukončí úlohu Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/jobs/write | Vytvoří novou úlohu Batch AI nebo aktualizuje existující úlohy Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/read | Seznamy Batch AI experimenty nebo získá vlastnosti experimentu Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/experiments/write | Vytvoří nový experiment Batch AI nebo aktualizuje existující experimentu Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/fileservers/delete | Odstraní u souborového serveru Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/fileservers/read | Uvádí Batch AI fileservers nebo získá vlastnosti u souborového serveru Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/fileservers/write | Vytvoří nový souborovém serveru Batch AI nebo aktualizuje souborovém serveru existující Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/read | Uvádí pracovní prostory Batch AI nebo načte vlastnosti pracovního prostoru Batch AI |
> | Akce | Microsoft.BatchAI/workspaces/write | Vytvoří nový pracovní prostor Batch AI nebo aktualizuje existujícímu pracovnímu prostoru Batch AI |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Billing/billingPeriods/read | Zobrazí seznam dostupných fakturační období |
> | Akce | Microsoft.Billing/invoices/read | K dispozici faktury seznamy |

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

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Cache/checknameavailability/action | Zkontroluje, jestli je název k dispozici, aby se dal použít spolu s novou mezipamětí Redis Cache. |
> | Akce | Microsoft.Cache/locations/operationresults/read | Získá výsledek dlouho spuštěné operace, pro kterou se klientovi dříve vrátila hlavička Location. |
> | Akce | Microsoft.Cache/operations/read | Vypíše operace, které podporuje poskytovatel Microsoft.Cache. |
> | Akce | Microsoft.Cache/redis/delete | Odstranit celou službu Redis Cache |
> | Akce | Microsoft.Cache/redis/export/action | Export dat Redis v určeném formátu do objektů blob úložiště označených prefixem |
> | Akce | Microsoft.Cache/redis/firewallRules/delete | Odstraní pravidla firewallu protokolu IP mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/firewallRules/read | Získá pravidla firewallu protokolu IP mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/firewallRules/write | Upraví pravidla firewallu protokolu IP mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/forceReboot/action | Vynuťte restartování instance mezipaměti, což ale může mít za následek ztrátu dat. |
> | Akce | Microsoft.Cache/redis/import/action | Import dat určeného formátu z víc objektů blob do Redis |
> | Akce | Microsoft.Cache/redis/linkedservers/delete | Odstranit propojený server z mezipaměti Redis Cache |
> | Akce | Microsoft.Cache/redis/linkedservers/read | Získat propojené servery přidružené k mezipaměti Redis Cache |
> | Akce | Microsoft.Cache/redis/linkedservers/write | Přidat propojený server do mezipaměti Redis Cache |
> | Akce | Microsoft.Cache/redis/listKeys/action | Zobrazit hodnotu přístupových klíčů služby Redis Cache na portálu pro správu |
> | Akce | Microsoft.Cache/redis/listUpgradeNotifications/read | Vypíše seznam nejnovějších upozornění na upgrady pro klienta mezipaměti. |
> | Akce | Microsoft.Cache/redis/metricDefinitions/read | Načte dostupné metriky pro Redis Cache. |
> | Akce | Microsoft.Cache/redis/patchSchedules/delete | Odstraní plán oprav mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/patchSchedules/read | Získá plán oprav mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/patchSchedules/write | Upraví plán oprav mezipaměti Redis Cache. |
> | Akce | Microsoft.Cache/redis/read | Zobrazit nastavení a konfiguraci Redis Cache na portálu pro správu |
> | Akce | Microsoft.Cache/redis/regenerateKey/action | Změnit hodnotu přístupových klíčů služby Redis Cache na portálu pro správu |
> | Akce | Microsoft.Cache/redis/start/action | Spuštění instance mezipaměti |
> | Akce | Microsoft.Cache/redis/stop/action | Zastavení instance mezipaměti |
> | Akce | Microsoft.Cache/redis/write | Upravit nastavení a konfiguraci služby Redis Cache na portálu pro správu |
> | Akce | Microsoft.Cache/register/action | Zaregistruje poskytovatele prostředků Microsoft.Cache u předplatného. |
> | Akce | Microsoft.Cache/unregister/action | Zruší registraci poskytovatele prostředků Microsoft.Cache u předplatného. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Capacity/register/action | Registruje zprostředkovatele prostředků kapacity a umožňuje vytváření kapacity prostředků. |
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
> | Akce | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek |
> | Akce | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek |
> | Akce | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro Microsoft.Cdn |
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
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Odstraňte existující certifikát |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Získat seznam certifikátů |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Přidat nový certifikát nebo aktualizovat stávající |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Delete | Odstraňte existující AppServiceCertificate |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Read | Získání seznamu CertificateOrders |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Opakujte existující certificateorder |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Obnovit existující certificateorder |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Znovu odeslat e-mail certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Znovu odeslat žádost o e-mailů jinou e-mailová adresa |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Načtení lokality zapečetění vydaného certifikátu služby aplikace |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Načtení seznamu akce certifikátu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Načtení historie certifikát e-mailu |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Ověřit vlastnictví domény |
> | Akce | Microsoft.CertificateRegistration/certificateOrders/Write | Přidat nové certificateOrder nebo aktualizovat stávající |
> | Akce | Microsoft.CertificateRegistration/operations/Read | Zobrazí seznam všech operací z registrace certifikátu služby aplikace |
> | Akce | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Zřízení instančního objektu pro objekt zabezpečení služby aplikace |
> | Akce | Microsoft.CertificateRegistration/register/action | Registrace poskytovatele prostředků Certificates společnosti Microsoft pro předplatné |
> | Akce | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Ověření objektu nákupu certifikát bez odeslání |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicCompute/capabilities/read | Umožňuje zobrazit schopnosti. |
> | Akce | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Umožňuje zkontrolovat dostupnost zadaného názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/active/write | Slouží k nastavení názvu aktivní domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Umožňuje zobrazit skupinu dostupnosti pro prostředek. |
> | Akce | Microsoft.ClassicCompute/domainNames/capabilities/read | Ukazuje možnosti názvu domény |
> | Akce | Microsoft.ClassicCompute/domainNames/delete | Umožňuje odebrat názvy domén pro prostředky. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/delete | Odebere rozšíření názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Přečte stav operace pro rozšíření názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/read | Vrátí rozšíření názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/extensions/write | Přidá rozšíření názvu domény. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Umožňuje odebrat interní nástroj pro vyrovnávání zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Přečte stav operace pro interní služby Load Balancer názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Umožňuje načíst interní nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Umožňuje vytvořit nový interní nástroj pro vyrovnávání zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Přečte stav operace pro sady koncových bodů s vyrovnaným zatížením u názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Umožňuje zobrazit skupiny koncových bodů s vyrovnáváním zatížení. |
> | Akce | Microsoft.ClassicCompute/domainNames/read | Umožňuje načíst názvy domén pro prostředky. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Umožňuje odstranit používané certifikáty služeb. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Přečte stav operace pro certifikáty služeb názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Umožňuje načíst používané certifikáty služeb. |
> | Akce | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Umožňuje přidat nebo změnit používané certifikáty služeb. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/delete | Umožňuje odstranit zadaný slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Přečte stav operace pro sloty názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/read | Umožňuje zobrazit sloty pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Odebere odkaz na rozšíření u role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Přečte stav operace pro reference na rozšíření rolí slotů názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Vrátí odkaz na rozšíření u role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Přidá nebo upraví odkaz na rozšíření u role slotu nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Umožňuje načíst nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Umožňuje přidat nebo změnit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Umožňuje načíst definice metrik. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/read | Umožňuje načíst roli slotu pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Přečte stav operace pro instance rolí u rolí slotů názvů domén. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Umožňuje načíst instanci role. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Znovu sestaví instanci role. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Umožňuje obnovit instanci role z image. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Umožňuje restartovat instance role. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/start/action | Umožňuje spustit slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Umožňuje změnit stav slotu pro nasazení na Zastavený. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Umožňuje změnit stav slotu pro nasazení na Spuštěný. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/stop/action | Umožňuje pozastavit slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Umožňuje upgradovat doménu. |
> | Akce | Microsoft.ClassicCompute/domainNames/slots/write | Umožňuje vytvořit nebo aktualizovat slot pro nasazení. |
> | Akce | Microsoft.ClassicCompute/domainNames/swap/action | Umožňuje přepnout přípravný slot na produkční. |
> | Akce | Microsoft.ClassicCompute/domainNames/write | Umožňuje přidat nebo změnit názvy domén pro prostředky. |
> | Akce | Microsoft.ClassicCompute/moveSubscriptionResources/action | Přesune všechny klasické prostředky do jiného předplatného. |
> | Akce | Microsoft.ClassicCompute/operatingSystemFamilies/read | Vypíše seznam řad hostovaných operačních systémů, které jsou dostupné v Microsoft Azure, a taky vypíše verze operačních systémů dostupné pro každou řadu. |
> | Akce | Microsoft.ClassicCompute/operatingSystems/read | Vypíše seznam verzí hostovaných operačních systémů, které jsou aktuálně dostupné v Microsoft Azure. |
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
> | Akce | Microsoft.ClassicCompute/virtualMachines/delete | Umožňuje odebrat virtuální počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Umožňuje odpojit datový disk od virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/disks/read | Umožňuje načíst seznam datových disků. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Umožňuje stáhnout soubor RDP pro virtuální počítač. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Přečte stav operace pro rozšíření virtuálních počítačů. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/read | Umožňuje načíst rozšíření virtuálního počítače. |
> | Akce | Microsoft.ClassicCompute/virtualMachines/extensions/write | Umožňuje vložit rozšíření virtuálního počítače. |
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
> | Akce | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Umožňuje načíst seznam podporovaných zařízení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/read | Načte skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Odstraní pravidlo zabezpečení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Přečte stav operace pro pravidla zabezpečení skupiny u zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Načte pravidlo zabezpečení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Přidá nebo aktualizuje pravidlo zabezpečení. |
> | Akce | Microsoft.ClassicNetwork/networkSecurityGroups/write | Přidá novou skupinu zabezpečení sítě. |
> | Akce | Microsoft.ClassicNetwork/quotas/read | Umožňuje načíst kvótu pro předplatné. |
> | Akce | Microsoft.ClassicNetwork/register/action | Registrace pro klasickou síť |
> | Akce | Microsoft.ClassicNetwork/reservedIps/delete | Umožňuje odstranit vyhrazenou IP adresu. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/join/action | Připojit se k rezervované IP adrese |
> | Akce | Microsoft.ClassicNetwork/reservedIps/link/action | Odkaz na rezervovanou IP adresu |
> | Akce | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Přečte stav operace pro rezervované IP adresy. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/read | Umožňuje načíst vyhrazené IP adresy. |
> | Akce | Microsoft.ClassicNetwork/reservedIps/write | Umožňuje přidat novou vyhrazenou IP adresu. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Umožňuje zobrazit schopnosti. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Zkontroluje dostupnost dané IP adresy ve virtuální síti. |
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
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/read | Umožňuje načíst virtuální síť. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě spojenou s podsítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Přečte stav operace pro skupinu zabezpečení sítě spojenou s podsítí virtuální sítě. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Načte skupinu zabezpečení sítě spojenou s podsítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Přidá skupinu zabezpečení sítě spojenou s podsítí. |
> | Akce | Microsoft.ClassicNetwork/virtualNetworks/write | Umožňuje přidat novou virtuální síť. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ClassicStorage/capabilities/read | Umožňuje zobrazit schopnosti. |
> | Akce | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Umožňuje zkontrolovat dostupnost účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/disks/read | Vrátí disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/images/read | Vrátí image. |
> | Akce | Microsoft.ClassicStorage/osImages/read | Vrátí image operačního systému. |
> | Akce | Microsoft.ClassicStorage/publicImages/read | Načte veřejnou image virtuálního počítače. |
> | Akce | Microsoft.ClassicStorage/quotas/read | Umožňuje načíst kvótu pro předplatné. |
> | Akce | Microsoft.ClassicStorage/register/action | Umožňuje registraci v klasickém úložišti. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/delete | Umožňuje odstranit účet úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/delete | Odstraní daný disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/read | Vrátí disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/disks/write | Přidá disk účtu úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/delete | Odstraní danou image účtu storage. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí image účtu storage. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Odstraní danou image operačního systému pro účet Storage. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/osImages/read | Vrátí image operačního systému pro účet Storage. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Umožňuje znovu vytvořit existující přístupové klíče pro účet úložiště. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Umožňuje načíst nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Umožňuje přidat nebo změnit nastavení diagnostiky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Umožňuje načíst definice metrik. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Umožňuje načíst metriky. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/services/read | Umožňuje načíst dostupné služby. |
> | Akce | Microsoft.ClassicStorage/storageAccounts/write | Umožňuje přidat nový účet úložiště. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CognitiveServices/accounts/delete | Odstraní účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/accounts/listKeys/action | Zobrazit seznam klíčů |
> | Akce | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. |
> | Akce | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředku. |
> | Akce | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Získá dostupné metriky pro službu Cognitive Services. |
> | Akce | Microsoft.CognitiveServices/accounts/read | Načte účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/accounts/regenerateKey/action | Znovu vygenerovat klíč |
> | Akce | Microsoft.CognitiveServices/accounts/skus/read | Načte dostupné skladové položky (SKU) pro existující prostředek. |
> | Akce | Microsoft.CognitiveServices/accounts/usages/read | Získá využití kvóty pro existující prostředek. |
> | Akce | Microsoft.CognitiveServices/accounts/write | Zapíše účty rozhraní API. |
> | Akce | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Přečte dostupná SKU pro předplatné. |
> | Akce | Microsoft.CognitiveServices/Operations/read | Vypíše všechny dostupné operace. |
> | Akce | Microsoft.CognitiveServices/register/action | Zaregistruje předplatné do služeb Cognitive Services. |
> | Akce | Microsoft.CognitiveServices/skus/read | Přečte dostupná SKU pro služby Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Commerce/RateCard/read | Vrátí nabízejí data, prostředku nebo měření metadata a sazby pro dané předplatné. |
> | Akce | Microsoft.Commerce/UsageAggregates/read | Získá předplatné Microsoft Azure spotřeby. Výsledek obsahuje agreguje data o využití, předplatné a prostředků související informace, na konkrétní časové rozmezí. |

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
> | Akce | Microsoft.Compute/images/delete | Odstraní image. |
> | Akce | Microsoft.Compute/images/read | Získá vlastnosti image. |
> | Akce | Microsoft.Compute/images/write | Vytvoří novou image nebo aktualizuje nějakou existující. |
> | Akce | Microsoft.Compute/locations/capsOperations/read | Umožňuje získat stav asynchronní operace Caps. |
> | Akce | Microsoft.Compute/locations/diskOperations/read | Získá stav asynchronní operace disku |
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
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Přihlášení k virtuálnímu počítači s oprávněními správce ve Windows nebo kořenovými oprávněními na Linuxu |
> | Akce | Microsoft.Compute/virtualMachines/performMaintenance/action | Provádí operace údržby ve virtuálním počítači. |
> | Akce | Microsoft.Compute/virtualMachines/powerOff/action | Umožňuje vypnout virtuální počítač. Všimněte si, že virtuální počítač bude dál účtovat. |
> | Akce | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Přečte definice metriky Virtuální počítač. |
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
> | Akce | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Získat historii upgradů operačního systému pro škálovací sadu virtuálních počítačů |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Provede plánovanou údržbu instancí škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Umožňuje vypnout instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Přečte definice metriky Škálovací sada virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Získá vlastnosti všech veřejných IP adres škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/read | Umožňuje získat vlastnosti škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Znovu nasadí instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Umožňuje obnovit instance škálovací sady virtuálních počítačů z image. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/restart/action | Umožňuje restartovat instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Zruší upgrade škálovací sady virtuálních počítačů se zajištěním provozu. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Získá nejnovější stav upgradu škálovací sady virtuálních počítačů se zajištěním provozu. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/scale/action | Ověřte, že existující škálovací sada virtuálních počítačů může horizontálně snížit nebo navýšit kapacitu na zadaný počet instancí. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/skus/read | Umožňuje zobrazit seznam platných SKU pro existující škálovací sadu virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/start/action | Umožňuje spustit instance škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Umožňuje vypnout a uvolnit výpočetní prostředky pro virtuální počítač ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Umožňuje odstranit konkrétní virtuální počítač ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Umožňuje načíst zobrazení instance virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Získáte vlastnosti veřejné IP adresy, které jsou vytvořené pomocí sady škálování virtuálního počítače. Sadu škálování virtuálního počítače můžete vytvořit maximálně jednu veřejnou IP adresu na ipconfiguration (privátní IP) |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Získáte vlastnosti jednoho nebo všech konfigurace protokolu IP síťového rozhraní vytvořený sadu škálování virtuálního počítače. Konfigurace protokolu IP představují privátních IP adres |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Získá vlastnosti jednoho nebo všech síťových rozhraní virtuálního počítače vytvořených pomocí škálovací sady virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Provede plánovanou údržbu instance virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Umožňuje vypnout instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Přečte definice metriky Virtuální počítač ve škálovací sadě. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Umožňuje načíst vlastnosti virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Znovu nasadí instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Obnoví instanci virtuálního počítače ve škálovací sadě virtuálních počítačů z image. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Umožňuje restartovat instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Umožňuje spustit instanci virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Umožňuje aktualizovat vlastnosti virtuálního počítače ve škálovací sadě virtuálních počítačů. |
> | Akce | Microsoft.Compute/virtualMachineScaleSets/write | Umožňuje vytvořit novou nebo aktualizovat stávající škálovací sadu virtuálních počítačů. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Consumption/balances/read | Zobrazí seznam využití shrnutí fakturační období pro skupinu pro správu. |
> | Akce | Microsoft.Consumption/budgets/read | Zobrazí seznam rozpočty předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/budgets/write | Vytvoří, aktualizovat a Odstranit rozpočty předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/marketplaces/read | Zobrazí seznam podrobnosti o obor odběrů EA a WebDirect využití prostředků marketplace. |
> | Akce | Microsoft.Consumption/operations/read | Zobrazí seznam všech podporovaných operací Microsoft.Consumption poskytovatele prostředků. |
> | Akce | Microsoft.Consumption/pricesheets/read | Zobrazí seznam dat Pricesheets pro předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/reservationDetails/read | Zobrazí seznam podrobnosti o využití pro vyhrazené instance rezervace pořadí nebo správu skupiny. Podrobnosti o data jsou za instance pro každý den úroveň. |
> | Akce | Microsoft.Consumption/reservationRecommendations/read | Seznam jedné nebo sdíleného doporučení pro vyhrazené instance pro předplatné. |
> | Akce | Microsoft.Consumption/reservationSummaries/read | Zobrazí seznam využití shrnutí vyhrazená instance podle rezervace pořadí nebo správu skupin. Souhrnná data je buď na úrovni denní nebo měsíční. |
> | Akce | Microsoft.Consumption/reservationTransactions/read | Zobrazí seznam historie transakcí vyhrazená instance podle skupiny pro správu. |
> | Akce | Microsoft.Consumption/tenants/register/action | Zaregistrujte akce pro obor Microsoft.Consumption klientem. |
> | Akce | Microsoft.Consumption/terms/read | Zobrazí seznam podmínek pro předplatné nebo skupinu pro správu. |
> | Akce | Microsoft.Consumption/usageDetails/read | Zobrazí seznam podrobnosti o využití pro obor pro EA i WebDirect předplatné. |

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
> | Akce | Microsoft.ContainerInstance/containerGroups/write | Vytvoří nebo aktualizuje konkrétní skupinu kontejnerů. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerRegistry/checkNameAvailability/read | Zkontroluje, zda je k dispozici pro použití registru název kontejneru. |
> | Akce | Microsoft.ContainerRegistry/locations/operationResults/read | Získá výsledek asynchronní operace |
> | Akce | Microsoft.ContainerRegistry/operations/read | Zobrazí seznam všech dostupných operací REST API služby Azure kontejneru registru |
> | Akce | Microsoft.ContainerRegistry/register/action | Zaregistruje předplatné pro zprostředkovatele prostředků registru kontejneru a umožňuje vytvoření kontejneru registrech. |
> | Akce | Microsoft.ContainerRegistry/registries/delete | Odstraní kontejner registru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Filtr událostí mřížky odstraní z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Získá vlastnosti filtru mřížky zadanou událost nebo vypíše všechny filtry mřížky událostí v registru zadaného kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Vytvoří nebo aktualizuje filtr událostí mřížky pro kontejner registru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/importImage/action | Importujte bitovou kopii do kontejneru registru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/listCredentials/action | Uvádí přihlašovací údaje pro zadaný kontejner registru. |
> | Akce | Microsoft.ContainerRegistry/registries/listUsages/read | Uvádí použití kvóty pro zadaný kontejner registru. |
> | Akce | Microsoft.ContainerRegistry/registries/operationStatuses/read | Získá stav registru asynchronní operace |
> | Akce | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro Microsoft ContainerRegistry |
> | Akce | Microsoft.ContainerRegistry/registries/read | Získá vlastnosti registru zadaný kontejner nebo vypíše všechny registrech kontejneru pod zadaná skupina prostředků nebo předplatného. |
> | Akce | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regeneruje mezi přihlašovací údaje pro zadaný kontejner registru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/delete | Odstraní replikace z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Získá stav asynchronní operace replikace |
> | Akce | Microsoft.ContainerRegistry/registries/replications/read | Načte vlastnosti zadaného replikace nebo vypíše všechny replikace pro zadaný kontejner registru. |
> | Akce | Microsoft.ContainerRegistry/registries/replications/write | Vytvoří nebo aktualizuje replikace pro kontejner registru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/delete | Odstraní webhook, jehož z registru kontejneru. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Získá konfiguraci URI služby a vlastní hlavičky pro webhooku. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Seznam posledních událostí zadaný webhooku. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Získá stav webhooku asynchronní operace |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Aktivuje událost ping k odeslání do webhooku. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/read | Načte vlastnosti zadaného webhooku nebo vypíše všechny webhooků, které jsou pro zadaný kontejner registru. |
> | Akce | Microsoft.ContainerRegistry/registries/webhooks/write | Vytvoří nebo aktualizuje webhook pro kontejner registru se zadanými parametry. |
> | Akce | Microsoft.ContainerRegistry/registries/write | Vytvoří nebo aktualizuje registr kontejneru se zadanými parametry. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContainerService/containerServices/delete | Odstraní službu kontejneru. |
> | Akce | Microsoft.ContainerService/containerServices/read | Získat služby kontejneru |
> | Akce | Microsoft.ContainerService/containerServices/write | Vytvoří novou službu kontejneru nebo aktualizuje existující |
> | Akce | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Získat profil spravovaný cluster přístup podle názvu role pomocí přihlašovacích údajů seznamu |
> | Akce | Microsoft.ContainerService/managedClusters/accessProfiles/read | Získat profil spravovaný cluster přístup podle názvu role |
> | Akce | Microsoft.ContainerService/managedClusters/delete | Odstraní spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky pro prostředek spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro spravovaný Cluster |
> | Akce | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro spravovaný Cluster |
> | Akce | Microsoft.ContainerService/managedClusters/read | Získat spravovaného clusteru |
> | Akce | Microsoft.ContainerService/managedClusters/write | Vytvoří nový spravovaný cluster nebo aktualizuje existující |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ContentModerator/applications/delete | Operace odstranění |
> | Akce | Microsoft.ContentModerator/applications/listSecrets/action | Uvést tajné klíče |
> | Akce | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Přečíst jednotného přihlašování tokeny |
> | Akce | Microsoft.ContentModerator/applications/read | Operace čtení |
> | Akce | Microsoft.ContentModerator/applications/write | Operace zápisu |
> | Akce | Microsoft.ContentModerator/applications/write | Operace zápisu |
> | Akce | Microsoft.ContentModerator/listCommunicationPreference/action | Seznam komunikace předvoleb |
> | Akce | Microsoft.ContentModerator/operations/read | operace čtení |
> | Akce | Microsoft.ContentModerator/updateCommunicationPreference/action | Předvolby komunikace aktualizace |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.CustomerInsights/hubs/adobemetadata/action | Vytvořit nebo aktualizovat veškerá Metadata, Adobe Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/adobemetadata/read | Číst veškerá Metadata, Adobe Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Odstranit všechny zásady Azure zákazníků Statistika sdílený přístupový podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Přečtěte si, že žádné přehledy Azure zákazníků sdílené zásady přístupu podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Znova vygenerovat primární klíč Azure zákazníka Statistika zásada sdíleného přístupu podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Znova vygenerovat sekundární klíč Azure zákazníka Statistika zásada sdíleného přístupu podpis |
> | Akce | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Vytvořit nebo aktualizovat žádné zásady Azure zákazníků Statistika sdílený přístupový podpis |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivovat všechny konektor služby Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivovat všechny konektor služby Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/delete | Odstranit všechny konektory Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Získání běhového stavu všech konektor služby Statistika zákazníka Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktivovat žádné mapování Statistika konektor Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Odstranit všechny Azure zákazníků Statistika konektor mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika konektor mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Číst všechny Azure zákazníků Statistika konektor mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Vytvořit nebo aktualizovat všechny Azure zákazníků Statistika konektor mapování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/operations/read | Číst libovolný výsledek operace konektor služby Statistika zákazníka Azure |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/read | Číst všechny konektor služby Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Vytvořit nebo aktualizovat všechny bráně Azure zákazníků Statistika konektor ověřování |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/update/action | Aktualizovat všechny konektor služby Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/connectors/write | Vytvořit nebo aktualizovat všechny konektor služby Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/crmmetadata/action | Vytvořit nebo aktualizovat veškerá Crm Metadata, Azure zákazníků statistiky |
> | Akce | Microsoft.CustomerInsights/hubs/crmmetadata/read | Číst veškerá Crm Metadata, Azure zákazníků statistiky |
> | Akce | Microsoft.CustomerInsights/hubs/delete | Odstranit všechny statistiky Centrum Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/gdpr/delete | Odstranit všechny statistiky Gdpr Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/gdpr/read | Číst všechny statistiky Gdpr Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/gdpr/write | Vytvořit nebo aktualizovat všechny statistiky Gdpr Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Získání Azure zákazníků Statistika rozbočovače fakturace kredity |
> | Akce | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Získání historie fakturace rozbočovače Statistika Azure zákazníka |
> | Akce | Microsoft.CustomerInsights/hubs/images/delete | Odstranit všechny Image Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/images/read | Číst všechny statistiky Image Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/images/write | Vytvořit nebo aktualizovat žádný obrázek Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/delete | Odstranit všechny azure interakcí statistiky |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika interakce |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/read | Číst interakce Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Navrhněte vztah odkazy pro všechny interakce Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/interactions/write | Vytvořit nebo aktualizovat interakce Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/delete | Odstranit všechny Azure zákazníků Statistika klíčového ukazatele výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika klíčové ukazatele výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/read | Číst všechny Azure zákazníků Statistika klíčového ukazatele výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Znovu zpracovat žádné Azure zákazníků přehled klíčových ukazatelů výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/kpi/write | Vytvořit nebo aktualizovat všechny Azure zákazníků Statistika klíčového ukazatele výkonu |
> | Akce | Microsoft.CustomerInsights/hubs/links/delete | Odstranit všechny odkazy Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/links/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika odkazy |
> | Akce | Microsoft.CustomerInsights/hubs/links/read | Číst všechny odkazy Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/links/write | Vytvořit nebo aktualizovat jakékoli odkazy Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/msemetadata/action | Vytvořit nebo aktualizovat veškerá Metadata, Mse Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/msemetadata/read | Číst veškerá Metadata, Mse Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/operationresults/read | Získat výsledky operace rozbočovače Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/delete | Odstranit všechny statistiky předpovědi Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika předpovědi |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/read | Číst všechny statistiky předpovědi Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/predictions/write | Vytvořit nebo aktualizovat všechny předpovědi Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Odstranit všechny zásady Azure zákazníků Statistika prediktivní shody |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika prediktivní shodu zásad |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Číst všechny zásady Azure zákazníků Statistika prediktivní shody |
> | Akce | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Vytvořit nebo aktualizovat všechny zásady Azure zákazníků Statistika prediktivní shody |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/delete | Odstranit žádný profil Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika profilu |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/read | Číst všechny statistiky profil Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/profiles/write | Zápis žádný profil Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro prostředek |
> | Akce | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro prostředek |
> | Akce | Microsoft.CustomerInsights/hubs/read | Číst všechny statistiky centra Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Odstraňte jakékoli odkazy Statistika vztah Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika vztah odkazy |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Přečtěte si jakékoli odkazy Statistika vztah Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Vytvořit nebo aktualizovat jakékoli odkazy Statistika vztah Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/delete | Odstranit všechny vztahy Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/operations/read | Číst libovolný výsledek operace Statistika vztahů se zákazníky Azure |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/read | Číst všechny vztahy Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/relationships/write | Vytvořit nebo aktualizovat všechny vztahy Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Odstranit všechny Azure zákazníků Statistika Rbac přiřazení |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Číst libovolný výsledek operace Azure zákazníka Statistika Rbac přiřazení |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/read | Číst všechny Azure zákazníků Statistika Rbac přiřazení |
> | Akce | Microsoft.CustomerInsights/hubs/roleAssignments/write | Vytvořit nebo aktualizovat všechny Azure zákazníků Statistika Rbac přiřazení |
> | Akce | Microsoft.CustomerInsights/hubs/roles/read | Číst všechny role Rbac Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Vytvořit nebo aktualizovat veškerá Metadata, SalesForce Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Číst veškerá Metadata, SalesForce Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/segments/delete | Odstranit všechny segmenty Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmentuje žádné Azure zákazníků přehled dynamické správy |
> | Akce | Microsoft.CustomerInsights/hubs/segments/read | Číst všechny segmenty Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/segments/static/action | Segmentuje všechny statické Azure zákazníků přehled správy |
> | Akce | Microsoft.CustomerInsights/hubs/segments/write | Vytvořit nebo aktualizovat všechny segmenty Statistika Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Odstranit všechny statistiky SqlConnectionStrings Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Číst všechny statistiky SqlConnectionStrings Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Vytvořit nebo aktualizovat všechny statistiky SqlConnectionStrings Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generování navrhnout typ schématu z ukázkových dat |
> | Akce | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Spravovat nastavení centra zákazníka Statistika Azure |
> | Akce | Microsoft.CustomerInsights/hubs/views/delete | Odstranit všechna zobrazení statistiky aplikace Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/views/read | Přečtěte si všechna zobrazení statistiky aplikace Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/views/write | Vytvořit nebo aktualizovat všechna zobrazení statistiky aplikace Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/widgettypes/read | Číst všechny typy pomůcky App Insights Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/hubs/write | Vytvořit nebo aktualizovat všechny statistiky centra Azure zákazníků |
> | Akce | Microsoft.CustomerInsights/operations/read | Přečtěte si Azure zákazníků Insights Api Metadatas |
> | Akce | Microsoft.CustomerInsights/register/action | Zaregistruje předplatné pro poskytovatele prostředků Statistika zákazníka a umožňuje vytváření prostředků Statistika zákazníka |
> | Akce | Microsoft.CustomerInsights/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Statistika zákazníka |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Databricks/workspaces/delete | Odebere k pracovnímu prostoru. |
> | Akce | Microsoft.Databricks/workspaces/read | Načte seznam pracovních prostorů. |
> | Akce | Microsoft.Databricks/workspaces/write | Vytvoří pracovní prostor služby. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataCatalog/catalogs/delete | Odstraní katalogu. |
> | Akce | Microsoft.DataCatalog/catalogs/read | Načíst vlastnosti pro katalogu nebo katalogů pod předplatné nebo skupinu prostředků. |
> | Akce | Microsoft.DataCatalog/catalogs/write | Katalog vytvoří nebo aktualizuje značky a vlastnosti pro katalog. |
> | Akce | Microsoft.DataCatalog/checkNameAvailability/action | Zkontroluje dostupnost názvu katalogu pro klienta. |
> | Akce | Microsoft.DataCatalog/operations/read | Seznam operací dostupných na zprostředkovateli prostředků Microsoft.DataCatalog. |
> | Akce | Microsoft.DataCatalog/register/action | Registruje zprostředkovatele prostředků Microsoft.DataCatalog předplatné. |
> | Akce | Microsoft.DataCatalog/unregister/action | Zruší registraci předplatného od zprostředkovatele prostředků Microsoft.DataCatalog. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataFactory/datafactories/activitywindows/read | Přečte okna aktivity v datové továrně se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Přečte aktivity Windows aktivity kanálu se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Přečte okna aktivity pro kanál se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/delete | Odstraní všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pozastaví všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/read | Načte všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Obnoví všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualizuje všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datapipelines/write | Vytvoří nebo aktualizuje všechny kanálu. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Přečte okna aktivity pro datovou sadu se zadanými parametry. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/delete | Odstraní všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/read | Načte všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Čte Data řez spustit pro danou datovou sadu s danou počáteční čas. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/slices/read | Získá datové řezy v daném časovém období. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualizujte stav datový řez. |
> | Akce | Microsoft.DataFactory/datafactories/datasets/write | Vytvoří nebo aktualizuje všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/delete | Odstraní Data Factory. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Čte informace o připojení pro všechny brány. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/delete | Odstraní všechny brány. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Zobrazí ověřovací klíče pro všechny brány. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/read | Načte všechny brány. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Vygeneruje ověřovací klíče pro všechny brány. |
> | Akce | Microsoft.DataFactory/datafactories/gateways/write | Vytvoří nebo aktualizuje žádné brány. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/delete | Odstraní všechny propojené služby. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/read | Načte všechny propojené služby. |
> | Akce | Microsoft.DataFactory/datafactories/linkedServices/write | Vytvoří nebo aktualizuje všechny propojené služby. |
> | Akce | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro datafactories |
> | Akce | Microsoft.DataFactory/datafactories/read | Čte Data Factory. |
> | Akce | Microsoft.DataFactory/datafactories/runs/loginfo/read | Načte identifikátor URI SAS pro kontejner objektů blob obsahující protokoly. |
> | Akce | Microsoft.DataFactory/datafactories/tables/delete | Odstraní všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/tables/read | Načte všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/tables/write | Vytvoří nebo aktualizuje všechny datové sady. |
> | Akce | Microsoft.DataFactory/datafactories/write | Vytvoří nebo aktualizuje Data Factory. |
> | Akce | Microsoft.DataFactory/factories/cancelpipelinerun/action | Zruší kanálu spustit podle ID spuštění. |
> | Akce | Microsoft.DataFactory/factories/datasets/delete | Odstraní všechny datové sady. |
> | Akce | Microsoft.DataFactory/factories/datasets/read | Načte všechny datové sady. |
> | Akce | Microsoft.DataFactory/factories/datasets/write | Vytvoří nebo aktualizuje všechny datové sady. |
> | Akce | Microsoft.DataFactory/factories/delete | Odstraní Data Factory. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/delete | Odstraní všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Čte informace o připojení integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Přečte stav modulu Runtime integrace. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Zobrazí ověřovací klíče pro všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Získá Data monitorování pro všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Odstraní uzel pro zadaný integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Vrátí IP adresu pro zadaný uzel integrace modulu runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualizuje vlastním hostováním uzlu integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/read | Načte všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Vygeneruje ověřovací klíče pro zadaný integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/start/action | Spustí všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Zastaví všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronizuje se přihlašovací údaje pro zadaný integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Upgraduje zadaný integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/integrationruntimes/write | Vytvoří nebo aktualizuje všechny integrace modulu Runtime. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/delete | Odstranění propojená služba. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/read | Čtení propojená služba. |
> | Akce | Microsoft.DataFactory/factories/linkedServices/write | Vytvořit nebo aktualizovat propojené služby |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Přečte že spuštění aktivity pro zadaný kanál spustit ID. |
> | Akce | Microsoft.DataFactory/factories/pipelineruns/read | Přečte spustí kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/createrun/action | Vytvoří spustit pro kanál. |
> | Akce | Microsoft.DataFactory/factories/pipelines/delete | Odstraní kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/read | Přečte kanálu. |
> | Akce | Microsoft.DataFactory/factories/pipelines/write | Vytvořit nebo aktualizovat kanál |
> | Akce | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro objekty Factory |
> | Akce | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro objekty Factory |
> | Akce | Microsoft.DataFactory/factories/read | Čte Data Factory. |
> | Akce | Microsoft.DataFactory/factories/triggers/delete | Odstraní všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/read | Načte všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/start/action | Spustí všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/stop/action | Zastaví všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/triggerruns/read | Přečte spustí aktivační události. |
> | Akce | Microsoft.DataFactory/factories/triggers/write | Vytvoří nebo aktualizuje všechny aktivační události. |
> | Akce | Microsoft.DataFactory/factories/write | Vytvořit nebo aktualizovat objekt pro vytváření dat |
> | Akce | Microsoft.DataFactory/register/action | Zaregistruje předplatné pro poskytovatele prostředků Data Factory. |
> | Akce | Microsoft.DataFactory/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Odstraníte zásadu výpočty. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Získáte informace o výpočetní zásad. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Vytvořit nebo aktualizovat zásadu výpočty. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Zrušit propojení účtu DataLakeStore z DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Získání informací o propojeném účtu DataLakeStore DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Vytvořit nebo aktualizovat účet propojené DataLakeStore DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/delete | Odstranění účtu DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Odstraňte pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Získání informací o pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Vytvořit nebo aktualizovat pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Získání výsledku operace DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Získáte nastavení diagnostiky pro DataLakeAnalytics účet. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvořit nebo aktualizovat nastavení diagnostiky pro DataLakeAnalytics účet. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | Získáte dostupné protokoly pro účet DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Získáte dostupné metriky pro účet DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/read | Získání informací o stávající účet DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Zobrazí seznam tokeny SAS pro kontejnery úložiště DataLakeAnalytics účtu propojeného účtu úložiště. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Získáte kontejnery DataLakeAnalytics účtu propojeného účtu úložiště. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Zrušit propojení účtu úložiště z účtu DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Získání informací o propojený účet úložiště DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Vytvořit nebo aktualizovat účet úložiště propojené DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udělení oprávnění k zrušení úlohy, odeslané jinými uživateli. |
> | Akce | Microsoft.DataLakeAnalytics/accounts/write | Vytvořit nebo aktualizovat účet DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/capability/read | Získání informací o odběru týkající se používání DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Zkontrolujte dostupnost názvu účtu DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/locations/operationResults/read | Získání výsledku operace DataLakeAnalytics účtu. |
> | Akce | Microsoft.DataLakeAnalytics/operations/read | Získáte dostupné operace DataLakeAnalytics. |
> | Akce | Microsoft.DataLakeAnalytics/register/action | Zaregistrujte předplatné DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DataLakeStore/accounts/delete | Odstranění účtu DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Povolte KeyVault DataLakeStore účet. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Odstraňte filtr EventGrid. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Získá filtr EventGrid. |
> | Akce | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Vytvořit nebo aktualizovat filtr EventGrid. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/delete | Odstraňte pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/read | Získání informací o pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeStore/accounts/firewallRules/write | Vytvořit nebo aktualizovat pravidlo brány firewall. |
> | Akce | Microsoft.DataLakeStore/accounts/operationResults/read | Získání výsledku operace DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Získáte nastavení diagnostiky pro DataLakeStore účet. |
> | Akce | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvořit nebo aktualizovat nastavení diagnostiky pro DataLakeStore účet. |
> | Akce | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | Získáte dostupné protokoly pro účet DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Získáte dostupné metriky pro účet DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/accounts/read | Získání informací o stávající účet DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/accounts/Superuser/action | Udělit superuživatel v Data Lake Store s Microsoft.Authorization/roleAssignments/write udělení. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Odstraňte zprostředkovatele důvěryhodné identity. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Získáte informace o zprostředkovateli důvěryhodné identity. |
> | Akce | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Vytvořit nebo aktualizovat zprostředkovatele důvěryhodné identity. |
> | Akce | Microsoft.DataLakeStore/accounts/write | Vytvořit nebo aktualizovat účet DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/capability/read | Získání informací o odběru týkající se používání DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Zkontrolujte dostupnost názvu účtu DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/locations/operationResults/read | Získání výsledku operace DataLakeStore účtu. |
> | Akce | Microsoft.DataLakeStore/operations/read | Získáte dostupné operace DataLakeStore. |
> | Akce | Microsoft.DataLakeStore/register/action | Zaregistrujte předplatné DataLakeStore. |

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

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DBforMySQL/locations/performanceTiers/read | Vrátí seznam úrovně výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMySQL/performanceTiers/read | Vrátí seznam úrovně výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforMySQL/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforMySQL/servers/firewallRules/delete | Odstraní existující pravidlo brány firewall. |
> | Akce | Microsoft.DBforMySQL/servers/firewallRules/read | Vrátí seznam brány firewall pravidla pro server nebo získá vlastnostech pro pravidlo brány firewall zadaný. |
> | Akce | Microsoft.DBforMySQL/servers/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci existujícího pravidla. |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá disagnostic nastavení pro prostředek. |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforMySQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforMySQL/servers/recoverableServers/read | Vrátí informace o obnovitelných MySQL serveru |
> | Akce | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Vrátí seznam virtuální sítě pravidla nebo získá vlastnosti pro pravidlo zadané virtuální síti. |
> | Akce | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální síti. |
> | Akce | Microsoft.DBforMySQL/servers/write | Vytvoří server, se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Vrátí seznam úrovně výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforPostgreSQL/performanceTiers/read | Vrátí seznam úrovně výkonu, které jsou k dispozici. |
> | Akce | Microsoft.DBforPostgreSQL/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Odstraní existující pravidlo brány firewall. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Vrátí seznam brány firewall pravidla pro server nebo získá vlastnostech pro pravidlo brány firewall zadaný. |
> | Akce | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci existujícího pravidla. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Získá disagnostic nastavení pro prostředek. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro Postgres servery |
> | Akce | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.DBforPostgreSQL/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Vrátí informace o obnovitelných PostgreSQL serveru |
> | Akce | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Načíst podrobnosti o zásadě detekce hrozeb server nakonfigurovaný na daném serveru |
> | Akce | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Změnit zásady detekce hrozeb serveru pro daný server |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Vrátí seznam virtuální sítě pravidla nebo získá vlastnosti pro pravidlo zadané virtuální síti. |
> | Akce | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální síti. |
> | Akce | Microsoft.DBforPostgreSQL/servers/write | Vytvoří server, se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Devices/checkNameAvailability/Action | Zkontrolujte, zda je název IotHub-li k dispozici |
> | Akce | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Zkontrolujte, zda je název IotHub-li k dispozici |
> | Akce | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Odstranit prostředek klienta IotHub |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Odstranění skupiny příjemců EventHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Získat EventHub příjemce skupiny |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Vytvoření skupiny příjemců EventHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Export zařízení |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Získá klienta IotHub statistiky prostředků |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Import zařízení |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Získá klíč tenanta IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Získání úloh Podrobnosti odeslaného na daný IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Získá klíče klienta IotHub |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Získá definice dostupné protokolu pro službu IotHub |
> | Akce | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Načte dostupné metriky pro službu IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Získat metriky kvóty |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/Read | Získá prostředek klienta IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testovací zpráva proti všechny existující trasy |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testovací zpráva proti testu zadané trasy |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Získá stav všech směrování koncové body pro platformou IotHub |
> | Akce | Microsoft.Devices/elasticPools/iotHubTenants/Write | Vytvořit nebo aktualizovat prostředek klienta IotHub |
> | Akce | Microsoft.Devices/ElasticPools/metricDefinitions/read | Načte dostupné metriky pro službu IotHub |
> | Akce | Microsoft.Devices/iotHubs/certificates/Delete | Odstranění certifikátu |
> | Akce | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generovat ověřovací kód |
> | Akce | Microsoft.Devices/iotHubs/certificates/Read | Získá certifikát |
> | Akce | Microsoft.Devices/iotHubs/certificates/verify/Action | Ověření certifikátu prostředků |
> | Akce | Microsoft.Devices/iotHubs/certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Akce | Microsoft.Devices/iotHubs/Delete | Odstranit prostředek IotHub |
> | Akce | Microsoft.Devices/IotHubs/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/IotHubs/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Odstraní filtr událostí mřížky |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Read | Získá filtr událostí mřížky |
> | Akce | Microsoft.Devices/iotHubs/eventGridFilters/Write | Vytvořit nový nebo aktualizovat existující filtr událostí mřížky |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Odstranění skupiny příjemců EventHub |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Získat EventHub příjemce skupiny |
> | Akce | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Vytvoření skupiny příjemců EventHub |
> | Akce | Microsoft.Devices/iotHubs/exportDevices/Action | Export zařízení |
> | Akce | Microsoft.Devices/iotHubs/importDevices/Action | Import zařízení |
> | Akce | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Získat klíč IotHub pro daným názvem. |
> | Akce | Microsoft.Devices/iotHubs/iotHubStats/Read | Získat statistiku IotHub |
> | Akce | Microsoft.Devices/iotHubs/jobs/Read | Získání úloh Podrobnosti odeslaného na daný IotHub |
> | Akce | Microsoft.Devices/iotHubs/listkeys/Action | Získat všechny klíče IotHub |
> | Akce | Microsoft.Devices/IotHubs/logDefinitions/read | Získá definice dostupné protokolu pro službu IotHub |
> | Akce | Microsoft.Devices/IotHubs/metricDefinitions/read | Načte dostupné metriky pro službu IotHub |
> | Akce | Microsoft.Devices/iotHubs/operationresults/Read | Získat výsledek operace (zastaralé rozhraní API) |
> | Akce | Microsoft.Devices/iotHubs/quotaMetrics/Read | Získat metriky kvóty |
> | Akce | Microsoft.Devices/iotHubs/Read | Získá prostředky IotHub |
> | Akce | Microsoft.Devices/iotHubs/routing/$testall/Action | Testovací zpráva proti všechny existující trasy |
> | Akce | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testovací zpráva proti testu zadané trasy |
> | Akce | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Získá stav všech směrování koncové body pro platformou IotHub |
> | Akce | Microsoft.Devices/iotHubs/skus/Read | Získání platných SKU IotHub |
> | Akce | Microsoft.Devices/iotHubs/Write | Vytvořit nebo aktualizovat prostředek IotHub |
> | Akce | Microsoft.Devices/operationresults/Read | Získat výsledek operace |
> | Akce | Microsoft.Devices/operations/Read | Získat všechny operace ResourceProvider |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Delete | Odstranění certifikátu |
> | Akce | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generovat ověřovací kód |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Read | Získá certifikát |
> | Akce | Microsoft.Devices/provisioningServices/certificates/verify/Action | Ověření certifikátu prostředků |
> | Akce | Microsoft.Devices/provisioningServices/certificates/Write | Vytvořit nebo aktualizovat certifikát |
> | Akce | Microsoft.Devices/provisioningServices/Delete | Odstranit prostředek IotDps |
> | Akce | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Získání IotDps klíčů pro název klíče |
> | Akce | Microsoft.Devices/provisioningServices/listkeys/Action | Získání všech IotDps klíčů |
> | Akce | Microsoft.Devices/provisioningServices/logDefinitions/read | Získá definice dostupné protokolu pro službu zřizování |
> | Akce | Microsoft.Devices/provisioningServices/metricDefinitions/read | Načte dostupné metriky pro službu zřizování |
> | Akce | Microsoft.Devices/provisioningServices/operationresults/Read | Získat výsledek operace distribučních bodů |
> | Akce | Microsoft.Devices/provisioningServices/Read | Získat prostředek IotDps |
> | Akce | Microsoft.Devices/provisioningServices/skus/Read | Získání platných SKU IotDps |
> | Akce | Microsoft.Devices/provisioningServices/Write | Vytvořte prostředek IotDps |
> | Akce | Microsoft.Devices/register/action | Registrace předplatného pro poskytovatele prostředků IotHub a umožňuje vytváření prostředků IotHub |
> | Akce | Microsoft.Devices/register/action | Registrace předplatného pro poskytovatele prostředků IotHub a umožňuje vytváření prostředků IotHub |
> | Akce | Microsoft.Devices/usages/Read | Získáte podrobnosti o použití předplatné pro tohoto zprostředkovatele. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DevTestLab/labCenters/delete | Odstraňte centrech testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labCenters/read | Přečtěte si centrech testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labCenters/write | Přidat nebo změnit centrech testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Přečtěte si šablony správce prostředků azure. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generuje šablony ARM pro danou artefaktů, odešle požadovaných souborů do účtu úložiště a ověří generovaného artefaktů. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Přečtěte si artefakty. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/delete | Odstraňte artefaktů zdroje. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/read | Přečtěte si artefaktů zdroje. |
> | Akce | Microsoft.DevTestLab/labs/artifactSources/write | Přidat nebo změnit artefaktů zdroje. |
> | Akce | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Deklarace identity náhodných vymahatelných virtuálního počítače v testovacím prostředí. |
> | Akce | Microsoft.DevTestLab/labs/costs/read | Přečtěte si náklady. |
> | Akce | Microsoft.DevTestLab/labs/costs/write | Přidat nebo změnit náklady. |
> | Akce | Microsoft.DevTestLab/labs/CreateEnvironment/action | Vytvoření virtuálních počítačů v testovacím prostředí. |
> | Akce | Microsoft.DevTestLab/labs/customImages/delete | Odstraňte vlastní Image. |
> | Akce | Microsoft.DevTestLab/labs/customImages/read | Přečtěte si vlastních bitových kopií. |
> | Akce | Microsoft.DevTestLab/labs/customImages/write | Přidat nebo upravit vlastní Image. |
> | Akce | Microsoft.DevTestLab/labs/delete | Odstraňte laboratoře. |
> | Akce | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exportuje využití prostředků testovacího prostředí do účtu úložiště |
> | Akce | Microsoft.DevTestLab/labs/formulas/delete | Odstraňte vzorce. |
> | Akce | Microsoft.DevTestLab/labs/formulas/read | Přečtěte si vzorce. |
> | Akce | Microsoft.DevTestLab/labs/formulas/write | Přidat nebo upravit vzorce. |
> | Akce | Microsoft.DevTestLab/labs/galleryImages/read | Přečtěte si Galerie obrázků. |
> | Akce | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generovat identifikátor URI pro nahrávání vlastní diskové Image do testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Virtuální počítač importujte do jiné testovacím prostředí. |
> | Akce | Microsoft.DevTestLab/labs/ListVhds/action | Zobrazí seznam bitových kopií disku k dispozici pro vytvoření vlastní image. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/delete | Odstraňte notificationchannels. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Odeslat oznámení na zadané kanálu. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/read | Přečtěte si notificationchannels. |
> | Akce | Microsoft.DevTestLab/labs/notificationChannels/write | Přidat nebo změnit notificationchannels. |
> | Akce | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Vyhodnotí zásady testovacího prostředí. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/delete | Odstraňte zásady. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/read | Přečtěte si zásady. |
> | Akce | Microsoft.DevTestLab/labs/policySets/policies/write | Přidat nebo upravit zásady. |
> | Akce | Microsoft.DevTestLab/labs/read | Přečtěte si laboratoře. |
> | Akce | Microsoft.DevTestLab/labs/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/labs/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Zobrazí všechny příslušné plány |
> | Akce | Microsoft.DevTestLab/labs/schedules/read | Plánuje pro čtení. |
> | Akce | Microsoft.DevTestLab/labs/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/delete | Odstraňte závodníky služby. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/read | Přečtěte si závodníky služby. |
> | Akce | Microsoft.DevTestLab/labs/serviceRunners/write | Přidat nebo změnit závodníky služby. |
> | Akce | Microsoft.DevTestLab/labs/users/delete | Odstraňte profily uživatelů. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/Attach/action | Připojit a vytvořit zapůjčení disku k virtuálnímu počítači. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/delete | Odstraňte disky. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/Detach/action | Odpojte a rozdělit zapůjčení disk připojený k virtuálnímu počítači. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/read | Přečtěte si disky. |
> | Akce | Microsoft.DevTestLab/labs/users/disks/write | Přidat nebo změnit disky. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/read | Přečtěte si prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/environments/write | Přidat nebo změnit prostředí. |
> | Akce | Microsoft.DevTestLab/labs/users/read | Číst profily uživatelů. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/delete | Odstraňte tajných klíčů. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/read | Čtení tajných klíčů. |
> | Akce | Microsoft.DevTestLab/labs/users/secrets/write | Přidat nebo změnit tajných klíčů. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Odstraňte service Fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Zobrazí všechny příslušné plány |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Prostředky infrastruktury služby pro čtení. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Plánuje pro čtení. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Spusťte service fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zastavit service fabric |
> | Akce | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Přidat nebo změnit service Fabric. |
> | Akce | Microsoft.DevTestLab/labs/users/write | Přidat nebo upravit profily uživatelů. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Připojte nové nebo stávající datový disk k virtuálnímu počítači. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Artefakty se vztahují na virtuální počítač. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Převzetí vlastnictví existujícího virtuálního počítače |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/delete | Odstraňte virtuální počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Zadaný disk z virtuálního počítače odpojte. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Zobrazí všechny příslušné plány |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/read | Přečtěte si virtuálních počítačů. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Znovu nasadit virtuální počítač |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Změnit velikost virtuálního počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Restartování virtuálního počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Plánuje pro čtení. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Přidat nebo změnit plány. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Spuštění virtuálního počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Zastavit virtuální počítač |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Převést vlastnictví datových disků virtuálního počítače na sami |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Verze vlastnictví existujícího virtuálního počítače |
> | Akce | Microsoft.DevTestLab/labs/virtualMachines/write | Umožňuje přidat nebo upravit virtuální počítače. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/delete | Odstraňte virtuální sítě. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/read | Čtení virtuální sítě. |
> | Akce | Microsoft.DevTestLab/labs/virtualNetworks/write | Přidat nebo upravit virtuální sítě. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/delete | Virtuální počítač fondy odstraňte. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/read | Přečtěte si fondy virtuálních počítačů. |
> | Akce | Microsoft.DevTestLab/labs/vmPools/write | Přidat nebo změnit fondy virtuálních počítačů. |
> | Akce | Microsoft.DevTestLab/labs/write | Přidat nebo změnit laboratoře. |
> | Akce | Microsoft.DevTestLab/locations/operations/read | Operace čtení. |
> | Akce | Microsoft.DevTestLab/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.DevTestLab/schedules/delete | Odstraňte plány. |
> | Akce | Microsoft.DevTestLab/schedules/Execute/action | Spuštění plánu. |
> | Akce | Microsoft.DevTestLab/schedules/read | Plánuje pro čtení. |
> | Akce | Microsoft.DevTestLab/schedules/Retarget/action | Aktualizace plán cílový prostředek ID. |
> | Akce | Microsoft.DevTestLab/schedules/write | Přidat nebo změnit plány. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DocumentDB/databaseAccountNames/read | Zkontroluje dostupnost názvu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Změnit skupinu prostředků účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Načte kolekci definice metrik. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Načte kolekci metriky. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Čtení databáze účet oddílu klíčové metriky na úrovni |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Číst databáze účet oddílu úrovně metriky |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Číst účet oddíly databáze v kolekci |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Číst účet databáze úrovně použití oddílu |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Načte kolekci použití. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Čte databázi definice metrik |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Přečte metriky databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Přečte použití databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/delete | Odstraní účty databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Změnit priorit převzetí služeb při selhání oblastí databázového účtu. To se používá k provedení operace ruční převzetí služeb při selhání |
> | Akce | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Získat připojovací řetězce pro databázový účet |
> | Akce | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Seznam klíčů účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Čte databázi definice metrik účtu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/metrics/read | Přečte metriky účtu databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline oblasti databázového účtu.  |
> | Akce | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online oblasti databázového účtu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Stav asynchronní operace čtení |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metriky latenci pro čtení |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/read | Přečtěte si percentily latenci replikace |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Číst metriky latence pro konkrétní oblasti zdroje a cíle |
> | Akce | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Číst metriky latence pro konkrétní cíl oblast |
> | Akce | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Získá catageries dostupné protokolů pro účet databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro databázi účtu |
> | Akce | Microsoft.DocumentDB/databaseAccounts/read | Přečte databázového účtu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Čte databázi, klíče účtu jen pro čtení. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Čte databázi, klíče účtu jen pro čtení. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Otočit klíče účtu databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Přečte metriky místní kolekce. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Přečtěte si místní databáze účet oddílu klíčové metriky na úrovni |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Číst metriky úrovně oddílu v účtu místní databáze |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Přečtěte si místní databáze účet oddíly v kolekci |
> | Akce | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Přečte účet metriky oblast a databáze. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/usages/read | Čte databázi použití účtu. |
> | Akce | Microsoft.DocumentDB/databaseAccounts/write | Aktualizace databáze účtů. |
> | Akce | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.DocumentDB upozorní, že se teď odstraňuje virtuální síť nebo podsíť |
> | Akce | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Stav deleteVirtualNetworkOrSubnets asynchronní operace čtení |
> | Akce | Microsoft.DocumentDB/operationResults/read | Stav asynchronní operace čtení |
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
> | Akce | Microsoft.DomainRegistration/domains/Read | Získat domény |
> | Akce | Microsoft.DomainRegistration/domains/renew/Action | Obnovte existující domény. |
> | Akce | Microsoft.DomainRegistration/domains/Write | Přidat novou doménu nebo aktualizovat stávající |
> | Akce | Microsoft.DomainRegistration/generateSsoRequest/Action | Generovat žádost o přihlášení k doméně řídicí centrum. |
> | Akce | Microsoft.DomainRegistration/listDomainRecommendations/Action | Načtení seznamu domény doporučení na základě klíčových slov |
> | Akce | Microsoft.DomainRegistration/operations/Read | Zobrazí seznam všech operací z registraci domény aplikace služby |
> | Akce | Microsoft.DomainRegistration/register/action | Registrace poskytovatele prostředků Microsoft Domains pro předplatné |
> | Akce | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Seznam smlouvu akce |
> | Akce | Microsoft.DomainRegistration/topLevelDomains/Read | Získat toplevel domény |
> | Akce | Microsoft.DomainRegistration/topLevelDomains/Read | Získat toplevel domény |
> | Akce | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Ověření objektu nákupu domény bez odeslání |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | V projektu služby Microsoft Dynamics životního cyklu, které patří uživateli zobrazit Microsoft Dynamics AX 2012 R3 vyhodnocení nasazení |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Vytvořte Microsoft Dynamics AX 2012 R3 vyhodnocení nasazení v projektu služby Microsoft Dynamics životního cyklu, které patří uživateli. Nasazení lze provádět z portálu správy Azure |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Konektory pro čtení, které patří do služby Microsoft Dynamics životního cyklu projektu |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Vytváření a aktualizaci konektory, které patří do služby Microsoft Dynamics životního cyklu projektu |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/delete | Odstranění služby životního cyklu aplikace Microsoft Dynamics projekty, které patří uživateli |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/read | Zobrazení služby životního cyklu aplikace Microsoft Dynamics projektů, které patří uživateli |
> | Akce | Microsoft.DynamicsLcs/lcsprojects/write | Vytvářet a aktualizovat projekty služby Microsoft Dynamics životního cyklu, které patří uživateli. Lze aktualizovat pouze vlastnosti název a popis. Po vytvoření nelze aktualizovat předplatné a umístění přidružený k projektu |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.EventGrid/eventSubscriptions/delete | Odstranit eventSubscription |
> | Akce | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Získat úplnou adresu url odběru událostí |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro odběry událostí |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro odběry událostí |
> | Akce | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro eventSubscriptions |
> | Akce | Microsoft.EventGrid/eventSubscriptions/read | Čtení eventSubscription |
> | Akce | Microsoft.EventGrid/eventSubscriptions/write | Vytvořit nebo aktualizovat eventSubscription |
> | Akce | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro témata |
> | Akce | Microsoft.EventGrid/register/action | Zaregistruje eventSubscription pro zprostředkovatele prostředků EventGrid a povolí vytvoření odběrů událostí mřížky. |
> | Akce | Microsoft.EventGrid/topics/delete | Odstranit téma |
> | Akce | Microsoft.EventGrid/topics/listKeys/action | Seznam klíčů pro téma |
> | Akce | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro témata |
> | Akce | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro témata |
> | Akce | Microsoft.EventGrid/topics/read | Přečtěte si téma |
> | Akce | Microsoft.EventGrid/topics/regenerateKey/action | Znovu generovat klíč pro téma |
> | Akce | Microsoft.EventGrid/topics/write | Vytvořit nebo aktualizovat téma |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.EventHub/checkNameAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. |
> | Akce | Microsoft.EventHub/checkNamespaceAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. místo toho použijte CheckNameAvailabiltiy. |
> | Akce | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Získání seznamu clusteru metrik popisy prostředků |
> | Akce | Microsoft.EventHub/clusters/read | Získá popis prostředku clusteru. |
> | Akce | Microsoft.EventHub/clusters/write | Získá popis prostředku clusteru. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/action | Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je depricated. Se aktualizovat autorizační pravidlo Namespace místo toho použijte volání PUT... Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/delete | Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.EventHub/namespaces/authorizationRules/write | Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.EventHub/namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Získá klíče pravidel ověřování pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat pravidla ověřování primárního oboru názvů zotavení po havárii |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních. |
> | Akce | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Odstraní konfigurace zotavení po havárii přidružené k oboru názvů. Tato operace může volat jenom přes primární obor názvů. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operace k aktualizaci EventHub. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operace odstranění EventHub autorizační pravidla |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Získat připojovací řetězec k centru EventHub |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Získání seznamu EventHub autorizační pravidla |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Centrum EventHub autorizační pravidla vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat. |
> | Akce | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operace odstranění ConsumerGroup prostředků |
> | Akce | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Získání seznamu popisů ConsumerGroup prostředků |
> | Akce | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Vytvoření nebo aktualizace ConsumerGroup vlastnosti. |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/Delete | Operace odstranění EventHub prostředků |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/read | Získání seznamu popisů EventHub prostředků |
> | Akce | Microsoft.EventHub/namespaces/eventhubs/write | Vytvoření nebo aktualizace EventHub vlastnosti. |
> | Akce | Microsoft.EventHub/namespaces/messagingPlan/read | Získá plánování zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralá.<br>Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API...<br>Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.EventHub/namespaces/messagingPlan/write | Aktualizace, zasílání zpráv plánování pro obor názvů.<br>Toto rozhraní API je zastaralá.<br>Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API...<br>Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.EventHub/namespaces/operationresults/read | Získá stav operace Namespace. |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Získání seznamu protokolů Namespace popisy prostředků |
> | Akce | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získání seznamu Namespace metrik popisy prostředků |
> | Akce | Microsoft.EventHub/namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Odebrat obor názvů služby ACS |
> | Akce | Microsoft.EventHub/namespaces/write | Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.EventHub/operations/read | Získejte operace |
> | Akce | Microsoft.EventHub/register/action | Zaregistruje předplatné pro poskytovatele prostředků EventHub a povolí vytváření prostředků EventHub. |
> | Akce | Microsoft.EventHub/sku/read | Získání seznamu popisů Sku prostředků |
> | Akce | Microsoft.EventHub/sku/regions/read | Získání seznamu popisů SkuRegions prostředků |
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

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.HDInsight/clusters/changerdpsetting/action | Změna nastavení protokolu RDP pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/configurations/action | Aktualizace konfigurace clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/configurations/read | Získání konfigurace clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/delete | Odstranění clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro prostředek clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro HDInsight Cluster |
> | Akce | Microsoft.HDInsight/clusters/read | Získejte podrobnosti o clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/roles/resize/action | Změnit velikost clusteru HDInsight |
> | Akce | Microsoft.HDInsight/clusters/write | Vytvořit nebo aktualizovat HDInsight Cluster |
> | Akce | Microsoft.HDInsight/locations/capabilities/read | Získat možnosti předplatného |
> | Akce | Microsoft.HDInsight/locations/checkNameAvailability/read | Zkontrolovat dostupnost názvu |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ImportExport/jobs/delete | Odstraní stávající úloze. |
> | Akce | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Získá klíče nástroje BitLocker pro zadanou úlohu. |
> | Akce | Microsoft.ImportExport/jobs/read | Získá vlastnosti pro zadanou úlohu nebo vrátí seznam úloh. |
> | Akce | Microsoft.ImportExport/jobs/write | Vytvoří úlohu se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadanou úlohu. |
> | Akce | Microsoft.ImportExport/locations/read | Získá vlastnosti pro zadané umístění nebo vrátí seznam umístění. |
> | Akce | Microsoft.ImportExport/register/action | Zaregistruje předplatné pro poskytovatele prostředků importu a exportu a umožňuje vytvoření úlohy importu a exportu. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Insights/ActionGroups/Delete | Odstranění skupiny akcí |
> | Akce | Microsoft.Insights/ActionGroups/Read | Načtení skupiny akcí |
> | Akce | Microsoft.Insights/ActionGroups/Write | Zápis skupiny akcí |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivovalo se upozornění protokolu aktivit. |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Delete | Odstranění upozornění protokolu aktivit |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Read | Načtení upozornění protokolu aktivit |
> | Akce | Microsoft.Insights/ActivityLogAlerts/Write | Načtení upozornění protokolu aktivit |
> | Akce | Microsoft.Insights/AlertRules/Activated/Action | Pravidlo výstrahy je aktivované. |
> | Akce | Microsoft.Insights/AlertRules/Delete | Odstraňuje se konfigurace pravidla výstrahy. |
> | Akce | Microsoft.Insights/AlertRules/Incidents/Read | Čte se konfigurace incidentu pravidla výstrahy. |
> | Akce | Microsoft.Insights/AlertRules/Read | Čte se konfigurace pravidla výstrahy. |
> | Akce | Microsoft.Insights/AlertRules/Resolved/Action | Pravidlo výstrahy je vyřešené. |
> | Akce | Microsoft.Insights/AlertRules/Throttled/Action | Pravidlo výstrahy je omezené. |
> | Akce | Microsoft.Insights/AlertRules/Write | Zapisuje se do konfigurace pravidla výstrahy. |
> | Akce | Microsoft.Insights/AutoscaleSettings/Delete | Odstraňuje se konfigurace nastavení automatického škálování. |
> | Akce | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/AutoscaleSettings/Read | Čte se konfigurace nastavení automatického škálování. |
> | Akce | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Operace automatického vertikálního snížení kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Operace automatického vertikálního navýšení kapacity |
> | Akce | Microsoft.Insights/AutoscaleSettings/Write | Zapisuje se do konfigurace nastavení automatického škálování. |
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
> | Akce | Microsoft.Insights/DiagnosticSettings/Delete | Odstraňuje se konfigurační nastavení diagnostiky. |
> | Akce | Microsoft.Insights/DiagnosticSettings/Read | Čte se konfigurační nastavení diagnostiky. |
> | Akce | Microsoft.Insights/DiagnosticSettings/Write | Zapisuje se do konfiguračního nastavení diagnostiky. |
> | Akce | Microsoft.Insights/EventCategories/Read | Načtení kategorie události |
> | Akce | Microsoft.Insights/eventtypes/digestevents/Read | Číst výtah typů událostí správy |
> | Akce | Microsoft.Insights/eventtypes/values/Read | Číst hodnoty typů událostí správy |
> | Akce | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Odstraňuje se konfigurace rozšířeného nastavení diagnostiky. |
> | Akce | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Načítá se konfigurace rozšířeného nastavení diagnostiky. |
> | Akce | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Zapisuje se konfigurace rozšířeného nastavení diagnostiky. |
> | Akce | Microsoft.Insights/ListMigrationDate/Action | Získá datum migrace předplatného. |
> | Akce | Microsoft.Insights/ListMigrationDate/Read | Získá datum migrace předplatného. |
> | Akce | Microsoft.Insights/LogDefinitions/Read | Číst definice protokolů |
> | Akce | Microsoft.Insights/LogProfiles/Delete | Odstraní konfiguraci profilů protokolů. |
> | Akce | Microsoft.Insights/LogProfiles/Read | Přečte profily protokolů. |
> | Akce | Microsoft.Insights/LogProfiles/Write | Provede zápis do konfigurace profilu protokolu. |
> | Akce | Microsoft.Insights/MetricAlerts/Delete | Odstranění upozornění metriky |
> | Akce | Microsoft.Insights/MetricAlerts/Read | Načtení upozornění metriky |
> | Akce | Microsoft.Insights/MetricAlerts/Status/Read | Načítá se stav upozornění metriky. |
> | Akce | Microsoft.Insights/MetricAlerts/Write | Zápis upozornění metriky |
> | Akce | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/MetricDefinitions/Read | Číst definice metrik |
> | Akce | Microsoft.Insights/Metrics/providers/Metrics/Read | Čtení metrik |
> | Akce | Microsoft.Insights/Metrics/Read | Čtení metrik |
> | Akce | Microsoft.Insights/Metrics/Write | Zapíše metriky. |
> | Akce | Microsoft.Insights/MigrateToNewpricingModel/Action | Migruje předplatné na nový cenový model. |
> | Akce | Microsoft.Insights/Operations/Read | Načtení operací |
> | Akce | Microsoft.Insights/Register/Action | Registrovat poskytovatele platformy Microsoft Insights |
> | Akce | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Vrátí předplatné na starší verzi cenového modelu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Delete | Odstraňuje se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Read | Načítá se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/ScheduledQueryRules/Write | Zapisuje se pravidlo naplánovaného dotazu. |
> | Akce | Microsoft.Insights/Tenants/Register/Action | Inicializuje poskytovatele platformy Microsoft Insights. |
> | Akce | Microsoft.Insights/Unregister/Action | Registrovat poskytovatele platformy Microsoft Insights |
> | Akce | Microsoft.Insights/Webtests/Delete | Odstraní konfiguraci webového testu. |
> | Akce | Microsoft.Insights/Webtests/GetToken/Read | Čtení tokenu webového testu |
> | Akce | Microsoft.Insights/Webtests/MetricDefinitions/Read | Čtení definic metrik webového testu |
> | Akce | Microsoft.Insights/Webtests/Metrics/Read | Čtení metriky webového testu |
> | Akce | Microsoft.Insights/Webtests/Read | Přečte konfiguraci webového testu. |
> | Akce | Microsoft.Insights/Webtests/Write | Provede zápis do konfigurace webového testu. |

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
> | Akce | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Načte dostupné protokoly pro trezor klíčů. |
> | Akce | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro trezor klíčů. |
> | Akce | Microsoft.KeyVault/vaults/read | Zobrazí vlastnosti trezoru klíčů. |
> | Akce | Microsoft.KeyVault/vaults/secrets/read | Zobrazí vlastnosti tajného kódu, ale ne jeho hodnotu. |
> | Akce | Microsoft.KeyVault/vaults/secrets/write | Vytvoří nový tajný kód nebo aktualizuje hodnotu existujícího tajného kódu. |
> | Akce | Microsoft.KeyVault/vaults/write | Vytvoří nový trezor klíčů nebo aktualizuje vlastnosti existujícího trezoru klíčů. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.LabServices/labAccounts/CreateLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/delete | Odstraňte účty testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/delete | Odstraňte laboratoře. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Deklarace identity náhodných prostředí pro uživatele v nastavení prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Odstraňte nastavení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Deklarace identity prostředí a přiřadí ji k uživateli |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Přečtěte si prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Spustí se prostředí spuštěním všechny prostředky v prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Zastaví prostředí tím zastavíte všechny prostředky v prostředí |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Přidat nebo změnit prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Zřizuje/deprovisions požadované prostředky pro nastavení prostředí na základě aktuálního stavu nastavení testovacím prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Nastavení prostředí pro čtení. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Spustí šablonu spuštěním všechny prostředky v šabloně. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Spustí šablonu spuštěním všechny prostředky v šabloně. |
> | Akce | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Přidat nebo změnit nastavení prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/read | Přečtěte si laboratoře. |
> | Akce | Microsoft.LabServices/labAccounts/labs/Register/action | Zaregistrovat do spravovaného testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/delete | Odstraňte uživatele. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/read | Přečtěte si uživatelé. |
> | Akce | Microsoft.LabServices/labAccounts/labs/users/write | Přidat nebo změnit uživatele. |
> | Akce | Microsoft.LabServices/labAccounts/labs/write | Přidat nebo změnit laboratoře. |
> | Akce | Microsoft.LabServices/labAccounts/read | Přečtěte si účty testovacího prostředí. |
> | Akce | Microsoft.LabServices/labAccounts/sizes/read | Přečtěte si velikosti. |
> | Akce | Microsoft.LabServices/labAccounts/write | Přidat nebo změnit účty testovacího prostředí. |
> | Akce | Microsoft.LabServices/locations/operations/read | Operace čtení. |
> | Akce | Microsoft.LabServices/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.LabServices/users/GetEnvironment/action | Získá podrobnosti virtuálního počítače |
> | Akce | Microsoft.LabServices/users/GetOperationStatus/action | Získá stav operace probíhající dlouhou dobu |
> | Akce | Microsoft.LabServices/users/ListEnvironments/action | Seznam prostředí pro uživatele |
> | Akce | Microsoft.LabServices/users/ListLabs/action | Zobrazí seznam labs pro uživatele. |
> | Akce | Microsoft.LabServices/users/Register/action | Registraci uživatele do spravovaného testovacího prostředí |
> | Akce | Microsoft.LabServices/users/StartEnvironment/action | Spustí se prostředí spuštěním všechny prostředky v prostředí. |
> | Akce | Microsoft.LabServices/users/StopEnvironment/action | Zastaví prostředí tím zastavíte všechny prostředky v prostředí |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.LocationBasedServices/accounts/delete | (Nepoužívané: použijte /providers/Microsoft.Maps) Odstranit umístění na základě účet služeb. |
> | Akce | Microsoft.LocationBasedServices/accounts/listKeys/action | (Nepoužívané: použijte /providers/Microsoft.Maps) Seznam klíčů k účtu služeb na základě umístění |
> | Akce | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Nepoužívané: použijte /providers/Microsoft.Maps) Získá nastavení diagnostiky pro prostředek |
> | Akce | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Nepoužívané: použijte /providers/Microsoft.Maps) Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek |
> | Akce | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Nepoužívané: použijte /providers/Microsoft.Maps) Načte dostupné metriky pro účty služeb na základě umístění |
> | Akce | Microsoft.LocationBasedServices/accounts/read | (Nepoužívané: použijte /providers/Microsoft.Maps) Při získávání umístění, na základě účet služeb. |
> | Akce | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Nepoužívané: použijte /providers/Microsoft.Maps) Generovat nové primární nebo sekundární klíč účtu služeb na základě umístění |
> | Akce | Microsoft.LocationBasedServices/accounts/write | (Nepoužívané: použijte /providers/Microsoft.Maps) Vytvořit nebo aktualizovat účet služeb na základě umístění. |
> | Akce | Microsoft.LocationBasedServices/register/action | (Nepoužívané: použijte /providers/Microsoft.Maps) Zaregistrujte zprostředkovatele |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Čtení dat z tabulky ADAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Čtení dat z tabulky ADReplicationResult |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Čtení dat z tabulky ADSecurityAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Čtení dat z tabulky výstrahy |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Čtení dat z tabulky AlertHistory |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Čtení dat z tabulky ApplicationInsights |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Čtení dat z tabulky AzureActivity |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Čtení dat z tabulky AzureMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Čtení dat z tabulky BoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Čtení dat z tabulky CommonSecurityLog |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Čtení dat z tabulky ComputerGroup |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Čtení dat z tabulky ConfigurationChange |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Čtení dat z tabulky ConfigurationData |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Čtení dat z tabulky ContainerImageInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Čtení dat z tabulky ContainerInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Čtení dat z tabulky ContainerLog |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Čtení dat z tabulky ContainerServiceLog |
> | DataAction | Microsoft.LogAnalytics/logs/CustomLogs/read | Čtení dat z jakékoli vlastního protokolu |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Čtení dat z tabulky DeviceAppCrash |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Čtení dat z tabulky DeviceAppLaunch |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Čtení dat z tabulky DeviceCalendar |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Čtení dat z tabulky DeviceCleanup |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Čtení dat z tabulky DeviceConnectSession |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Čtení dat z tabulky DeviceEtw |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Čtení dat z tabulky DeviceHardwareHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Čtení dat z tabulky DeviceHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Čtení dat z tabulky DeviceHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Čtení dat z tabulky DeviceSkypeHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Čtení dat z tabulky DeviceSkypeSignIn |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Čtení dat z tabulky DeviceSleepState |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Čtení dat z tabulky DHAppFailure |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Čtení dat z tabulky DHAppReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Čtení dat z tabulky DHDriverReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Čtení dat z tabulky DHLogonFailures |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Čtení dat z tabulky DHLogonMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Čtení dat z tabulky DHOSCrashData |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Čtení dat z tabulky DHOSReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Čtení dat z tabulky DHWipAppLearning |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Čtení dat z tabulky DnsEvents |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Čtení dat z tabulky DnsInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Čtení dat z tabulky ETWEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Čtení dat z tabulky události |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Čtení dat z tabulky ExchangeAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Čtení dat z tabulky prezenčního signálu |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Čtení dat z tabulky IISAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Čtení dat z tabulky InboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Čtení dat z tabulky KubeNodeInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Čtení dat z tabulky KubePodInventory |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Čtení dat z tabulky LinuxAuditLog |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Čtení dat z tabulky MAApplication |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Čtení dat z tabulky MAApplicationHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Čtení dat z tabulky MAApplicationInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Čtení dat z tabulky MAApplicationReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Čtení dat z tabulky MADeploymentPlan |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Čtení dat z tabulky MADevice |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Čtení dat z tabulky MADeviceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Čtení dat z tabulky MADriverReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Čtení dat z tabulky MAOfficeAddin |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Čtení dat z tabulky MAOfficeAddinHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Čtení dat z tabulky MAOfficeAddinInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Čtení dat z tabulky MAOfficeAddinReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Čtení dat z tabulky MAOfficeApp |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Čtení dat z tabulky MAOfficeAppHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Čtení dat z tabulky MAOfficeAppInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Čtení dat z tabulky MAOfficeAppReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Čtení dat z tabulky MAOfficeBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Čtení dat z tabulky MAOfficeCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Čtení dat z tabulky MAOfficeCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Čtení dat z tabulky MAOfficeDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Čtení dat z tabulky MAOfficeMacroIssueReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Čtení dat z tabulky MAOfficeMacroSummary |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Čtení dat z tabulky MAOfficeSuite |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Čtení dat z tabulky MAOfficeSuiteInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Čtení dat z tabulky MAProposedPilotDevices |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Čtení dat z tabulky MAWindowsBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Čtení dat z tabulky MAWindowsCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Čtení dat z tabulky MAWindowsCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Čtení dat z tabulky MAWindowsDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Čtení dat z tabulky NetworkMonitoring |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Čtení dat z tabulky OfficeActivity |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Čtení dat z tabulky operace |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Čtení dat z tabulky OutboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Čtení dat z tabulky výkonu |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Čtení dat z tabulky ProtectionStatus |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Čtení dat z tabulky ReservedAzureCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Čtení dat z tabulky ReservedCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Čtení dat z tabulky SCCMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Čtení dat z tabulky SCOMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Čtení dat z tabulky SecurityAlert |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Čtení dat z tabulky SecurityBaseline |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Čtení dat z tabulky SecurityBaselineSummary |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Čtení dat z tabulky SecurityDetection |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Čtení dat z tabulky SecurityEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Čtení dat z tabulky ServiceFabricOperationalEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Čtení dat z tabulky ServiceFabricReliableActorEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Čtení dat z tabulky ServiceFabricReliableServiceEvent |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Čtení dat z tabulky SfBAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Čtení dat z tabulky SPAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Čtení dat z tabulky SQLAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Čtení dat z tabulky SQLQueryPerformance |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Čtení dat z tabulky Syslog |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Čtení dat z tabulky SysmonEvent |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Čtení dat z tabulky UAApp |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Čtení dat z tabulky UAComputer |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Čtení dat z tabulky UAComputerRank |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Čtení dat z tabulky UADriver |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Čtení dat z tabulky UADriverProblemCodes |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Čtení dat z tabulky UAFeedback |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Čtení dat z tabulky UAHardwareSecurity |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Čtení dat z tabulky UAIESiteDiscovery |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Čtení dat z tabulky UAOfficeAddIn |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Čtení dat z tabulky UAProposedActionPlan |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Čtení dat z tabulky UASysReqIssue |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Čtení dat z tabulky UAUpgradedComputer |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Čtení dat z tabulky aktualizace |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Čtení dat z tabulky UpdateRunProgress |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Čtení dat z tabulky UpdateSummary |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Čtení dat z tabulky využití |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Čtení dat z tabulky W3CIISLog |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Čtení dat z tabulky WaaSDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Čtení dat z tabulky WaaSInsiderStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Čtení dat z tabulky WaaSUpdateStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Čtení dat z tabulky WDAVStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Čtení dat z tabulky WDAVThreat |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Čtení dat z tabulky WindowsClientAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Čtení dat z tabulky WindowsFirewall |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Čtení dat z tabulky WindowsServerAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Čtení dat z tabulky WireData |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Čtení dat z tabulky WUDOAggregatedStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Čtení dat z tabulky WUDOStatus |

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
> | Akce | Microsoft.Logic/isolatedEnvironments/delete | Odstraní izolované prostředí. |
> | Akce | Microsoft.Logic/isolatedEnvironments/join/action | Připojí se k izolovanému prostředí. |
> | Akce | Microsoft.Logic/isolatedEnvironments/read | Přečte izolované prostředí. |
> | Akce | Microsoft.Logic/isolatedEnvironments/write | Vytvoří nebo aktualizuje izolované prostředí. |
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
> | Akce | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Přečte workflow běžet historii žádostí o opakování akce. |
> | Akce | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Přečte pracovní postup spustit akci historii žádostí. |
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
> | Akce | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Přesunout všechny strojového učení závazků přidružení plánu |
> | Akce | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Číst všechny strojového učení závazků přidružení plánu |
> | Akce | Microsoft.MachineLearning/commitmentPlans/delete | Odstranit všechny strojového učení závazků plán |
> | Akce | Microsoft.MachineLearning/commitmentPlans/join/action | Připojte všechny strojového učení závazků plán |
> | Akce | Microsoft.MachineLearning/commitmentPlans/read | Číst všechny strojového učení závazků plán |
> | Akce | Microsoft.MachineLearning/commitmentPlans/write | Vytvořit nebo aktualizovat každého Machine Learning závazků plánu |
> | Akce | Microsoft.MachineLearning/locations/operationresults/read | Získání výsledku operace Machine Learning |
> | Akce | Microsoft.MachineLearning/locations/operationsstatus/read | Získat stav probíhající operace Machine Learning |
> | Akce | Microsoft.MachineLearning/operations/read | Získat strojového učení operací |
> | Akce | Microsoft.MachineLearning/register/action | Zaregistruje předplatné pro strojové učení zprostředkovatel prostředků webové služby a povolí vytvoření webových služeb. |
> | Akce | Microsoft.MachineLearning/skus/read | Získat strojového učení závazků plán SKU |
> | Akce | Microsoft.MachineLearning/webServices/action | Vytvořit místní vlastnosti webové služby pro podporované oblasti |
> | Akce | Microsoft.MachineLearning/webServices/delete | Odstranit všechny webová služba Machine Learning |
> | Akce | Microsoft.MachineLearning/webServices/listkeys/read | Získání klíčů k webové službě Machine Learning |
> | Akce | Microsoft.MachineLearning/webServices/read | Číst všechny webová služba Machine Learning |
> | Akce | Microsoft.MachineLearning/webServices/write | Vytvořit nebo aktualizovat žádné webové služby Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/delete | Odstranit všechny strojového učení pracovního prostoru |
> | Akce | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Seznam klíčů pro pracovní prostor Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/read | Číst všechny strojového učení pracovního prostoru |
> | Akce | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Nové synchronizace klíče účtu úložiště, které jsou nakonfigurované pro pracovní prostor Machine Learning |
> | Akce | Microsoft.MachineLearning/Workspaces/write | Vytvořit nebo aktualizovat všechny strojového učení pracovního prostoru |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Zkontrolujte, zda jsou k dispozici pro systémových služeb pro operationalization cluster aktualizace |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Odstranění všech hostitelských účtu |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Výpis klíčů přidruženého k operationalization clusteru |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/read | Číst libovolného hostování účtu |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Aktualizace systémových služeb v clusteru služby operationalization |
> | Akce | Microsoft.MachineLearningCompute/operationalizationClusters/write | Vytvořit nebo aktualizovat všechny hostování účtu |
> | Akce | Microsoft.MachineLearningCompute/register/action | Zaregistruje ID předplatného pro poskytovatele prostředků a povolí vytvoření strojového učení výpočetní prostředky |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningModelManagement/accounts/delete | Odstranění všech hostitelských účtu |
> | Akce | Microsoft.MachineLearningModelManagement/accounts/read | Číst libovolného hostování účtu |
> | Akce | Microsoft.MachineLearningModelManagement/accounts/write | Vytvořit nebo aktualizovat všechny hostování účtu |
> | Akce | Microsoft.MachineLearningModelManagement/register/action | Zaregistruje ID předplatného pro poskytovatele prostředků a povolí vytvoření hostování účtu |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MachineLearningServices/register/action | Zaregistruje předplatné pro zprostředkovatele prostředků služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/delete | Odstraní výpočetní prostředky v Machine Learning služby pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Výpis tajných klíčů pro výpočetní prostředky v prostoru služby Machine Learning |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/read | Získá výpočetní prostředky v Machine Learning služby pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/computes/write | Vytvoří nebo aktualizuje výpočetní prostředky v Machine Learning služby pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/delete | Odstraní strojového učení služby pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/listKeys/action | Výpis tajných klíčů pro pracovní prostor Machine Learning služby |
> | Akce | Microsoft.MachineLearningServices/workspaces/read | Získá strojového učení služby pracovních prostorů |
> | Akce | Microsoft.MachineLearningServices/workspaces/write | Vytvoří nebo aktualizuje pracovních prostorů Machine Learning služby |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Akce RBAC pro přiřazení stávajícího uživatele přiřazené identity k prostředku |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Odstraní stávajícího uživatele přiřazené identity |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/read | Získá stávajícího uživatele přiřazené identity |
> | Akce | Microsoft.ManagedIdentity/userAssignedIdentities/write | Vytvoří nového uživatele přiřazené identity nebo aktualizuje značkám přidruženým stávajícího uživatele přiřazené identity |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ManagedLab/labAccounts/CreateLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/delete | Odstraňte účty testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/delete | Odstraňte laboratoře. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Odstraňte nastavení prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Odstraňte prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Přečtěte si prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Přidat nebo změnit prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Nastavení prostředí pro čtení. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Přidat nebo změnit nastavení prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Odstranění testovacího prostředí virtuálních počítačů. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Přečtěte si testovacího prostředí virtuálních počítačů. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Přidat nebo změnit prostředí virtuálních počítačů. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/read | Přečtěte si laboratoře. |
> | Akce | Microsoft.ManagedLab/labAccounts/labs/write | Přidat nebo změnit laboratoře. |
> | Akce | Microsoft.ManagedLab/labAccounts/read | Přečtěte si účty testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/labAccounts/write | Přidat nebo změnit účty testovacího prostředí. |
> | Akce | Microsoft.ManagedLab/locations/operations/read | Operace čtení. |
> | Akce | Microsoft.ManagedLab/register/action | Zaregistruje předplatné. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Management/checkNameAvailability/action | Ověří, zda je název skupiny pro správu zadané platné a jedinečný. |
> | Akce | Microsoft.Management/getEntities/action | Zobrazí seznam všech entit (skupiny pro správu, odběry atd.) pro ověřené uživatele. |
> | Akce | Microsoft.Management/managementGroups/delete | Odstraňte skupinu pro správu. |
> | Akce | Microsoft.Management/managementGroups/read | Zobrazí seznam skupin pro správu pro ověřené uživatele. |
> | Akce | Microsoft.Management/managementGroups/subscriptions/delete | Přidruží zrušíte předplatné ze skupiny pro správu. |
> | Akce | Microsoft.Management/managementGroups/subscriptions/write | Partnerů existující předplatné se skupinou pro správu. |
> | Akce | Microsoft.Management/managementGroups/write | Vytvořit nebo aktualizovat skupinu pro správu. |
> | Akce | Microsoft.Management/register/action | Zaregistrovat určený odběr Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Maps/accounts/delete | Odstranění účtu služby Maps. |
> | Akce | Microsoft.Maps/accounts/listKeys/action | Klíče účtu mapy seznamu |
> | Akce | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro účty mapy |
> | Akce | Microsoft.Maps/accounts/read | Získáte účet mapy. |
> | Akce | Microsoft.Maps/accounts/regenerateKey/action | Generovat nové primární nebo sekundární klíč účtu mapy |
> | Akce | Microsoft.Maps/accounts/write | Vytvořit nebo aktualizovat účet mapy. |
> | Akce | Microsoft.Maps/register/action | Zaregistrujte zprostředkovatele |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Vrátí smlouvu. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Přijme podepsané smlouvy. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importuje bitovou kopii ACR koncového uživatele. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Vrátí konfigurace. |
> | Akce | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Uloží konfigurace. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/delete | Nepodporuje operace odstranění prostředku služby classic vývojářů. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Získá classic vývojářů služby prostředků správy klíčů. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Získá jeden přihlašovací na URL pro službu classic vývojářů. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/read | Provede operaci GET na službě classic vývojářů. |
> | Akce | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generuje classic vývojářů služby prostředků správy klíčů. |
> | Akce | Microsoft.MarketplaceApps/Operations/read | Čtení operací pro všechny typy prostředků. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Zrušit smlouvu pro položku daného webu marketplace |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Vrátí smlouvu pro položku daného webu marketplace |
> | Akce | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Podepsat smlouvu pro položku daného webu marketplace |
> | Akce | Microsoft.MarketplaceOrdering/agreements/read | Vrátí všechny smlouvy v rámci zadané předplatné |
> | Akce | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Získat smlouvu pro položku marketplace daného virtuálního počítače |
> | Akce | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Přihlášení nebo zrušte smlouvu pro položku marketplace daného virtuálního počítače |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Media/checknameavailability/action | Ověří, zda je název účtu Media Services k dispozici |
> | Akce | Microsoft.Media/locations/checkNameAvailability/action | Ověří, zda je název účtu Media Services k dispozici |
> | Akce | Microsoft.Media/mediaservices/assets/delete | Odstranit prostředek |
> | Akce | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Získat prostředek šifrovací klíč |
> | Akce | Microsoft.Media/mediaservices/assets/listContainerSas/action | Seznam Asset kontejneru SAS URL – adresy |
> | Akce | Microsoft.Media/mediaservices/assets/read | Číst prostředek |
> | Akce | Microsoft.Media/mediaservices/assets/write | Vytvořit nebo aktualizovat prostředek |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Odstranit všechny zásady pro klíče obsahu |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Získat vlastnosti zásady obsahující tajné údaje |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/read | Číst všechny zásady pro klíče obsahu |
> | Akce | Microsoft.Media/mediaservices/contentKeyPolicies/write | Vytvořit nebo aktualizovat všechny obsahu klíče zásady |
> | Akce | Microsoft.Media/mediaservices/delete | Odstranit všechny účtu Media Services |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/delete | Odstranit všechny události mřížky filtru |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/read | Číst libovolný filtr událostí mřížky |
> | Akce | Microsoft.Media/mediaservices/eventGridFilters/write | Vytvořit nebo aktualizovat libovolný filtr událostí mřížky |
> | Akce | Microsoft.Media/mediaservices/liveEventOperations/read | Číst všechny operace živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/delete | Odstranit všechny události za provozu |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Odstranit žádný výstup za provozu |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Číst žádný výstup za provozu |
> | Akce | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Vytvořit nebo aktualizovat žádný výstup za provozu |
> | Akce | Microsoft.Media/mediaservices/liveEvents/read | Číst všechny živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/reset/action | Resetovat všechny operace živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/start/action | Spustit všechny operace živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/stop/action | Zastavit všechny operace živé události |
> | Akce | Microsoft.Media/mediaservices/liveEvents/write | Vytvořit nebo aktualizovat všechny živé události |
> | Akce | Microsoft.Media/mediaservices/liveOutputOperations/read | Všechny za provozu výstupní operace čtení |
> | Akce | Microsoft.Media/mediaservices/read | Číst všechny účtu Media Services |
> | Akce | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Číst všechny operace a koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/delete | Odstranit všechny koncového bodu streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/read | Číst libovolný koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Škálování všechny operace a koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Spustit všechny operace a koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Zastavit všechny operace a koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingEndpoints/write | Vytvořit nebo aktualizovat žádný koncový bod streamování |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/delete | Odstranit všechny Lokátor streamování |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Seznam klíčů k obsahu |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Seznam cest |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/read | Číst všechny Lokátor streamování |
> | Akce | Microsoft.Media/mediaservices/streamingLocators/write | Vytvořit nebo aktualizovat všechny Lokátor streamování |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/delete | Odstranit všechny zásady pro streamování |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/read | Číst všechny zásady pro streamování |
> | Akce | Microsoft.Media/mediaservices/streamingPolicies/write | Vytvořit nebo aktualizovat všechny zásady pro streamování |
> | Akce | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronizovat úložiště klíče pro účet připojené úložiště Azure |
> | Akce | Microsoft.Media/mediaservices/transforms/delete | Odstranit všechny transformace |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Zrušit úlohu |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/delete | Odstranit všechny úlohy |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/read | Číst všechny úlohy |
> | Akce | Microsoft.Media/mediaservices/transforms/jobs/write | Vytvořit nebo aktualizovat všechny úlohy |
> | Akce | Microsoft.Media/mediaservices/transforms/read | Číst všechny transformace |
> | Akce | Microsoft.Media/mediaservices/transforms/write | Vytvořit nebo aktualizovat všechny transformace |
> | Akce | Microsoft.Media/mediaservices/write | Vytvořit nebo aktualizovat všechny účtu Media Services |
> | Akce | Microsoft.Media/operations/read | Získat dostupné operace |
> | Akce | Microsoft.Media/register/action | Zaregistruje předplatné pro poskytovatele prostředků Media Services a povolí vytvoření účtů Media Services |
> | Akce | Microsoft.Media/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Migrate/Operations/read | Přečte zveřejněné operace |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.NetApp/locations/operationresults/read | Načte prostředek výsledek operace. |
> | Akce | Microsoft.NetApp/locations/read | Čtení na dostupnosti zkontrolujte prostředků. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Odstraní fond prostředků. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/read | Přečte fond prostředků. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/delete | Odstraní prostředek svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/delete | Odstraní cílový prostředek připojení. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/read | Přečte cílový prostředek připojení. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/write | Zapíše cílový prostředek připojení. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/read | Odstraní prostředek svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/write | Odstraní prostředek svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/metricDefinitions/read | Odstraní prostředek svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/read | Načte prostředek svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/delete | Odstraní prostředek snímku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/read | Načte prostředek snímku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/write | Zapíše prostředek snímku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/write | Zapíše prostředek svazku. |
> | Akce | Microsoft.NetApp/netAppAccounts/capacityPools/write | Zapíše fond prostředků. |
> | Akce | Microsoft.NetApp/netAppAccounts/delete | Odstraní prostředek účtu. |
> | Akce | Microsoft.NetApp/netAppAccounts/read | Načte prostředek účtu. |
> | Akce | Microsoft.NetApp/netAppAccounts/write | Zapíše prostředek účtu. |
> | Akce | Microsoft.NetApp/Operations/read | Přečte operace prostředky. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl předdefinovaných zásad |
> | Akce | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Dostupné možnosti Ssl aplikace brány |
> | Akce | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Získá aplikační bránu, kterou sad pravidel k dispozici firewall webových aplikací |
> | Akce | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Spojí fond adres back-end brány aplikace |
> | Akce | Microsoft.Network/applicationGateways/backendhealth/action | Získá stavu back-end brány aplikaci |
> | Akce | Microsoft.Network/applicationGateways/delete | Odstraní aplikační bránu. |
> | Akce | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Získejte tabulku směrování nakonfigurované na aplikační brány |
> | Akce | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Získejte tabulku směrování nakonfigurované na aplikační brány |
> | Akce | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Načte události služby Application Gateway |
> | Akce | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro službu Application Gateway |
> | Akce | Microsoft.Network/applicationGateways/read | Získá aplikační bránu. |
> | Akce | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Konfigurace sady Application Gateway Security Center |
> | Akce | Microsoft.Network/applicationGateways/start/action | Spuštění služby application gateway |
> | Akce | Microsoft.Network/applicationGateways/stop/action | Zastavení služby application gateway |
> | Akce | Microsoft.Network/applicationGateways/write | Služby application gateway vytvoří nebo aktualizuje aplikační bránu. |
> | Akce | Microsoft.Network/applicationSecurityGroups/delete | Odstraní skupinu zabezpečení aplikací |
> | Akce | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Spojí konfiguraci IP adres do skupin zabezpečení aplikace. |
> | Akce | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Spojí pravidlo zabezpečení do skupiny zabezpečení aplikací. |
> | Akce | Microsoft.Network/applicationSecurityGroups/read | Získá identifikátor skupiny zabezpečení aplikací. |
> | Akce | Microsoft.Network/applicationSecurityGroups/write | Vytvoří skupinu zabezpečení aplikace nebo aktualizuje existující skupiny zabezpečení aplikace. |
> | Akce | Microsoft.Network/bgpServiceCommunities/read | Získat komunit protokolu Bgp služby |
> | Akce | Microsoft.Network/checkTrafficManagerNameAvailability/action | Zkontroluje dostupnost názvu DNS relativní Traffic Manageru. |
> | Akce | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/sharedkey/action | Získat VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/sharedKey/read | Získá VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/sharedKey/write | Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection SharedKey |
> | Akce | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Získá konfiguraci zařízení Vpn VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/connections/write | Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | Odstraní Proxy plán ochranu DDoS |
> | Akce | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | Získá definici Proxy plán ochranu DDoS |
> | Akce | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Vytvoří Proxy plán ochranu DDoS nebo aktualizace a stávající DDoS ochrany plánování Proxy |
> | Akce | Microsoft.Network/ddosProtectionPlans/delete | Odstraní plán ochranu DDoS |
> | Akce | Microsoft.Network/ddosProtectionPlans/join/action | Spojí plán ochranu DDoS |
> | Akce | Microsoft.Network/ddosProtectionPlans/read | Získá plán ochranu DDoS |
> | Akce | Microsoft.Network/ddosProtectionPlans/write | Vytvoří plán ochrany DDoS nebo aktualizuje plán ochranu DDoS  |
> | Akce | Microsoft.Network/dnsoperationresults/read | Získá výsledky operace DNS |
> | Akce | Microsoft.Network/dnsoperationstatuses/read | Získá stav operace DNS  |
> | Akce | Microsoft.Network/dnszones/A/delete | Odebrat sadu záznamů daného názvu a typu "A" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/A/read | Načíst sadu záznamů typu "A", ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/A/write | Vytvořit nebo aktualizovat sadu záznamů typu "A" v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/AAAA/delete | Odebrat sadu záznamů daného názvu a typu 'AAAA, umožňuje ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/AAAA/read | Načíst sadu záznamů typu 'AAAA, ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/AAAA/write | Vytvořit nebo aktualizovat sadu záznamů typu 'AAAA, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/all/read | Získá sady záznamů DNS mezi typy |
> | Akce | Microsoft.Network/dnszones/CAA/delete | Odebrat sadu záznamů daného názvu a typu "KÁ' umožňuje ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/CAA/read | Načíst sadu záznamů typu 'KÁ' ve formátu JSON. Sada záznamů obsahuje TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/CAA/write | Vytvořit nebo aktualizovat sadu záznamů typu 'KÁ' v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/CNAME/delete | Odebrat sadu záznamů daného názvu a typu, CNAME, umožňuje ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/CNAME/read | Načíst sadu záznamů typu 'CNAME' ve formátu JSON. Sada záznamů obsahuje TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/CNAME/write | Vytvořit nebo aktualizovat sadu záznamů typu, CNAME, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/delete | Odstraňte zónu DNS, ve formátu JSON. Vlastnosti zóny zahrnují značky, etag, numberOfRecordSets a maxNumberOfRecordSets. |
> | Akce | Microsoft.Network/dnszones/MX/delete | Odebrat sadu záznamů daného názvu a typu "MX" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/MX/read | Načíst sadu záznamů typu "MX", ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/MX/write | Vytvořit nebo aktualizovat sadu záznamů typu "MX" v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/NS/delete | Umožňuje odstranit sadu záznamů typu NS DNS |
> | Akce | Microsoft.Network/dnszones/NS/read | Získá DNS sadu záznamů typu NS |
> | Akce | Microsoft.Network/dnszones/NS/write | Vytvoří nebo aktualizuje DNS sadu záznamů typu NS |
> | Akce | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky zóny DNS |
> | Akce | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky zóny DNS |
> | Akce | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | Získá zónu DNS definice metrik |
> | Akce | Microsoft.Network/dnszones/PTR/delete | Odebrat sadu záznamů daného názvu a typu 'PTR, umožňuje ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/PTR/read | Načíst sadu záznamů typu 'PTR, ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/PTR/write | Vytvořit nebo aktualizovat sadu záznamů typu 'PTR, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/read | Umožňuje načíst zónu DNS, ve formátu JSON. Vlastnosti zóny zahrnují značky, etag, numberOfRecordSets a maxNumberOfRecordSets. Všimněte si, že tento příkaz nenačítá sady záznamů v rámci zóny. |
> | Akce | Microsoft.Network/dnszones/recordsets/read | Získá sady záznamů DNS mezi typy |
> | Akce | Microsoft.Network/dnszones/SOA/read | Získá sadu záznamů DNS typu SOA |
> | Akce | Microsoft.Network/dnszones/SOA/write | Vytvoří nebo aktualizuje DNS sadu záznamů typu SOA |
> | Akce | Microsoft.Network/dnszones/SRV/delete | Odebrat sadu záznamů daného názvu a typu SRV "služby" ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/SRV/read | Načíst sadu záznamů typu 'SRV' ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/SRV/write | Vytvořit nebo aktualizovat sadu záznamů typu SRV |
> | Akce | Microsoft.Network/dnszones/TXT/delete | Odebrat sadu záznamů daného názvu a typu 'TXT, umožňuje ze zóny DNS. |
> | Akce | Microsoft.Network/dnszones/TXT/read | Načíst sadu záznamů typu 'TXT, ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag. |
> | Akce | Microsoft.Network/dnszones/TXT/write | Vytvořit nebo aktualizovat sadu záznamů typu 'TXT, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů. |
> | Akce | Microsoft.Network/dnszones/write | Vytvořit nebo aktualizovat zónu DNS v rámci skupiny prostředků.  Použít k aktualizaci značek u prostředku zóny DNS. Všimněte si, že tento příkaz nelze použít k vytvoření nebo aktualizaci sad záznamů v rámci zóny. |
> | Akce | Microsoft.Network/expressRouteCircuits/authorizations/delete | Odstraní ExpressRouteCircuit povolení |
> | Akce | Microsoft.Network/expressRouteCircuits/authorizations/read | Získá povolení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/authorizations/write | Vytvoří nebo aktualizuje existující povolení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/delete | Odstraní ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/join/action | Spojí okruhu Express Route |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Získá ArpTable ExpressRouteCircuit partnerského vztahu |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Odstraní připojení k ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Získá připojení k ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Vytvoří nebo aktualizuje existující prostředek připojení ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/delete | Odstraní partnerský vztah ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/read | Získá partnerský vztah ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Získá RouteTable ExpressRouteCircuit partnerského vztahu |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Získá ExpressRouteCircuit partnerský vztah RouteTable souhrn |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Získá ExpressRouteCircuit partnerský vztah Statistika |
> | Akce | Microsoft.Network/expressRouteCircuits/peerings/write | Vytvoří nebo aktualizuje existující ExpressRouteCircuit partnerský vztah |
> | Akce | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro okruhy ExpressRoute |
> | Akce | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro okruhy ExpressRoute |
> | Akce | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Získat události pro okruhy ExpressRoute |
> | Akce | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Získá definice metrik pro okruhy ExpressRoute |
> | Akce | Microsoft.Network/expressRouteCircuits/read | Získat ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCircuits/stats/read | Získá ExpressRouteCircuit Stat |
> | Akce | Microsoft.Network/expressRouteCircuits/write | Vytvoří nebo aktualizuje existující ExpressRouteCircuit |
> | Akce | Microsoft.Network/expressRouteCrossConnections/delete | Odstranit Express Route křížová připojení |
> | Akce | Microsoft.Network/expressRouteCrossConnections/join/action | Křížové spojení na Express Route připojení |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Získá trasu Express křížové připojení partnerský vztah na základě tabulky Arp |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Odstraní Express směrování mezi partnerský vztah připojení |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/read | Získá trasu Express křížové připojení partnerského vztahu |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Získá trasu Express křížové připojení partnerský vztah směrovací tabulku |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Získá trasu Express křížové připojení partnerský vztah trasy tabulky Souhrn |
> | Akce | Microsoft.Network/expressRouteCrossConnections/peerings/write | Vztahu Express Route křížová připojení vytvoří nebo aktualizuje existující Express trasy mezi připojení partnerský vztah |
> | Akce | Microsoft.Network/expressRouteCrossConnections/read | Získat Express Route křížová připojení |
> | Akce | Microsoft.Network/expressRouteCrossConnections/write | Vytvořit nebo aktualizovat Express Route křížová připojení |
> | Akce | Microsoft.Network/expressRouteServiceProviders/read | Získá poskytovatele služeb Expressroute |
> | Akce | Microsoft.Network/intendedPolicies/delete | Odstraní požadované zásady |
> | Akce | Microsoft.Network/intendedPolicies/read | Získá popis požadované zásady |
> | Akce | Microsoft.Network/intendedPolicies/write | Vytvoří zásadu určený nebo aktualizuje existující určené zásady |
> | Akce | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí fond back-end adresy Vyrovnávání zatížení. |
> | Akce | Microsoft.Network/loadBalancers/backendAddressPools/read | Získá definici fondu adres back-end zatížení nástroje pro vyrovnávání |
> | Akce | Microsoft.Network/loadBalancers/delete | Odstraní nástroj pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Získá definice konfigurace IP front-endu nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Spojí Vyrovnávání zatížení fond příchozích pravidel nat |
> | Akce | Microsoft.Network/loadBalancers/inboundNatPools/read | Získá Vyrovnávání zatížení příchozí nat definici fondu |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/delete | Odstraní pravidlo příchozí nat nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Spojí pravidlo příchozí nat nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/read | Nástroj pro vyrovnávání zatížení získá definici příchozího pravidla nat |
> | Akce | Microsoft.Network/loadBalancers/inboundNatRules/write | Vytvoří pravidlo příchozí nat nástroje pro vyrovnávání zatížení nebo aktualizuje existující pravidlo příchozí nat nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/loadBalancingRules/read | Získá zatížení nástroje pro vyrovnávání zatížení vyrovnávání definici pravidla |
> | Akce | Microsoft.Network/loadBalancers/networkInterfaces/read | Získá odkazy na rozhraní sítě pod nástrojem pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/outboundNatRules/read | Získá definici pravidla odchozí nat nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/probes/join/action | Umožňuje použití sondy nástroje pro vyrovnávání zatížení. S touto vlastností healthProbe oprávnění měřítka virtuálních počítačů sady můžete odkazovat, sonda. |
> | Akce | Microsoft.Network/loadBalancers/probes/read | Získá sondu nástroje pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Získá události pro nástroj pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Akce | Microsoft.Network/loadBalancers/virtualMachines/read | Získá odkazy na všechny virtuální počítače pod nástrojem pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/loadBalancers/write | Vytvoří nástroj pro vyrovnávání zatížení nebo aktualizuje existující pro vyrovnávání zatížení |
> | Akce | Microsoft.Network/localnetworkgateways/delete | Odstraní LocalNetworkGateway |
> | Akce | Microsoft.Network/localnetworkgateways/read | Získá LocalNetworkGateway |
> | Akce | Microsoft.Network/localnetworkgateways/write | Vytvoří nebo aktualizuje existující LocalNetworkGateway |
> | Akce | Microsoft.Network/locations/availableDelegations/read | Získá dostupné delegování |
> | Akce | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Podpora Accelerated sítě kontroly |
> | Akce | Microsoft.Network/locations/checkDnsNameAvailability/read | Zkontroluje, jestli je popisek dns k dispozici v zadaném umístění |
> | Akce | Microsoft.Network/locations/effectiveResourceOwnership/action | Získá vlastnictví efektivní prostředků |
> | Akce | Microsoft.Network/locations/operationResults/read | Získá výsledek operace asynchronní POST nebo operace odstranění |
> | Akce | Microsoft.Network/locations/operations/read | Získá prostředek operace, který reprezentuje stav asynchronní operace |
> | Akce | Microsoft.Network/locations/setResourceOwnership/action | Nastaví vlastnictví prostředků |
> | Akce | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Získá podporované velikosti virtuálních počítačů |
> | Akce | Microsoft.Network/locations/usages/read | Získá metriky využití prostředků |
> | Akce | Microsoft.Network/locations/validateResourceOwnership/action | Ověří vlastnictví prostředků |
> | Akce | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Získá seznam dostupných virtuální síťové koncový bod služby |
> | Akce | Microsoft.Network/networkInterfaces/delete | Odstraní rozhraní sítě |
> | Akce | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Získá diagnostiky Identity prostředku |
> | Akce | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Získat skupiny zabezpečení sítě nakonfigurované na síťové rozhraní z virtuálního počítače |
> | Akce | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Získejte tabulku směrování nakonfigurovaný v síťovém rozhraní virtuálního počítače |
> | Akce | Microsoft.Network/networkInterfaces/ipconfigurations/read | Získá definici konfigurace ip rozhraní sítě.  |
> | Akce | Microsoft.Network/networkInterfaces/join/action | Virtuální počítač připojí k síťovému rozhraní |
> | Akce | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Spojí prostředek k síťovému rozhraní prostřednictvím služby přidružení |
> | Akce | Microsoft.Network/networkInterfaces/loadBalancers/read | Získá všech služeb Vyrovnávání zatížení, které je součástí rozhraní sítě |
> | Akce | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro síťové rozhraní |
> | Akce | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Akce | Microsoft.Network/networkInterfaces/serviceAssociations/delete | Odstraní přidružení služby |
> | Akce | Microsoft.Network/networkInterfaces/serviceAssociations/read | Získá definici služby přidružení |
> | Akce | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Ověří služba přidružení |
> | Akce | Microsoft.Network/networkInterfaces/serviceAssociations/write | Vytvoří nové přidružení služby nebo upraví existující přidružení služby |
> | Akce | Microsoft.Network/networkInterfaces/write | Vytvoří rozhraní sítě nebo aktualizuje existující rozhraní sítě.  |
> | Akce | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Získá definici pravidla zabezpečení výchozí |
> | Akce | Microsoft.Network/networkSecurityGroups/delete | Odstraní skupinu zabezpečení sítě |
> | Akce | Microsoft.Network/networkSecurityGroups/join/action | Spojí skupinu zabezpečení sítě |
> | Akce | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky skupiny zabezpečení sítě. |
> | Akce | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky skupin zabezpečení sítě. Tuto operaci doplňuje poskytovatel prostředků Insights. |
> | Akce | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Načte události pro skupinu zabezpečení sítě. |
> | Akce | Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/delete | Odstraní pravidlo zabezpečení |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/read | Získá definici pravidla zabezpečení |
> | Akce | Microsoft.Network/networkSecurityGroups/securityRules/write | Vytvoří pravidlo zabezpečení nebo aktualizuje existující pravidlo zabezpečení. |
> | Akce | Microsoft.Network/networkSecurityGroups/write | Vytvoří skupinu zabezpečení sítě nebo aktualizuje existující skupinu zabezpečení sítě |
> | Akce | Microsoft.Network/networkWatchers/availableProvidersList/action | Vrátí všechny dostupné internet poskytovatelé služeb pro zadané oblasti Azure. |
> | Akce | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Vrátí relativní latence skóre pro internet poskytovatelé služeb z určitého umístění k oblastem Azure. |
> | Akce | Microsoft.Network/networkWatchers/configureFlowLog/action | Nakonfiguruje toku protokolování pro cílový prostředek. |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/delete | Odstraní monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Dotaz na monitorování připojení mezi zadané koncové body |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/read | Získat podrobnosti o monitorování připojení |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Spuštění monitorování připojení mezi zadané koncové body |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Zastavení nebo pozastavení monitorování připojení mezi zadané koncové body |
> | Akce | Microsoft.Network/networkWatchers/connectionMonitors/write | Vytvoří připojení monitorování |
> | Akce | Microsoft.Network/networkWatchers/connectivityCheck/action | Ověří možnost pro přímé připojení TCP z virtuálního počítače na danou koncový bod, včetně jiným virtuálním Počítačem nebo libovolný vzdálený server. |
> | Akce | Microsoft.Network/networkWatchers/delete | Odstraní sledovací proces sítě |
> | Akce | Microsoft.Network/networkWatchers/ipFlowVerify/action | Vrátí, jestli je paket povolený nebo zakázaný do nebo z konkrétní cíl. |
> | Akce | Microsoft.Network/networkWatchers/lenses/delete | Odstraní přehledu |
> | Akce | Microsoft.Network/networkWatchers/lenses/query/action | Dotaz na monitorování síťových přenosů na zadaný koncový bod |
> | Akce | Microsoft.Network/networkWatchers/lenses/read | Získat podrobnosti o přehledu |
> | Akce | Microsoft.Network/networkWatchers/lenses/start/action | Spuštění monitorování síťových přenosů na zadaný koncový bod |
> | Akce | Microsoft.Network/networkWatchers/lenses/stop/action | Zastavení nebo pozastavení monitorování síťových přenosů na zadaný koncový bod |
> | Akce | Microsoft.Network/networkWatchers/lenses/write | Vytvoří přehledu |
> | Akce | Microsoft.Network/networkWatchers/nextHop/action | U zadaného cíle a cílové IP adresy vrátí typ dalšího směrování a další naděje IP adresu. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/delete | Odstraní zachytávání paketů |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Získá informace o vlastnostech a stav prostředku zachytávání paketů. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/read | Získat definici zachytávání paketů |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Zastavte spuštěné relaci zachytávání paketů. |
> | Akce | Microsoft.Network/networkWatchers/packetCaptures/write | Vytvoří zachytávání paketů |
> | Akce | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Získá stav toku protokolování na prostředku. |
> | Akce | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Získá řešení potíží výsledek z dříve spuštění nebo aktuálně spuštěna operace odstraňování potíží. |
> | Akce | Microsoft.Network/networkWatchers/read | Získat definici sledovací proces sítě |
> | Akce | Microsoft.Network/networkWatchers/securityGroupView/action | Zobrazení pravidel skupiny zabezpečení sítě nakonfigurované a efektivní použita na virtuálním počítači. |
> | Akce | Microsoft.Network/networkWatchers/topology/action | Získá úrovně zobrazení síťových prostředků a jejich vztahů ve skupině prostředků. |
> | Akce | Microsoft.Network/networkWatchers/troubleshoot/action | Spustí se Poradce při potížích s na prostředku sítě v Azure. |
> | Akce | Microsoft.Network/networkWatchers/write | Sledovací proces sítě vytvoří nebo aktualizuje existující sledovací proces sítě |
> | Akce | Microsoft.Network/operations/read | Získat dostupné operace |
> | Akce | Microsoft.Network/publicIPAddresses/delete | Odstraní veřejnou Ip adresu. |
> | Akce | Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. |
> | Akce | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | Odstraní veřejnou IP adresu fondu vyrovnávání zatížení back-end |
> | Akce | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | Spojení veřejných IP adres fondu vyrovnávání zatížení back-end |
> | Akce | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Získá veřejnou back-end fondu definice nástroje pro vyrovnávání zatížení IP adresu |
> | Akce | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | Vytvoří fond back-end pro vyrovnávání zatížení veřejnou IP adresu nebo aktualizuje existující veřejnou IP adresu zatížení vyrovnávání back-end fond |
> | Akce | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky veřejné IP adresy |
> | Akce | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Vytvořit nebo aktualizovat nastavení diagnostiky veřejné IP adresy |
> | Akce | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | Získat definice protokolu veřejné IP adresy |
> | Akce | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | Získat definice metrik veřejné IP adresy |
> | Akce | Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
> | Akce | Microsoft.Network/publicIPAddresses/write | Vytvoří veřejnou Ip adresu nebo aktualizuje existující veřejnou Ip adresu.  |
> | Akce | Microsoft.Network/register/action | Zaregistruje předplatné. |
> | Akce | Microsoft.Network/routeFilters/delete | Odstraní definici směrovací filtru |
> | Akce | Microsoft.Network/routeFilters/join/action | Spojí filtr trasy |
> | Akce | Microsoft.Network/routeFilters/read | Získá definici filtru trasy |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/delete | Odstraní definici směrovací filtr pravidlo |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/read | Získá definici pravidla filtru trasy |
> | Akce | Microsoft.Network/routeFilters/routeFilterRules/write | Vytvoří pravidlo filtru trasu nebo aktualizuje existující pravidlo filtru trasy |
> | Akce | Microsoft.Network/routeFilters/write | Vytvoří filtr trasu nebo aktualizuje existující směrovací filtr |
> | Akce | Microsoft.Network/routeTables/delete | Odstraní definici směrovací tabulky |
> | Akce | Microsoft.Network/routeTables/join/action | Spojí směrovací tabulku |
> | Akce | Microsoft.Network/routeTables/read | Získá definici směrovací tabulky |
> | Akce | Microsoft.Network/routeTables/routes/delete | Odstraní definici směrovací |
> | Akce | Microsoft.Network/routeTables/routes/read | Získá definici trasy |
> | Akce | Microsoft.Network/routeTables/routes/write | Vytvoří trasu nebo aktualizuje existující trasu. |
> | Akce | Microsoft.Network/routeTables/write | Vytvoří směrovací tabulku nebo aktualizuje existující směrovací tabulku. |
> | Akce | Microsoft.Network/securegateways/applicationRuleCollections/delete | Odstraní kolekci aplikace pravidel pro zabezpečené brány |
> | Akce | Microsoft.Network/securegateways/applicationRuleCollections/read | Načtení kolekci pravidel aplikace pro danou bránu zabezpečení |
> | Akce | Microsoft.Network/securegateways/applicationRuleCollections/write | Vytvoří nebo aktualizuje kolekci pravidel aplikace pro bránu zabezpečení |
> | Akce | Microsoft.Network/securegateways/delete | Odstranit bránu zabezpečení |
> | Akce | Microsoft.Network/securegateways/networkRuleCollections/delete | Odstraní kolekci pravidel sítě pro zabezpečené brány |
> | Akce | Microsoft.Network/securegateways/networkRuleCollections/read | Načtení sítě kolekci pravidel pro danou bránu zabezpečení |
> | Akce | Microsoft.Network/securegateways/networkRuleCollections/write | Vytvoří nebo aktualizuje kolekci pravidel sítě pro bránu zabezpečení |
> | Akce | Microsoft.Network/securegateways/providers/Microsoft.Insights/logDefinitions/read | Získá události pro Azure bránu Firewall |
> | Akce | Microsoft.Network/securegateways/read | Zjištění zabezpečené brány |
> | Akce | Microsoft.Network/securegateways/write | Vytvoří nebo aktualizuje bránu zabezpečení |
> | Akce | Microsoft.Network/serviceEndpointPolicies/delete | Odstraní zásadu koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/join/action | Spojí zásadu koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Spojí podsíť zásad koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/read | Získá popis zásad koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Odstraní definici zásady koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Získá dešifrování definice zásady koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Vytvoří definici zásady koncový bod služby nebo aktualizuje existující definice zásady koncový bod služby |
> | Akce | Microsoft.Network/serviceEndpointPolicies/write | Vytvoří zásadu koncový bod služby nebo aktualizuje existující zásady koncový bod služby |
> | Akce | Microsoft.Network/trafficManagerGeographicHierarchies/read | Získá obsahující oblastí, které se dají použít s metodu směrování provozu geografické hierarchii Geographic Traffic Manager |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Odstraní koncový bod Azure z existujícího profilu Správce provozu. Správce provozu se zastaví směrování provozu odstraněné koncový bod Azure. |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Získá koncový bod Azure, který patří k profilu Traffic Manageru, včetně všechny vlastnosti tohoto koncového bodu Azure. |
> | Akce | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Přidat nový koncový bod Azure ve stávající profil správce provozu nebo aktualizujte vlastnosti existující koncový bod Azure v tomto profilu služby Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/delete | Odstraňte profil služby Traffic Manager. Všechna nastavení spojená s profilem správce provozu se ztratí a profil slouží už pro směrování provozu. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Externí koncový bod se odstraní z existujícího profilu Správce provozu. Správce provozu se zastaví směrování provozu odstraněné externí koncový bod. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Získá externí koncový bod, který patří k profilu Traffic Manageru, včetně všechny vlastnosti tohoto externího koncového bodu. |
> | Akce | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Přidat nový externí koncový bod v existující profil Traffic Manageru nebo aktualizujte vlastnosti existující externí koncový bod v tomto profilu služby Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Získá Heat mapa Traffic Manager pro daný profil Traffic Manageru, která obsahuje dotaz počty a latence data podle umístění a zdroj IP. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Odstraní koncový bod vnořené z existujícího profilu Správce provozu. Správce provozu se zastaví směrování provozu odstraněné vnořené koncový bod služby. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Získá koncový vnořené, který patří k profilu Traffic Manageru, včetně všechny vlastnosti tohoto koncového bodu vnořený. |
> | Akce | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Přidat nový koncový bod vnořené v existující profil Traffic Manageru nebo aktualizujte vlastnosti existující vnořené koncový bod v tomto profilu služby Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky Traffic Manageru |
> | Akce | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky Traffic Manager. Tato operace je doplněná poskytovatelem prostředků statistiky. |
> | Akce | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Získá události pro správce provozu |
> | Akce | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro správce provozu. |
> | Akce | Microsoft.Network/trafficManagerProfiles/read | Získání konfigurace profilu Správce provozu. To zahrnuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncového bodu a seznam koncových bodů směrovaných tímto profilem Traffic Manager. |
> | Akce | Microsoft.Network/trafficManagerProfiles/write | Vytvoření profilu Správce provozu nebo úprava konfigurace existujícího profilu Správce provozu.<br>To zahrnuje zapnutí nebo vypnutí profilu a změna nastavení DNS, nastavení směrování provozu nebo nastavení monitorování koncového bodu.<br>Koncových bodů směrovaných tímto profilem správce provozu můžete přidat, odebrat, povolit nebo zakázat. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Odstraní úrovni předplatného je klíč používaný k shromažďování metrik uživatele v reálném čase. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/read | Získá úrovni předplatného klíč používaný pro shromažďování metrik uživatele v reálném čase. |
> | Akce | Microsoft.Network/trafficManagerUserMetricsKeys/write | Vytvoří nový klíč úrovni předplatného, který se má použít pro shromažďování metrik uživatele v reálném čase. |
> | Akce | Microsoft.Network/unregister/action | Zruší registraci předplatného |
> | Akce | Microsoft.Network/virtualHubs/delete | Odstraní virtuální rozbočovače |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Odstraní HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Získání HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Vytvořit nebo aktualizovat HubVirtualNetworkConnection |
> | Akce | Microsoft.Network/virtualHubs/read | Získat virtuální rozbočovače |
> | Akce | Microsoft.Network/virtualHubs/write | Vytvořit nebo aktualizovat virtuální rozbočovače |
> | Akce | Microsoft.Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Akce | Microsoft.Network/virtualNetworkGateways/delete | Odstraní virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generovat balíček VpnClient pro virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | Generovat VpnProfile balíček pro VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Získá virtualNetworkGateway Inzerovat trasy |
> | Akce | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Získá stav partnerského protokolu bgp virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Získá virtualnetworkgateway naučili trasy |
> | Akce | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Získávání parametrů Vpnclient Ipsec pro VirtualNetworkGateway P2S klienta. |
> | Akce | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Získá adresu URL profilu balíčku klienta vpn předem vygenerovaná |
> | Akce | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení pro diagnostiku brány virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky brány virtuální sítě, tato operace je doplněná poskytovatelem prostředků statistiky. |
> | Akce | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Získá události pro bránu virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro bránu virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworkGateways/read | Získá VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/reset/Action | Obnoví virtualNetworkGateway |
> | Akce | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Nastavte parametry pro klienta VirtualNetworkGateway P2S Vpnclient Ipsec. |
> | Akce | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Seznamy podporovaná zařízení Vpn |
> | Akce | Microsoft.Network/virtualNetworkGateways/write | Vytvoří nebo aktualizuje VirtualNetworkGateway |
> | Akce | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Zkontrolujte, jestli je k dispozici v zadané virtuální síti Ip adresy |
> | Akce | Microsoft.Network/virtualNetworks/customViews/get/action | Získat vlastní zobrazení obsahu virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/customViews/read | Načtení definice zobrazení vlastní virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/delete | Odstraní virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/peer/action | Partnerský vztah virtuální síť s jinou virtuální sítí |
> | Akce | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Získat nastavení diagnostiky virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Vytvořit nebo aktualizovat nastavení diagnostiky virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | Získat definice protokolu virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Odstraní virtuální sítě partnerského vztahu proxy server |
> | Akce | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Získá virtuální síť partnerský vztah definici proxy |
> | Akce | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Proxy server partnerského vztahu virtuální sítě vytvoří nebo aktualizuje stávající virtuální síť partnerského vztahu proxy |
> | Akce | Microsoft.Network/virtualNetworks/subnets/delete | Odstraní podsíť virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Spojí prostředků, jako je například účet úložiště nebo SQL database k podsíti. |
> | Akce | Microsoft.Network/virtualNetworks/subnets/read | Získá definici podsítě virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Odstraní prostředek navigační odkaz |
> | Akce | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Načtení definice prostředku navigační odkaz |
> | Akce | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Vytvoří navigační odkaz na prostředek nebo aktualizuje existující prostředek navigační odkaz |
> | Akce | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | Odstraní propojení přidružení služby |
> | Akce | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Získá definici služby odkaz přidružení podrobností |
> | Akce | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Získá definici odkaz přidružení služby |
> | Akce | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Ověří odkaz přidružení služby |
> | Akce | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Odkaz přidružení Service vytvoří nebo aktualizuje existující odkaz přidružení služby |
> | Akce | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Získá odkazy na všechny virtuální počítače v podsíti virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/subnets/write | Vytvoří podsíť virtuální sítě nebo aktualizuje existující podsíť virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Odstraní s příznakem provoz příjemce |
> | Akce | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | Získat definici označené provoz příjemce |
> | Akce | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Ověří s příznakem provoz příjemce |
> | Akce | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Provoz příjemce označené vytvoří nebo aktualizuje existující příjemce provoz s příznakem |
> | Akce | Microsoft.Network/virtualNetworks/usages/read | Získat IP použití pro každou podsíť virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/virtualMachines/read | Získá odkazy na všechny virtuální počítače ve virtuální síti |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Odstraní virtuální sítě partnerského vztahu |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Získá definici partnerského vztahu virtuální sítě |
> | Akce | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Vytvoří partnerský vztah virtuální sítě nebo aktualizuje existující virtuální síť partnerský vztah |
> | Akce | Microsoft.Network/virtualNetworks/write | Vytvoří virtuální síť nebo aktualizuje existující virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/delete | Odstranit klepněte na virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/join/action | Spojí klepněte na virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/read | Získat klepněte na virtuální síť |
> | Akce | Microsoft.Network/virtualNetworkTaps/write | Vytvořit nebo aktualizovat klepněte na virtuální síť |
> | Akce | Microsoft.Network/virtualWans/delete | Odstranění a virtuální sítě Wan |
> | Akce | Microsoft.Network/virtualWans/read | GET a virtuální sítě Wan |
> | Akce | Microsoft.Network/virtualWans/virtualHubProxies/delete | Odstraní proxy server virtuální rozbočovače |
> | Akce | Microsoft.Network/virtualWans/virtualHubProxies/read | Získá definici proxy virtuální rozbočovače |
> | Akce | Microsoft.Network/virtualWans/virtualHubProxies/write | Proxy server virtuální rozbočovače vytvoří nebo aktualizuje proxy server virtuální rozbočovače |
> | Akce | Microsoft.Network/virtualWans/virtualHubs/read | Získá všechny virtuální rozbočovače, které odkazují virtuální sítě Wan. |
> | Akce | Microsoft.Network/virtualwans/vpnconfiguration/action | Získá konfiguraci sítě Vpn |
> | Akce | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Odstraní proxy serveru sítě Vpn |
> | Akce | Microsoft.Network/virtualWans/vpnSiteProxies/read | Získá definici proxy serveru sítě Vpn |
> | Akce | Microsoft.Network/virtualWans/vpnSiteProxies/write | Vytvoří proxy serveru sítě Vpn nebo aktualizuje proxy serveru sítě Vpn |
> | Akce | Microsoft.Network/virtualWans/vpnSites/read | Získá všechny lokality sítě VPN, které odkazují virtuální sítě Wan. |
> | Akce | Microsoft.Network/virtualWans/write | Vytvořit nebo aktualizovat virtuální Wan |
> | Akce | Microsoft.Network/vpnGateways/read | Získá Brána VPN. |
> | Akce | microsoft.network/vpnGateways/vpnConnections/read | Získá VpnConnection. |
> | Akce | microsoft.network/vpnGateways/vpnConnections/write | Vloží VpnConnection. |
> | Akce | Microsoft.Network/vpnGateways/write | Vloží Brána VPN. |
> | Akce | Microsoft.Network/vpnsites/delete | Odstraní prostředek Vpn Site. |
> | Akce | Microsoft.Network/vpnsites/read | Získá zdroj Vpn webu. |
> | Akce | Microsoft.Network/vpnsites/write | Vytvoří nebo aktualizuje prostředek Vpn Site. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Zkontroluje, jestli je ve službě NotificationHub k dispozici název prostředku oboru názvů, nebo ne. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Autorizační pravidlo oboru názvů – opětovné vygenerování primárního nebo sekundárního klíče. Zadejte klíč, který je nutné znovu vygenerovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Zkontroluje, jestli je v oboru názvů k dispozici název NotificationHub, nebo ne. |
> | Akce | Microsoft.NotificationHubs/Namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Získá seznam autorizačních pravidel Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Odstranit autorizační pravidla Centra oznámení |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Získá připojovací řetězec k Centru oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Získá seznam autorizačních pravidel Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Autorizační pravidlo Centra oznámení – opětovné vygenerování primárního nebo sekundárního klíče. Zadejte klíč, který je nutné znovu vygenerovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Vytvoření pravidla autorizace centra oznámení a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Poslat testovací nabízené oznámení |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Odstraní prostředek Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Získání seznamu Namespace metrik popisy prostředků |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Získáte všechna pověření systému PNS centra oznámení. To zahrnuje, přihlašovací údaje WNS, MPNS, APNS, GCM a Baidu |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Získá seznam popisů prostředků Centra oznámení. |
> | Akce | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Vytvoření centra oznámení a aktualizujte jeho vlastnosti. Jeho vlastnosti především zahrnovat přihlašovací údaje systému PNS. Autorizační pravidla a hodnota TTL |
> | Akce | Microsoft.NotificationHubs/Namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.NotificationHubs/Namespaces/write | Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.NotificationHubs/register/action | Zaregistruje předplatné u poskytovatele prostředků NotificationHubs a povolí vytváření oborů názvů a NotificationHubs. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OperationalInsights/linkTargets/read | Zobrazí seznam stávajících účtů, které nejsou přidružené předplatné Azure. Toto předplatné odkaz na pracovním prostoru, použijte id zákazníka vrácená touto operací ve vlastnosti id zákazníka operace vytvořit pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/register/action | Registrujte předplatné zprostředkovatele prostředků. |
> | Akce | Microsoft.OperationalInsights/workspaces/analytics/query/action | Vyhledávání pomocí nový modul. |
> | Akce | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Načíst schéma vyhledávání V2. |
> | Akce | Microsoft.OperationalInsights/workspaces/api/query/action | Vyhledávání pomocí nový modul. |
> | Akce | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Načíst schéma vyhledávání V2. |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Odstranit konfigurace oboru |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Získání konfigurace oboru |
> | Akce | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Nastavení konfigurace oboru |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/delete | Odstraňte zdrojů dat v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/read | Získáte zdrojů dat v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/datasources/write | Vytvořit nebo aktualizovat zdroje dat v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/delete | Odstraní pracovního prostoru. Pokud byl pracovním prostoru propojený k existujícímu pracovnímu prostoru v okamžiku vytvoření není odstranit pracovní prostor, který byl propojen s. |
> | Akce | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generuje registrační certifikát pro pracovní prostor. Tento certifikát se používá k připojení do pracovního prostoru Microsoft System Center Operation Manager. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Zakáže intelligence pack pro daný pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Umožňuje intelligence pack pro daný pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Obsahuje seznam všech intelligence Pack, které jsou viditelné pro danou worksapce a také uvádí, jestli je sada povolit nebo zakázat pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Odstranění propojené služby v rámci daného pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/read | Získat propojené služby v rámci zadané pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/linkedServices/write | Vytvořit nebo aktualizovat propojené služby v rámci daného pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/listKeys/action | Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/listKeys/read | Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/managementGroups/read | Získá názvy a metadat pro připojení k tomuto pracovnímu prostoru skupiny pro správu System Center Operations Manager. |
> | Akce | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Získat definice metrika v části pracovní prostor |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Odstraňte uživatele nastavení oznámení pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Získáte uživatelské nastavení oznámení pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Nastavte uživatele nastavení oznámení pro pracovní prostor. |
> | Akce | Microsoft.OperationalInsights/workspaces/purge/action | Odstranit zadaná data z pracovního prostoru |
> | Akce | Microsoft.OperationalInsights/workspaces/read | Získá existujícímu pracovnímu prostoru |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Odstraní uložené vyhledávací dotaz. |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/read | Získá uložené vyhledávací dotaz. |
> | Akce | Microsoft.OperationalInsights/workspaces/savedSearches/write | Vytvoří uložené vyhledávací dotaz. |
> | Akce | Microsoft.OperationalInsights/workspaces/schema/read | Získá schéma vyhledávání pro pracovní prostor.  Hledání schématu zahrnuje zveřejněné pole a jejich typy. |
> | Akce | Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
> | Akce | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Odstraní konfigurace úložiště. To se zastaví statistice provozu Microsoft z čtení dat z účtu úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Získá konfiguraci úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Vytvoří novou konfiguraci úložiště. Tyto konfigurace se používají k načítání dat z umístění, do stávající účet úložiště. |
> | Akce | Microsoft.OperationalInsights/workspaces/usages/read | Získá data o využití pro pracovní prostor, včetně množství dat číst v pracovním prostoru. |
> | Akce | Microsoft.OperationalInsights/workspaces/write | Vytvoří nový pracovní prostor nebo odkazy k existujícímu pracovnímu prostoru tím, že poskytuje id zákazníka z existující pracovního prostoru. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.OperationsManagement/managementAssociations/delete | Odstraňte existující přidružení sady Management |
> | Akce | Microsoft.OperationsManagement/managementAssociations/read | Získat existující přidružení sady Management |
> | Akce | Microsoft.OperationsManagement/managementAssociations/write | Vytvoření nového přidružení správy |
> | Akce | Microsoft.OperationsManagement/managementConfigurations/delete | Odstraňte existující Configuratin správy |
> | Akce | Microsoft.OperationsManagement/managementConfigurations/read | Získat existující konfigurace správy |
> | Akce | Microsoft.OperationsManagement/managementConfigurations/write | Vytvořit novou konfiguraci správy |
> | Akce | Microsoft.OperationsManagement/register/action | Registrujte předplatné zprostředkovatele prostředků. |
> | Akce | Microsoft.OperationsManagement/solutions/delete | Odstraňte existující řešení OMS |
> | Akce | Microsoft.OperationsManagement/solutions/read | Získat ukončení OMS řešení |
> | Akce | Microsoft.OperationsManagement/solutions/write | Vytvořte nové řešení OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.PolicyInsights/policyEvents/queryResults/action | Dotaz na informace o události zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/queryResults/action | Dotaz na informace o stavech zásad. |
> | Akce | Microsoft.PolicyInsights/policyStates/summarize/action | Dotaz na souhrnné informace o nejnovější stavy zásad. |

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
> | Akce | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Kontroluje, zda daný kapacity vyhrazené Power BI je platný název a ne v používání. |
> | Akce | Microsoft.PowerBIDedicated/capacities/delete | Odstranění Power BI vyhrazené kapacity. |
> | Akce | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro Power BI vyhrazené kapacity |
> | Akce | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro Power BI vyhrazené kapacity. |
> | Akce | Microsoft.PowerBIDedicated/capacities/read | Načte informace o zadané Power BI vyhrazené kapacity. |
> | Akce | Microsoft.PowerBIDedicated/capacities/resume/action | Obnoví kapacitu. |
> | Akce | Microsoft.PowerBIDedicated/capacities/skus/read | Načíst dostupné informace o SKU pro kapacitu |
> | Akce | Microsoft.PowerBIDedicated/capacities/suspend/action | Pozastaví kapacitu. |
> | Akce | Microsoft.PowerBIDedicated/capacities/write | Vytvoří nebo aktualizuje zadaný Power BI vyhrazené kapacitu. |
> | Akce | Microsoft.PowerBIDedicated/locations/operationresults/read | Načte informace o zadanou operaci výsledku. |
> | Akce | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Načte informace o stavu Zadaná operace. |
> | Akce | Microsoft.PowerBIDedicated/operations/read | Načte informace o operací |
> | Akce | Microsoft.PowerBIDedicated/register/action | Registruje zprostředkovatele prostředků vyhrazené pro Power BI. |
> | Akce | Microsoft.PowerBIDedicated/skus/read | Načte informace o SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Akce | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace, které používá služba |
> | Akce | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Akce | Microsoft.RecoveryServices/locations/backupStatus/action | Zkontrolujte stav zálohování trezory služeb zotavení |
> | Akce | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověřit funkce |
> | Akce | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro poskytovatele prostředků |
> | Akce | Microsoft.RecoveryServices/register/action | Zaregistruje předplatné pro danou poskytovatele prostředků |
> | Akce | Microsoft.RecoveryServices/Vaults/backupconfig/read | Vrátí konfiguraci pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupconfig/write | Konfigurace aktualizací pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery správy zálohování zaregistrované s trezorem. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvoření zálohy záměr ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Získání všech chránitelné kontejnery |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Odstraní kontejner registrovaných |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Proveďte dotaz pro úlohy v rámci kontejneru |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Získat všechny položky v kontejneru |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Provede zálohování chráněné položky. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Odstranění chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zřízení rychlých položky obnovení pro chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Obnoví body obnovení pro chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odvolat rychlých položky obnovení pro chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvoření zálohy chráněné položky |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Vytvoří kontejner registrovaných |
> | Akce | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualizuje seznam kontejneru |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Zrušení úlohy |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úlohy |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
> | Akce | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Vrátí metadata správy zálohování trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupOperations/read | Operace zálohování vrátí stav pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Odstranění zásady ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Načíst stav operace zásad. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Vytvoří zásadu ochrany |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Vrátí seznam chránitelných položek. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
> | Akce | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Vrátí zabezpečení PIN kódu informace pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Vrátí konfiguraci úložiště pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aktualizace konfiguraci úložiště pro obnovení služeb trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
> | Akce | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů. |
> | Akce | Microsoft.RecoveryServices/Vaults/delete | Operace odstranění trezoru Odstraní zadaný prostředek Azure typu 'trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřeší výstrahy. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Získá konfigurace oznámení trezoru služby pro obnovení. |
> | Akce | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Nakonfiguruje e-mailová oznámení do trezoru služeb zotavení. |
> | Akce | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Zálohování Azure Diagnostics |
> | Akce | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Zálohování Azure Diagnostics |
> | Akce | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Azure protokoly zálohování |
> | Akce | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Azure Backup metriky |
> | Akce | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Zrušit registraci kontejneru operaci lze použít se zrušit registraci kontejneru. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
> | Akce | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operaci zaregistrovat kontejneru služby lze použít k registraci kontejner službou obnovení. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Číst všechny nastavení výstrah |
> | Akce | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Vytvořit nebo aktualizovat nastavení výstrah |
> | Akce | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Zkontroluje konzistenci prostředí infrastruktury. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Odstranit všechny prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Nasadit Image procesového serveru |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Číst všechny prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Přidružení brány |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Odebrání infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Obnovit certifikát pro prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Odstranit všechny mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Číst veškerá jeho mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Vytvořit nebo aktualizovat veškerá jeho mapování sítě |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Zjistit Chránitelná položka |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Kontejner ochrany odebrat. |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Číst všechny položky, které jsou předmětem ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Použít bod obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Odstranit všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Potvrzení převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Plánované převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Číst všechny body obnovení replikace |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Odebrat chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Oprava replikace |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Znovu aktivujte ochranu chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testovací převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Vyčistit testovací převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizace služby Mobility |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Vytvořit nebo aktualizovat všechny chráněné položky |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Odstranit veškerá jeho mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Číst veškerá jeho mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Odebrat mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Vytvořit nebo aktualizovat veškerá jeho mapování kontejnerů ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kontejner ochrany přepínače |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Vytvořit nebo aktualizovat všechny kontejnery ochrany |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Odstranit zprostředkovatelů služby obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Přečtěte si zprostředkovatelů služby obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aktualizujte zprostředkovatele |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Odebrat zprostředkovatele služeb zotavení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Vytvořit nebo aktualizovat zprostředkovatelů služby obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Odstranit veškerá jeho mapování klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Číst veškerá jeho mapování klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Vytvořit nebo aktualizovat veškerá jeho mapování klasifikace úložiště |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Odstranit všechny úlohy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny úlohy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Vytvořit nebo aktualizovat všechny úlohy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Vytvořit nebo aktualizovat všechny prostředky infrastruktury |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Zrušit úlohu |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/read | Číst všechny úlohy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Restartujte úlohu |
> | Akce | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Obnovení úlohy |
> | Akce | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Odstranit všechny zásady |
> | Akce | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
> | Akce | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Vytvořit nebo aktualizovat všechny zásady |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Odstranit všechny plány obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánované převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Znovu nastavte ochranu plánu obnovení |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testovací převzetí služeb při selhání obnovení plán |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plán obnovení testovací převzetí služeb při selhání čištění |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Akce | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Vytvořit nebo aktualizovat všechny plány obnovení |
> | Akce | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
> | Akce | Microsoft.RecoveryServices/vaults/usages/read | Číst všechny použití trezoru |
> | Akce | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Akce | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
> | Akce | Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Relay/checkNameAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. |
> | Akce | Microsoft.Relay/checkNamespaceAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. místo toho použijte CheckNameAvailabiltiy. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/action | Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je depricated. Se aktualizovat autorizační pravidlo Namespace místo toho použijte volání PUT... Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/delete | Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.Relay/namespaces/authorizationRules/write | Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.Relay/namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Získá klíče pravidel ověřování pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat pravidla ověřování primárního oboru názvů zotavení po havárii |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních. |
> | Akce | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Odstraní konfigurace zotavení po havárii přidružené k oboru názvů. Tato operace může volat jenom přes primární obor názvů. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operace k aktualizaci HybridConnection. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operace odstranění HybridConnection autorizační pravidla |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Získat připojovací řetězec k HybridConnection |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Získání seznamu HybridConnection autorizační pravidla |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Vytvořte HybridConnection autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat. |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/Delete | Operace odstranění HybridConnection prostředků |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/read | Získání seznamu popisů HybridConnection prostředků |
> | Akce | Microsoft.Relay/namespaces/HybridConnections/write | Vytvoření nebo aktualizace HybridConnection vlastnosti. |
> | Akce | Microsoft.Relay/namespaces/messagingPlan/read | Získá plánování zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralá.<br>Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API...<br>Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.Relay/namespaces/messagingPlan/write | Aktualizace, zasílání zpráv plánování pro obor názvů.<br>Toto rozhraní API je zastaralá.<br>Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API...<br>Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.Relay/namespaces/operationresults/read | Získá stav operace Namespace. |
> | Akce | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získání seznamu Namespace metrik popisy prostředků |
> | Akce | Microsoft.Relay/namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Odebrat obor názvů služby ACS |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operace k aktualizaci WcfRelay. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operace odstranění WcfRelay autorizační pravidla |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Získat připojovací řetězec k WcfRelay |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Získání seznamu WcfRelay autorizační pravidla |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Vytvořte WcfRelay autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat. |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/Delete | Operace odstranění WcfRelay prostředků |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/read | Získání seznamu popisů WcfRelay prostředků |
> | Akce | Microsoft.Relay/namespaces/WcfRelays/write | Vytvoření nebo aktualizace WcfRelay vlastnosti. |
> | Akce | Microsoft.Relay/namespaces/write | Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.Relay/operations/read | Získejte operace |
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
> | Akce | Microsoft.ResourceHealth/register/action | Zaregistruje předplatné do služby Microsoft Resource Health. |

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
> | Akce | Microsoft.Search/operations/read | Zobrazí seznam všech dostupných operací Microsoft.Search zprostředkovatele. |
> | Akce | Microsoft.Search/register/action | Zaregistruje předplatné pro poskytovatele vyhledávání prostředků a umožňuje vytvoření služby vyhledávání. |
> | Akce | Microsoft.Search/searchServices/createQueryKey/action | Vytvoří klíč dotazu. |
> | Akce | Microsoft.Search/searchServices/delete | Odstraní službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/deleteQueryKey/delete | Odstraní klíč dotazu. |
> | Akce | Microsoft.Search/searchServices/diagnosticSettings/read | Získá nastavení pro čtení pro prostředek diganostic |
> | Akce | Microsoft.Search/searchServices/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diganostic pro prostředek. |
> | Akce | Microsoft.Search/searchServices/listAdminKeys/action | Načte klíče správce. |
> | Akce | Microsoft.Search/searchServices/listQueryKeys/read | Vrátí seznam klíčů dotaz rozhraní API pro danou službu Azure Search. |
> | Akce | Microsoft.Search/searchServices/logDefinitions/read | Získá dostupné protokoly pro službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/metricDefinitions/read | Načte dostupné metriky pro službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/read | Načte službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/regenerateAdminKey/action | Znovu vygeneruje klíč správce. |
> | Akce | Microsoft.Search/searchServices/start/action | Spustí službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/stop/action | Zastaví službu vyhledávání. |
> | Akce | Microsoft.Search/searchServices/write | Vytvoří nebo aktualizuje službu vyhledávání. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Security/alerts/read | Získá všechny výstrahy zabezpečení k dispozici |
> | Akce | Microsoft.Security/applicationWhitelistings/read | Získá whitelistings aplikace |
> | Akce | Microsoft.Security/applicationWhitelistings/write | Vytvoří nové vytvoření seznamu povolených aplikací nebo aktualizuje existující |
> | Akce | Microsoft.Security/complianceResults/read | Získá výsledky dodržování shody pro prostředek |
> | Akce | Microsoft.Security/locations/alerts/activate/action | Aktivovat výstrahy zabezpečení |
> | Akce | Microsoft.Security/locations/alerts/dismiss/action | Zamítnutí výstrahy zabezpečení |
> | Akce | Microsoft.Security/locations/alerts/read | Získá všechny výstrahy zabezpečení k dispozici |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicializuje zásady přístupu k síti za běhu |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Získá zásad přístupu k síti za běhu |
> | Akce | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Vytvoří nové zásady přístupu k síti za běhu nebo aktualizuje existující |
> | Akce | Microsoft.Security/locations/read | Získá umístění zabezpečení dat |
> | Akce | Microsoft.Security/locations/tasks/activate/action | Aktivovat doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/dismiss/action | Zavření doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/read | Získá všechny dostupné zabezpečení doporučení |
> | Akce | Microsoft.Security/locations/tasks/resolve/action | Vyřešte doporučení zabezpečení |
> | Akce | Microsoft.Security/locations/tasks/start/action | Spustit doporučení zabezpečení |
> | Akce | Microsoft.Security/policies/read | Získá zásady zabezpečení |
> | Akce | Microsoft.Security/policies/write | Aktualizace nastavení zásad zabezpečení |
> | Akce | Microsoft.Security/pricings/delete | Odstraní cenovou nastavení oboru |
> | Akce | Microsoft.Security/pricings/read | Získá cenovou nastavení oboru |
> | Akce | Microsoft.Security/pricings/write | Aktualizace nastavení cenové pro obor |
> | Akce | Microsoft.Security/register/action | Zaregistruje předplatné pro Azure Security Center |
> | Akce | Microsoft.Security/securityContacts/delete | Odstraní zabezpečení kontakt |
> | Akce | Microsoft.Security/securityContacts/read | Získá kontakt zabezpečení |
> | Akce | Microsoft.Security/securityContacts/write | Aktualizace zabezpečení kontakt |
> | Akce | Microsoft.Security/securitySolutions/delete | Odstraní zabezpečení řešení |
> | Akce | Microsoft.Security/securitySolutions/read | Získá řešení zabezpečení |
> | Akce | Microsoft.Security/securitySolutions/write | Vytvoří nové řešení zabezpečení nebo aktualizuje existující |
> | Akce | Microsoft.Security/securitySolutionsReferenceData/read | Získá referenční data řešení zabezpečení |
> | Akce | Microsoft.Security/securityStatuses/read | Získá stavy stavu zabezpečení prostředků Azure |
> | Akce | Microsoft.Security/securityStatusesSummaries/read | Získá zabezpečení souhrny stavů pro obor |
> | Akce | Microsoft.Security/tasks/read | Získá všechny dostupné zabezpečení doporučení |
> | Akce | Microsoft.Security/webApplicationFirewalls/delete | Odstraní brány firewall webových aplikací |
> | Akce | Microsoft.Security/webApplicationFirewalls/read | Získá webové aplikace brány firewall |
> | Akce | Microsoft.Security/webApplicationFirewalls/write | Vytvoří nové brány firewall webových aplikací nebo aktualizuje existující |
> | Akce | Microsoft.Security/workspaceSettings/connect/action | Změňte nastavení obnovení nastavení pracovního prostoru |
> | Akce | Microsoft.Security/workspaceSettings/delete | Odstraní nastavení pracovního prostoru |
> | Akce | Microsoft.Security/workspaceSettings/read | Získá nastavení pracovního prostoru |
> | Akce | Microsoft.Security/workspaceSettings/write | Aktualizace nastavení pracovního prostoru |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/delete | Odstraňuje se nastavení diagnostiky |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/read | Načítání nastavení diagnostiky |
> | Akce | Microsoft.SecurityGraph/diagnosticsettings/write | Zápis nastavení diagnostiky |
> | Akce | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Načítání nastavení diagnostiky kategorií |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.ServiceBus/checkNameAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. |
> | Akce | Microsoft.ServiceBus/checkNamespaceAvailability/action | Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. místo toho použijte CheckNameAvailabiltiy. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/action | Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je depricated. Se aktualizovat autorizační pravidlo Namespace místo toho použijte volání PUT... Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit.  |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Získat připojovací řetězec k oboru názvů |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/read | Získá seznam popisů autorizačních pravidel oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.ServiceBus/namespaces/authorizationRules/write | Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat. |
> | Akce | Microsoft.ServiceBus/namespaces/Delete | Odstraní prostředek oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Získá klíče pravidel ověřování pro primární obor názvů zotavení po havárii. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Získat pravidla ověřování primárního oboru názvů zotavení po havárii |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Zkontroluje dostupnost aliasu oboru názvů v daném předplatném. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Odstraní konfigurace zotavení po havárii přidružené k oboru názvů. Tato operace může volat jenom přes primární obor názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Odstraní filtr Event Gridu přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Získá filtr Event Gridu přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Vytvoří nebo aktualizuje filtr Event Gridu přidružený k oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/eventhubs/read | Získání seznamu popisů EventHub prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/messagingPlan/read | Získá plánování zasílání zpráv pro obor názvů.<br>Toto rozhraní API je zastaralá.<br>Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API...<br>Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.ServiceBus/namespaces/messagingPlan/write | Aktualizace, zasílání zpráv plánování pro obor názvů.<br>Toto rozhraní API je zastaralá.<br>Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API...<br>Tato operace není podporována na volání rozhraní API verze 2017-04-01. |
> | Akce | Microsoft.ServiceBus/namespaces/migrate/action | Operace migrace oboru názvů |
> | Akce | Microsoft.ServiceBus/namespaces/operationresults/read | Získá stav operace Namespace. |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Získání seznamu protokolů Namespace popisy prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Získání seznamu Namespace metrik popisy prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operace k aktualizaci fronty. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operace odstranění fronty autorizační pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Získat připojovací řetězec do fronty |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Získání seznamu fronty autorizační pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Vytvoření fronty autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat. |
> | Akce | Microsoft.ServiceBus/namespaces/queues/Delete | Operace odstranění fronty prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/queues/read | Získání seznamu popisů fronty prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/queues/write | Vytvoření nebo aktualizace fronty vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/read | Získá seznam popisů prostředku oboru názvů. |
> | Akce | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Odebrat obor názvů služby ACS |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operace k aktualizaci tématu. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operace odstranění tématu autorizační pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Získat připojovací řetězec k tématu |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Získání seznamu tématu autorizační pravidla |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Znovu vygeneruje primární nebo sekundární klíč k prostředku. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Vytvoření tématu autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/Delete | Operace odstranění prostředků tématu |
> | Akce | Microsoft.ServiceBus/namespaces/topics/read | Získání seznamu popisů tématu prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operace odstranění TopicSubscription prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Získání seznamu popisů TopicSubscription prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operace se odstranit pravidlo prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Získání seznamu popisů pravidlo prostředků |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Vytvořit nebo aktualizovat pravidlo vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Vytvoření nebo aktualizace TopicSubscription vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/topics/write | Vytvoření nebo aktualizace tématu vlastnosti. |
> | Akce | Microsoft.ServiceBus/namespaces/write | Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat. |
> | Akce | Microsoft.ServiceBus/operations/read | Získejte operace |
> | Akce | Microsoft.ServiceBus/register/action | Zaregistruje předplatné u poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus. |
> | Akce | Microsoft.ServiceBus/sku/read | Získání seznamu popisů Sku prostředků |
> | Akce | Microsoft.ServiceBus/sku/regions/read | Získání seznamu popisů SkuRegions prostředků |
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
> | Akce | Microsoft.SignalRService/checknameavailability/action | Ověří, zda název je k dispozici pro použití s novou službu SignalR |
> | Akce | Microsoft.SignalRService/register/action | Registruje zprostředkovatele prostředků 'Microsoft.SignalRService' s předplatným |
> | Akce | Microsoft.SignalRService/SignalR/delete | Odstranit celý SignalR |
> | Akce | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro SignalR |
> | Akce | Microsoft.SignalRService/SignalR/read | Zobrazit nastavení a konfigurace funkci SignalR v portálu pro správu nebo prostřednictvím rozhraní API |
> | Akce | Microsoft.SignalRService/SignalR/write | Upravit nastavení a konfigurace v portálu pro správu nebo prostřednictvím rozhraní API funkci SignalR |
> | Akce | Microsoft.SignalRService/unregister/action | Zrušení registrace poskytovatele prostředků 'Microsoft.SignalRService' s předplatným |

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
> | Akce | Microsoft.Solutions/locations/operationStatuses/read | Umožňuje načíst stav operace pro prostředek. |
> | Akce | Microsoft.Solutions/register/action | Provede registraci do Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Sql/checkNameAvailability/action | Ověřte, zda zadaný server je k dispozici pro zřizování po celém světě pro dané předplatné název. |
> | Akce | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Načíst výsledek operace nastavení zásad auditování objektu blob rozšířený server |
> | Akce | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Načíst výsledek operace nastavení zásad auditování objektu blob serveru |
> | Akce | Microsoft.Sql/locations/capabilities/read | Získá možnosti pro toto předplatné v daném umístění |
> | Akce | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/locations/databaseOperationResults/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Získá v průběhu operace na odstraněné serveru |
> | Akce | Microsoft.Sql/locations/deletedServerOperationResults/read | Získá v průběhu operace na odstraněné serveru |
> | Akce | Microsoft.Sql/locations/deletedServers/read | Vrátí seznam odstraněných servery nebo získá vlastnosti pro zadaný server odstraněné. |
> | Akce | Microsoft.Sql/locations/deletedServers/recover/action | Obnovení odstraněného serveru |
> | Akce | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Odstraní pravidla virtuální sítě přidružený k virtuální síť nebo podsíť |
> | Akce | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Získá azure asynchronní operace pro elastický fond asynchronní operace |
> | Akce | Microsoft.Sql/locations/elasticPoolOperationResults/read | Získá výsledek operace elastického fondu. |
> | Akce | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Načíst výsledek operace nastavení zásad auditování objektu blob rozšířený server |
> | Akce | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Načíst výsledek operace nastavení zásad auditování objektu blob rozšířený server |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/delete | Odstraní existující skupinu pro převzetí služeb při selhání instance. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Provede plánované převzetí služeb při selhání v existující skupině instance převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Provede Vynucené převzetí služeb při selhání v existující skupině instance převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/read | Vrátí seznam instance převzetí služeb při selhání skupiny nebo získá vlastnosti pro zadanou instanci skupinu převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/instanceFailoverGroups/write | Vytvoří skupinu instance převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti a značky pro zadanou instanci skupinu převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/locations/longTermRetentionBackups/read | Seznamy dlouhodobé uchovávání zálohy pro všechny databáze na každém serveru v umístění |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Seznamy dlouhodobé uchovávání zálohy pro všechny databáze na serveru |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Odstraní zálohu dlouhodobé uchovávání |
> | Akce | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Seznamy dlouhodobé uchovávání zálohy pro databázi |
> | Akce | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Dokončení operace obnovení spravované databáze |
> | Akce | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Získá v průběhu operací na spravované databáze transparentní šifrování dat |
> | Akce | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Získá v průběhu operací na spravované databáze transparentní šifrování dat |
> | Akce | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Vrátí podrobnosti o konkrétním síťovém rozhraní Azure asynchronní operace |
> | Akce | Microsoft.Sql/locations/networkInterfaceOperationResults/read | Vrátí podrobnosti o operaci zadané síťové rozhraní |
> | Akce | Microsoft.Sql/locations/read | Získá umístění dostupné pro dané předplatné |
> | Akce | Microsoft.Sql/locations/syncAgentOperationResults/read | Načíst výsledek operace synchronizace agenta prostředků |
> | Akce | Microsoft.Sql/locations/syncDatabaseIds/read | Načtení ID databáze synchronizace pro konkrétní oblasti a předplatného |
> | Akce | Microsoft.Sql/locations/syncGroupOperationResults/read | Načíst výsledek operace synchronizace skupiny prostředků |
> | Akce | Microsoft.Sql/locations/syncMemberOperationResults/read | Načíst výsledek operace synchronizace člen prostředků |
> | Akce | Microsoft.Sql/locations/usages/read | Získá kolekci metriky využití pro toto předplatné v umístění |
> | Akce | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Vrátí podrobnosti pravidel zadané virtuální síti azure asynchronní operace  |
> | Akce | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Vrací podrobnosti operace pravidla zadané virtuální síti  |
> | Akce | Microsoft.Sql/managedInstances/administrators/delete | Odstraní stávající správce spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/administrators/read | Získá seznam správců spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/administrators/write | Vytvoří nebo aktualizuje spravované instance správce se zadanými parametry. |
> | Akce | Microsoft.Sql/managedInstances/databases/delete | Odstraní stávající spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/read | Získá existující spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Načtení podrobností zásady detekce hrozeb databáze nakonfigurované na danou databázi spravované |
> | Akce | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Změna zásad detekce hrozeb databáze pro danou databázi spravované |
> | Akce | Microsoft.Sql/managedInstances/databases/securityEvents/read | Načte události zabezpečení spravované databáze |
> | Akce | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Načíst podrobnosti o databáze transparentní šifrování dat na danou databázi spravované |
> | Akce | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Změnit databázi transparentní šifrování dat pro danou databázi spravované |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Odebrat vyhodnocení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Načíst podrobnosti o vyhodnocení ohrožení zabezpečení, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Odeberte směrný plán vyhodnocení pravidlo ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Získat základní pravidlo assessment ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Změňte základní pravidlo assessment ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Převeďte stávající výsledek kontroly na lidské čitelném formátu. Pokud už nic se stane |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Spusťte kontrolu databáze assessment ohrožení zabezpečení. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Vrátí seznam ohrožení zabezpečení databáze záznamy kontrolu vyhodnocení nebo získat kontrolu záznam pro ID zadaný kontroly. |
> | Akce | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Změnit vyhodnocení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/managedInstances/databases/write | Vytvoří novou databázi nebo aktualizuje existující databázi. |
> | Akce | Microsoft.Sql/managedInstances/delete | Odstraní existující spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/metricDefinitions/read | Získat definice metrik spravované instance |
> | Akce | Microsoft.Sql/managedInstances/metrics/read | Získat metriky spravované instance |
> | Akce | Microsoft.Sql/managedInstances/read | Vrátí seznam spravované instance nebo získá vlastnosti pro zadané spravované instance. |
> | Akce | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Načíst podrobnosti o zásadě detekce hrozeb spravovaný server nakonfigurovaný na daném spravovaného serveru |
> | Akce | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Změna zásad detekce hrozeb spravovaného serveru pro danou spravovaný server |
> | Akce | Microsoft.Sql/managedInstances/write | Vytvoří instanci spravovaného se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadané spravované instance. |
> | Akce | Microsoft.Sql/operations/read | Získá dostupné operace REST |
> | Akce | Microsoft.Sql/register/action | Zaregistruje předplatné pro poskytovatele prostředků Microsoft SQL Database a umožňuje vytvoření databáze Microsoft SQL. |
> | Akce | Microsoft.Sql/servers/administratorOperationResults/read | Získá v průběhu operace ve Správci serveru |
> | Akce | Microsoft.Sql/servers/administrators/delete | Odstranit správce serveru |
> | Akce | Microsoft.Sql/servers/administrators/read | Načíst podrobnosti o správce serveru |
> | Akce | Microsoft.Sql/servers/administrators/write | Vytvořit nebo aktualizovat Správce serveru |
> | Akce | Microsoft.Sql/servers/advisors/read | Vrátí seznam poradci, které jsou k dispozici pro server |
> | Akce | Microsoft.Sql/servers/advisors/recommendedActions/read | Vrátí seznam doporučených akcí zadaný advisor pro server |
> | Akce | Microsoft.Sql/servers/advisors/recommendedActions/write | Použití doporučenou akci na server |
> | Akce | Microsoft.Sql/servers/advisors/write | Aktualizace automatického – spuštění stav advisor na úrovni serveru. |
> | Akce | Microsoft.Sql/servers/auditingPolicies/read | Načíst podrobnosti o serveru tabulky výchozí zásady na daném serveru nakonfigurován auditování |
> | Akce | Microsoft.Sql/servers/auditingPolicies/write | Změňte výchozí server tabulky auditování pro daný server |
> | Akce | Microsoft.Sql/servers/auditingSettings/operationResults/read | Načíst výsledek operace nastavení zásad auditování objektu blob serveru |
> | Akce | Microsoft.Sql/servers/auditingSettings/read | Načíst podrobnosti o zásadě auditování objektů blob serveru na daném serveru nakonfigurován |
> | Akce | Microsoft.Sql/servers/auditingSettings/write | Změnit nastavení auditování serveru objekt blob pro daný server |
> | Akce | Microsoft.Sql/servers/automaticTuning/read | Vrátí automatické ladění nastavení pro server |
> | Akce | Microsoft.Sql/servers/automaticTuning/write | Automatické ladění nastavení pro server aktualizací a vrátí aktualizovaným nastavením |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Odstraní existující trezor záloh archivaci. |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Tato operace se používá k získání trezoru zálohování dlouhodobé uchovávání. Vrátí informace o úložišti registrované k tomuto serveru |
> | Akce | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Tato operace se používá k registraci zálohování dlouhodobé uchovávání trezoru k serveru |
> | Akce | Microsoft.Sql/servers/communicationLinks/delete | Odstraní existující odkaz komunikace serveru. |
> | Akce | Microsoft.Sql/servers/communicationLinks/read | Vrátí seznam komunikační propojení zadaný server. |
> | Akce | Microsoft.Sql/servers/communicationLinks/write | Vytvořit nebo aktualizovat datový spoj server. |
> | Akce | Microsoft.Sql/servers/connectionPolicies/read | Vrátí seznam zásady připojení serveru zadaného serveru. |
> | Akce | Microsoft.Sql/servers/connectionPolicies/write | Vytvořit nebo aktualizovat zásady připojení serveru. |
> | Akce | Microsoft.Sql/servers/databases/advisors/read | Vrátí seznam poradci, které jsou k dispozici pro databázi |
> | Akce | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Vrátí seznam doporučené akce zadané pro databázi Poradce pro |
> | Akce | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Použít akci doporučenou v databázi |
> | Akce | Microsoft.Sql/servers/databases/advisors/write | Aktualizace automatického – spuštění stav advisor na úrovni databáze. |
> | Akce | Microsoft.Sql/servers/databases/auditingPolicies/read | Načtení podrobností tabulky auditování zásady, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingPolicies/write | Změna zásad auditování tabulka pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingSettings/read | Načíst podrobnosti o objektu blob auditování zásady, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditingSettings/write | Změna zásad auditování objektů blob pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu databáze objektů blob |
> | Akce | Microsoft.Sql/servers/databases/automaticTuning/read | Vrátí nastavení automatického ladění pro databázi |
> | Akce | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualizace nastavení automatického ladění pro databázi a vrátí aktualizovaným nastavením |
> | Akce | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Vrátí seznam archivace zásady zálohování zadané databáze. |
> | Akce | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Vytvořit nebo aktualizovat zásady zálohování archivace databáze. |
> | Akce | Microsoft.Sql/servers/databases/connectionPolicies/read | Načíst podrobnosti o zásadě připojení nakonfigurovaná na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/connectionPolicies/write | Změnit zásady připojení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Vrátí seznam maskování zásady dat databáze. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Odstranit pravidlo zásad pro danou databázi maskování dat. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Načtení podrobností pravidla zásad, které jsou nakonfigurované na danou databázi maskování dat |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Změňte pravidla zásad pro danou databázi maskování dat. |
> | Akce | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Změnit zásady pro danou databázi maskování dat. |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Vrátí informace o kroku distribuovaného dotazu dotazu datový sklad pro vybraný krok ID |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Vrátí distribuční dotazu informace o datovém skladu pro vybraný dotaz ID |
> | Akce | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Načte aktivit uživatelů instance SQL Data Warehouse, která obsahuje dotazy, spuštěné a pozastavený |
> | Akce | Microsoft.Sql/servers/databases/delete | Odstraní existující databázi. |
> | Akce | Microsoft.Sql/servers/databases/export/action | Export databáze Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Načtení podrobností rozšířený objekt blob auditování zásady, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Změna zásad auditování rozšířený objekt blob pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/extensions/read | Získá kolekci rozšíření pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/extensions/write | Změňte příponu pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Načtení geograficky zásady zálohování pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Vytvořit nebo aktualizovat zásadu geobackup databáze |
> | Akce | Microsoft.Sql/servers/databases/importExportOperationResults/read | Získá probíhající operace importu a exportu |
> | Akce | Microsoft.Sql/servers/databases/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.Sql/servers/databases/metrics/read | Vrátí metriky pro databáze |
> | Akce | Microsoft.Sql/servers/databases/move/action | Přejmenování databáze Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/operationResults/read | Získá stav operace databáze. |
> | Akce | Microsoft.Sql/servers/databases/operations/cancel/action | Zruší Azure SQL Database čekající asynchronní operaci, která ještě není dokončen. |
> | Akce | Microsoft.Sql/servers/databases/operations/read | Vrátí seznam operací provést v databázi |
> | Akce | Microsoft.Sql/servers/databases/pause/action | Pozastavení databáze datového skladu Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro databáze |
> | Akce | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro databáze |
> | Akce | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Vrátí kolekci texty dotazu, které odpovídají na zadané parametry. |
> | Akce | Microsoft.Sql/servers/databases/queryStore/read | Vrátí aktuální hodnoty nastavení úložiště dotazů pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/queryStore/write | Aktualizace nastavení úložiště dotazů pro databázi |
> | Akce | Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti pro určenou databázi. |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/delete | Ukončení relace replikace vynuceně a potenciální ztráty dat. |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Převzetí služeb při selhání po synchronizaci všech změní z primární, provedení této databáze do replikace relationship\u0027s primární a provádění vzdálených primární do sekundárního |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Převzetí služeb při selhání okamžitě potenciální ztrátě dat, provedení této databáze do replikace relationship\u0027s primární a provádění vzdálených primární do sekundárního |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/read | Návratový podrobnosti o propojení replikace navázána pro konkrétní databázi |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Ukončení relace replikace vynuceně nebo po synchronizaci s partnera |
> | Akce | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Režim aktualizace replikace pro odkaz na synchronní nebo asynchronní režim |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/action | Vytvoří nový bod obnovení |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/delete | Odstraní bod obnovení pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/restorePoints/read | Vrátí obnovit body pro databázi. |
> | Akce | Microsoft.Sql/servers/databases/resume/action | Obnovení databáze datového skladu Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/schemas/read | Načíst seznam schémat databáze |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Načíst seznam sloupců tabulky. |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Odstranit štítek utajení daného sloupce |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Získat štítek utajení daného sloupce |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Vytvořit nebo aktualizovat štítek utajení daného sloupce |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/read | Načtení seznamu tabulek databáze |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Načíst seznam doporučení indexu v databázi |
> | Akce | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Použít doporučení indexu |
> | Akce | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Načíst podrobnosti o zásadě detekce hrozeb nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Změna zásad detekce hrozeb pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/securityMetrics/read | Získá kolekci metriky zabezpečení databáze |
> | Akce | Microsoft.Sql/servers/databases/sensitivityLabels/read | Seznam popisky velkých a malých písmen na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Vrátí návrhu o škálování databáze nahoru nebo dolů na základě statistik provádění dotazu na výkon nebo snížit náklady na |
> | Akce | Microsoft.Sql/servers/databases/skus/read | Získá kolekci SKU, které jsou k dispozici pro databázi |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Zrušit synchronizaci skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/delete | Odstraní existující synchronizace skupiny. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Vrátí seznam synchronizace databáze schémata rozbočovače |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/logs/read | Vrátí seznam protokolů synchronizace skupiny |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/read | Vrátí seznam synchronizace skupiny nebo načte vlastnosti zadaného synchronizace skupiny. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Aktualizace schématu databáze Centrum synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu Centrum synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Odstraní existujícího člena synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Vrátí seznam členů synchronizace nebo získá vlastnosti pro člena zadané synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Aktualizovat schéma člen synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Načíst výsledek operace aktualizace schématu člen synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Vrátí seznam synchronizace schémat databáze člena |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Vytvoří členem synchronizace se zadanými parametry nebo aktualizovat vlastnosti pro člena zadané synchronizace. |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Aktivace synchronizace skupiny synchronizace |
> | Akce | Microsoft.Sql/servers/databases/syncGroups/write | Vytvoří skupinu synchronizace se zadanými parametry nebo aktualizovat vlastnosti pro zadaný synchronizace skupiny. |
> | Akce | Microsoft.Sql/servers/databases/topQueries/queryText/action | Vrátí text Transact-SQL pro ID vybraný dotaz |
> | Akce | Microsoft.Sql/servers/databases/topQueries/read | Vrátí statistiku modulu runtime pro vybraný dotaz agregovat do vybrané časové období |
> | Akce | Microsoft.Sql/servers/databases/topQueries/statistics/read | Vrátí statistiku modulu runtime pro vybraný dotaz agregovat do vybrané časové období |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Získá v průběhu operací na transparentní šifrování dat |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Načíst stav a podrobnosti o funkce zabezpečení dat transparentní šifrování pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Změnit stav šifrování transparentní dat |
> | Akce | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Upgrade databáze datového skladu Azure SQL |
> | Akce | Microsoft.Sql/servers/databases/usages/read | Získá informace o použití Azure SQL Database |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Odebrat vyhodnocení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Načíst podrobnosti o vyhodnocení ohrožení zabezpečení, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Odeberte směrný plán vyhodnocení pravidlo ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Získat základní pravidlo assessment ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Změňte základní pravidlo assessment ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Převeďte stávající výsledek kontroly na lidské čitelném formátu. Pokud už nic se stane |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Spusťte kontrolu databáze assessment ohrožení zabezpečení. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Vrátí seznam ohrožení zabezpečení databáze záznamy kontrolu vyhodnocení nebo získat kontrolu záznam pro ID zadaný kontroly. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Změnit vyhodnocení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Spusťte kontrolu databáze assessment ohrožení zabezpečení. |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Načíst výsledky prohledávání vyhodnocení ohrožení zabezpečení databáze operaci zpracování |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Načíst podrobnosti o vyhodnocení ohrožení zabezpečení, které jsou nakonfigurované na danou databázi |
> | Akce | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Změnit vyhodnocení ohrožení zabezpečení pro danou databázi |
> | Akce | Microsoft.Sql/servers/databases/write | Vytvoří databázi se zadanými parametry nebo aktualizovat vlastnosti a značky pro určenou databázi. |
> | Akce | Microsoft.Sql/servers/delete | Odstraní existující server. |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Odstraní stávající konfigurace zotavení po havárii pro daný server |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Vynutit převzetí služeb při selhání DisasterRecoveryConfiguration |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Získá kolekci po havárii obnovení konfigurace, které zahrnují tento server |
> | Akce | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Konfigurace obnovení po havárii serveru změn |
> | Akce | Microsoft.Sql/servers/elasticPoolEstimates/read | Vrátí seznam odhad elastického fondu vytvořeny již pro tento server |
> | Akce | Microsoft.Sql/servers/elasticPoolEstimates/write | Vytvoří nový odhad elastického fondu pro seznam databází poskytuje |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/read | Vrátí seznam poradci pro elastický fond k dispozici |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Vrátí seznam doporučených akcí zadaný advisor pro elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Použít akci doporučenou v elastického fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/advisors/write | Aktualizace automatického – spuštění stav advisor na úrovni elastického fondu. |
> | Akce | Microsoft.Sql/servers/elasticPools/databases/read | Získá seznam databází pro elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/delete | Odstranění existujícího elastického fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Načtení činnosti a údaje na danou databázi elastického fondu |
> | Akce | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Načtení činnosti a údaje na danou databázi, která je součástí fondu elastické databáze |
> | Akce | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro fondy elastické databáze |
> | Akce | Microsoft.Sql/servers/elasticPools/metrics/read | Vrátí metriky pro fondy elastické databáze |
> | Akce | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Zruší elastický fond Azure SQL čekající asynchronní operaci, která ještě není dokončen. |
> | Akce | Microsoft.Sql/servers/elasticPools/operations/read | Vrátí seznam operací týkajících se elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro fondy elastické databáze |
> | Akce | Microsoft.Sql/servers/elasticPools/read | Načíst podrobnosti o elastického fondu na daném serveru |
> | Akce | Microsoft.Sql/servers/elasticPools/skus/read | Získá kolekci SKU, které jsou k dispozici pro elastický fond |
> | Akce | Microsoft.Sql/servers/elasticPools/write | Vytvořit novou nebo změnit vlastnosti existujícího elastického fondu |
> | Akce | Microsoft.Sql/servers/encryptionProtector/read | Vrátí seznam ochrany pomocí šifrování serveru nebo získá vlastnosti pro zadaný server ochranu pomocí šifrování. |
> | Akce | Microsoft.Sql/servers/encryptionProtector/write | Aktualizujte vlastnosti pro zadaná šifrování Ochrana serveru. |
> | Akce | Microsoft.Sql/servers/extendedAuditingSettings/read | Načíst podrobnosti o objektu blob rozšířený server auditování zásady nakonfigurované na daném serveru |
> | Akce | Microsoft.Sql/servers/extendedAuditingSettings/write | Změnit server rozšířených auditování objektů blob pro daný server |
> | Akce | Microsoft.Sql/servers/failoverGroups/delete | Odstraní existující skupinu pro převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/failover/action | Provede plánované převzetí služeb při selhání v existující skupině převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Provede Vynucené převzetí služeb při selhání v existující skupině převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/failoverGroups/read | Vrátí seznam převzetí služeb při selhání skupiny nebo načte vlastnosti zadaného převzetí služeb při selhání skupiny. |
> | Akce | Microsoft.Sql/servers/failoverGroups/write | Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti a značky pro skupinu zadaný převzetí služeb při selhání. |
> | Akce | Microsoft.Sql/servers/firewallRules/delete | Odstraní existující pravidlo brány firewall serveru. |
> | Akce | Microsoft.Sql/servers/firewallRules/read | Vrátí seznam serverová brána firewall, pravidla nebo získá vlastnosti pro zadaný server pravidlo brány firewall. |
> | Akce | Microsoft.Sql/servers/firewallRules/write | Vytvoří pravidlo brány firewall serveru se zadanými parametry, aktualizovat vlastnosti pro zadané pravidlo nebo přepsat všechny existující pravidla nové pravidel brány firewall serveru. |
> | Akce | Microsoft.Sql/servers/import/action | Vytvoření nové databáze na serveru a nasadit schéma a data z balíčku DacPac |
> | Akce | Microsoft.Sql/servers/importExportOperationResults/read | Získá probíhající operace importu a exportu |
> | Akce | Microsoft.Sql/servers/keys/delete | Odstraní existující klíč serveru. |
> | Akce | Microsoft.Sql/servers/keys/read | Vrátí seznam serveru klíče nebo získá vlastnosti pro klíč zadaný server. |
> | Akce | Microsoft.Sql/servers/keys/write | Vytvoří klíč se zadanými parametry nebo aktualizovat vlastnosti a značky pro klíč zadaný server. |
> | Akce | Microsoft.Sql/servers/networkInterfaces/read | Vrací vlastnosti pro zadané síťové rozhraní |
> | Akce | Microsoft.Sql/servers/networkInterfaces/write | Vytvoří rozhraní sítě se zadanými parametry nebo aktualizuje vlastnosti a značky pro zadané síťové rozhraní |
> | Akce | Microsoft.Sql/servers/operationResults/read | Získá serveru v průběhu operace |
> | Akce | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Návratové typy metriky, které jsou k dispozici pro servery |
> | Akce | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Akce | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Načtení metriky pro fondy doporučené elastické databáze pro daný server |
> | Akce | Microsoft.Sql/servers/recommendedElasticPools/read | Načtení doporučení pro fondy elastické databáze pro snížení nákladů na nebo vylepšení výkonu na základě využití prostředků historica |
> | Akce | Microsoft.Sql/servers/recoverableDatabases/read | Tato operace se používá pro zotavení po havárii živé databáze k obnovení databáze pro poslední známá funkční bodu zálohy. Vrací informace o poslední správné zálohy ale doesn\u0027t ve skutečnosti obnovit databázi. |
> | Akce | Microsoft.Sql/servers/restorableDroppedDatabases/read | Získáte seznam databází, které byly vyřazeny na daném serveru, které jsou stále v rámci zásady uchovávání informací. |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Načíst výsledky operace zápisu zásady detekce hrozeb serveru |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/read | Načíst podrobnosti o zásadě detekce hrozeb server nakonfigurovaný na daném serveru |
> | Akce | Microsoft.Sql/servers/securityAlertPolicies/write | Změnit zásady detekce hrozeb serveru pro daný server |
> | Akce | Microsoft.Sql/servers/serviceObjectives/read | Načtení seznamu cílů na úrovni služby (také označované jako úrovně výkonu) k dispozici na daném serveru |
> | Akce | Microsoft.Sql/servers/syncAgents/delete | Odstraní stávající agenta synchronizace. |
> | Akce | Microsoft.Sql/servers/syncAgents/generateKey/action | Vygenerování klíče registrace agenta synchronizace |
> | Akce | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Vrátí seznam agenta propojené databáze synchronizace |
> | Akce | Microsoft.Sql/servers/syncAgents/read | Vrátí seznam agentů synchronizace nebo získá vlastnosti pro zadaný synchronizace agenta. |
> | Akce | Microsoft.Sql/servers/syncAgents/write | Vytvoří agenta synchronizace se zadanými parametry nebo aktualizovat vlastnosti pro zadaný synchronizace agenta. |
> | Akce | Microsoft.Sql/servers/usages/read | Vrátí kvóty DTU serveru a aktuální consuption DTU všemi databázemi v rámci serveru |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/delete | Odstraní existující pravidlo virtuální sítě |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/read | Vrátí seznam virtuální sítě pravidla nebo získá vlastnosti pro pravidlo zadané virtuální síti. |
> | Akce | Microsoft.Sql/servers/virtualNetworkRules/write | Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální síti. |
> | Akce | Microsoft.Sql/servers/write | Vytvoří server, se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server. |
> | Akce | Microsoft.Sql/unregister/action | Zrušení registrace předplatného pro poskytovatele prostředků Microsoft SQL Database a umožňuje vytvoření databáze Microsoft SQL. |
> | Akce | Microsoft.Sql/virtualClusters/read | Vrátí seznam virtuálních clusterů nebo získá vlastnosti pro zadanou virtuální cluster. |
> | Akce | Microsoft.Sql/virtualClusters/write | Aktualizace virtuálního clusteru značky. |

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
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Vrátí výsledek přidávání obsahu objektu blob. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstraňování objektu blob. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů blob. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu objektu blob. |
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
> | Akce | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Získá seznam definic Metrik úložišť Microsoft. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/read | Vrátí vlastnosti nebo statistiky služby Blob Service. |
> | Akce | Microsoft.Storage/storageAccounts/blobServices/write | Vrátí výsledek vložení vlastností služby Blob Service. |
> | Akce | Microsoft.Storage/storageAccounts/delete | Odstraní existující účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/fileShare/delete | Umožňuje uživateli odstranit sdílenou složku. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/fileShare/read | Umožňuje uživateli číst sdílenou složku. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/fileShare/write | Umožňuje uživateli zapisovat do sdílené složky. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Získá seznam definic Metrik úložišť Microsoft. |
> | Akce | Microsoft.Storage/storageAccounts/listAccountSas/action | Vrátí token SAS určeného účtu úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/listServiceSas/action | Vrátí token SAS služby pro určený účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Získá seznam definic Metrik úložišť Microsoft. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Získá seznam definic Metrik úložišť Microsoft. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Vrátí výsledek odstranění fronty. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Vrátí výsledek přidání zprávy. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Vrátí výsledek odstranění zprávy. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Vrátí výsledek zpracování zprávy. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Vrátí zprávu. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Vrátí výsledek zápisu zprávy. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/queues/write | Vrátí výsledek zápisu fronty. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/read | Vrátí vlastnosti nebo statistiky služby Queue. |
> | Akce | Microsoft.Storage/storageAccounts/queueServices/write | Vrátí výsledky nastavení vlastností služby Queue. |
> | Akce | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Akce | Microsoft.Storage/storageAccounts/regeneratekey/action | Obnoví přístupové klíče pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky účtu úložiště. |
> | Akce | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky prostředku. |
> | Akce | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Získá seznam definic Metrik úložišť Microsoft. |
> | Akce | Microsoft.Storage/storageAccounts/write | Vytvoří účet úložiště se zadanými parametry, aktualizuje vlastnosti a značky nebo přidá vlastní doménu pro zadaný účet úložiště. |
> | Akce | Microsoft.Storage/usages/read | Vrací limit a aktuální počet použití pro zdroje v zadaném odběru |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | microsoft.storagesync/storageSyncServices/delete | Odstranit všechny synchronizační služby úložiště |
> | Akce | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro úložiště synchronizační služby |
> | Akce | microsoft.storagesync/storageSyncServices/read | Číst všechny synchronizační služby úložiště |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/delete | Odstranit libovolný zaregistrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/read | Číst všechny zaregistrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/registeredServers/write | Vytvořit nebo aktualizovat všechny zaregistrovaný Server |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Odstranit žádné koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Umístění rozhraní api pro asynchronní volání zálohování |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Volat tuto akci po dokončení zálohování |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Volat tuto akci po obnovení |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Volat tuto akci před zálohováním |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Tato akce před obnovení volání |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Číst všechny koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Obnovení prezenčního signálu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Vytvořit nebo aktualizovat žádné koncové body cloudu |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/delete | Odstranit všechny skupiny pro synchronizaci |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/read | Číst všechny skupiny pro synchronizaci |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Odstranit žádné koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Číst všechny koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Volat tuto akci vrátit soubory na server |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Vytvořit nebo aktualizovat žádné koncové body serveru |
> | Akce | microsoft.storagesync/storageSyncServices/syncGroups/write | Vytvořit nebo aktualizovat všechny skupiny pro synchronizaci |
> | Akce | microsoft.storagesync/storageSyncServices/write | Vytvořit nebo aktualizovat všechny synchronizační služby úložiště |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/delete | Odstraní záznamy řízení přístupu |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/read | Uvádí nebo získá záznamy řízení přístupu |
> | Akce | Microsoft.StorSimple/managers/accessControlRecords/write | Vytvořit nebo aktualizovat záznamy řízení přístupu |
> | Akce | Microsoft.StorSimple/managers/alerts/read | Uvádí nebo získá výstrahy |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/delete | Odstraní existující nastavení šířky pásma (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/read | Seznam nastavení šířky pásma (pouze 8000 řada) |
> | Akce | Microsoft.StorSimple/managers/bandwidthSettings/write | Vytvoří novou nebo aktualizuje nastavení šířky pásma (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/Managers/certificates/write | Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů. |
> | Akce | Microsoft.StorSimple/managers/clearAlerts/action | Vymažte všechny výstrahy spojené se Správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Seznam zařízení cloudu podporované konfigurace |
> | Akce | Microsoft.StorSimple/managers/configureDevice/action | Konfiguruje zařízení |
> | Akce | Microsoft.StorSimple/managers/delete | Odstraní Správci zařízení |
> | Akce | Microsoft.StorSimple/Managers/delete | Operace odstranění trezoru Odstraní zadaný prostředek Azure typu 'trezoru. |
> | Akce | Microsoft.StorSimple/managers/devices/alertSettings/read | Uvádí nebo získá nastavení výstrah |
> | Akce | Microsoft.StorSimple/managers/devices/alertSettings/write | Vytvořit nebo aktualizovat nastavení výstrah |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Proveďte zálohu ručně vytvořit na vyžádání zálohování všech svazků chráněnými zásadami. |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Odstraní existující zásady zálohování (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/read | Seznam zálohování zásady (pouze 8000 řada) |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Odstraní existující plán |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Seznam plány |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Vytvoří novou nebo aktualizovat plány |
> | Akce | Microsoft.StorSimple/managers/devices/backupPolicies/write | Vytvoří novou nebo aktualizovat zásady zálohování (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/backups/delete | Odstraní zálohovacího skladu |
> | Akce | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klonování sdílené složky nebo svazku pomocí zálohování elementu. |
> | Akce | Microsoft.StorSimple/managers/devices/backups/read | Uvádí nebo získá zálohovacího skladu |
> | Akce | Microsoft.StorSimple/managers/devices/backups/restore/action | Všechny svazky obnovte ze zálohovacího skladu. |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Odstraní plán zálohování skupiny |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Uvádí nebo získá skupiny plánu zálohování |
> | Akce | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Vytvořit nebo aktualizovat skupiny plánu zálohování |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/delete | Odstraní nastavení Chap |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/read | Uvádí nebo získá nastavení Chap |
> | Akce | Microsoft.StorSimple/managers/devices/chapSettings/write | Vytvořit nebo aktualizovat nastavení Chap |
> | Akce | Microsoft.StorSimple/managers/devices/deactivate/action | Deaktivuje zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/delete | Odstraní zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/download/action | Stažení aktualizací pro zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/failover/action | Převzetí služeb při selhání zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Proveďte zálohování souborového serveru. |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/delete | Odstraní souborových serverů |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Uvádí nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Uvádí nebo získá definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/read | Uvádí nebo získá souborových serverů |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Odstraní sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Uvádí nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Uvádí nebo získá definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Uvádí nebo získá sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Vytvořit nebo aktualizovat sdílené složky |
> | Akce | Microsoft.StorSimple/managers/devices/fileservers/write | Vytvořit nebo aktualizovat souborových serverů |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Změnit stav napájení řadiče hardwaru skupiny součástí |
> | Akce | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Seznam skupin součástí hardwaru |
> | Akce | Microsoft.StorSimple/managers/devices/install/action | Instalaci aktualizací na zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/installUpdates/action | Nainstaluje aktualizace zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Proveďte zálohování serveru iSCSI. |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Odstraní iSCSI servery |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Odstraní disky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Uvádí nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Uvádí nebo získá definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Uvádí nebo získá disky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Vytvořit nebo aktualizovat disky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Uvádí nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Uvádí nebo získá definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/read | Uvádí nebo získá iSCSI servery |
> | Akce | Microsoft.StorSimple/managers/devices/iscsiservers/write | Vytvořit nebo aktualizovat iSCSI servery |
> | Akce | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Zrušení spuštěné úlohy |
> | Akce | Microsoft.StorSimple/managers/devices/jobs/read | Uvádí nebo získá úlohy |
> | Akce | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Zobrazí seznam sady převzetí služeb při selhání ze stávajících zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Seznam cílů převzetí služeb při selhání zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/metrics/read | Uvádí nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Uvádí nebo získá definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Potvrzuje úspěšné migrace a potvrďte ho. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Načtěte stav potvrdit migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Načtěte stav odhad úlohy migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Načtěte stav migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Import konfigurace zdroje pro migraci. |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Spustit migraci pomocí zdroj konfigurace |
> | Akce | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Spustíte úlohu k odhad doby trvání procesu migrace. |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/read | Uvádí nebo získá nastavení sítě |
> | Akce | Microsoft.StorSimple/managers/devices/networkSettings/write | Vytvoří novou nebo aktualizovat nastavení sítě |
> | Akce | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Seznam veřejný šifrovací klíč Správce zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publikujte balíček pro podporu zařízení pro řešení potíží s Microsoft Support. |
> | Akce | Microsoft.StorSimple/managers/devices/read | Uvádí nebo získá zařízení |
> | Akce | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Vyhledávání aktualizací v zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/read | Seznam nastavení zabezpečení |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synchronizujte certifikát vzdálené správy pro zařízení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aktualizujte nastavení zabezpečení. |
> | Akce | Microsoft.StorSimple/managers/devices/securitySettings/write | Vytvoří novou nebo aktualizuje nastavení zabezpečení |
> | Akce | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Odeslat testovací výstrahy e-mailu k příjemce nakonfigurované e-mailů. |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/read | Uvádí nebo získá nastavení času |
> | Akce | Microsoft.StorSimple/managers/devices/timeSettings/write | Vytvoří novou nebo aktualizuje nastavení času |
> | Akce | Microsoft.StorSimple/managers/devices/updateSummary/read | Uvádí nebo získá souhrn aktualizace |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Odstraní existující kontejnery svazků (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Seznam šifrování klíče kontejnery svazků |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Seznam metriky |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Seznam definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/read | Seznam kontejnery svazků (pouze 8000 řada) |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Šifrování klíče výměny kontejnery svazků |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Odstraní existující svazky |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Seznam metriky |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Seznam definice metrik |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Seznam svazků |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Vytvoří novou nebo aktualizuje svazky |
> | Akce | Microsoft.StorSimple/managers/devices/volumeContainers/write | Vytvoří novou nebo aktualizuje kontejnery svazků (pouze řady 8000) |
> | Akce | Microsoft.StorSimple/managers/devices/write | Vytvořit nebo aktualizovat zařízení |
> | Akce | Microsoft.StorSimple/managers/encryptionSettings/read | Uvádí nebo získá nastavení šifrování |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/delete | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/Managers/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Akce | Microsoft.StorSimple/managers/getActivationKey/action | Získáte aktivační klíč pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/getEncryptionKey/action | Získání šifrovacího klíče pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/listActivationKey/action | Získá aktivační klíč Správce zařízení StorSimple. |
> | Akce | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Získá privátní šifrovací klíč pro správce zařízení StorSimple. |
> | Akce | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Seznam veřejný šifrovací klíče nástroje Správce zařízení StorSimple. |
> | Akce | Microsoft.StorSimple/managers/metrics/read | Uvádí nebo získá metriky |
> | Akce | Microsoft.StorSimple/managers/metricsDefinitions/read | Uvádí nebo získá definice metrik |
> | Akce | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Vytvořte nové zařízení cloudu. |
> | Akce | Microsoft.StorSimple/managers/read | Uvádí nebo získá správce zařízení |
> | Akce | Microsoft.StorSimple/Managers/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Akce | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Znovu vygenerujte registrační certifikát pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/regenerateActivationKey/action | Znovu vygenerujte aktivační klíč pro správce zařízení. |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Odstraní údaje k účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | Seznam přístupových klíčů přihlašovacích údajů účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/read | Uvádí nebo získá přihlašovací údaje účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageAccountCredentials/write | Vytvořit nebo aktualizovat údaje k účtu úložiště |
> | Akce | Microsoft.StorSimple/managers/storageDomains/delete | Odstraní domén úložiště |
> | Akce | Microsoft.StorSimple/managers/storageDomains/read | Uvádí nebo získá domén úložiště |
> | Akce | Microsoft.StorSimple/managers/storageDomains/write | Vytvořit nebo aktualizovat domén úložiště |
> | Akce | Microsoft.StorSimple/managers/write | Vytvořit nebo aktualizovat Správci zařízení |
> | Akce | Microsoft.StorSimple/Managers/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.StreamAnalytics/locations/quotas/Read | Kvóty předplatného analýzy datového proudu pro čtení |
> | Akce | Microsoft.StreamAnalytics/operations/Read | Čtení operací analýz datového proudu |
> | Akce | Microsoft.StreamAnalytics/Register/action | Registrovat předplatné u poskytovatele prostředků Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Delete | Odstranit úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Odstranit funkci úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Přečtěte si výsledky operace pro funkci úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Číst Stream Analytics úlohy funkce |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Načíst výchozí definici funkce úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics úlohy funkce |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Write – funkce úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Odstranit vstupu úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Výsledky operace čtení pro vstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Číst Stream Analytics úlohy vstup |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Ukázka Stream Analytics úlohy vstup |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Test Stream Analytics úlohy vstup |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Zápis vstupu úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Číst definice metrik |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Přečtěte si výsledky operace úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Odstranit výstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Výsledky operace čtení pro výstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Výstup úlohy analýzy datového proudu pro čtení |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Výstup úlohy analýzy datového proudu testu |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Zapisovat výstup úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Nastavení diagnostiky pro čtení. |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Zápis nastavení diagnostiky. |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro streamingjobs |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro streamingjobs |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Read | Úlohy Stream Analytics pro čtení |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Start/action | Spuštění úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Zastavení úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Odstranit transformaci úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Číst Stream Analytics úlohy transformace |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Zápis transformace úlohy Stream Analytics |
> | Akce | Microsoft.StreamAnalytics/streamingjobs/Write | Zápis úlohy Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Subscription/CreateSubscription/action | Vytvořit předplatné Azure |
> | Akce | Microsoft.Subscription/SubscriptionDefinitions/read | Získáte definici předplatného Azure v rámci skupiny pro správu. |
> | Akce | Microsoft.Subscription/SubscriptionDefinitions/write | Vytvořit definici předplatného Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Support/register/action | Registruje zprostředkovatele prostředků podpory. |
> | Akce | Microsoft.Support/supportTickets/read | Umožňuje získat podrobnosti lístku podpory (včetně stavu, závažnosti, podrobností kontaktu a komunikací) nebo seznam lístků podpory pro všechna předplatná. |
> | Akce | Microsoft.Support/supportTickets/write | Vytvoří nebo aktualizuje lístek podpory. Můžete vytvořit lístek podpory pro Technical, fakturace, kvóty nebo správy předplatného související problémy. Můžete aktualizovat závažnost, podrobnosti kontaktu a komunikace pro existující lístky žádostí o podporu. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Odstraní zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Umožňuje získáte vlastnosti zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Vytvoří nové zásady přístupu pro prostředí nebo aktualizuje existující zásady přístupu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/delete | Odstraní prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Odstraní zdroj události. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro eventsources |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/read | Umožňuje získáte vlastnosti zdroje událostí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/eventsources/write | Vytvoří nový zdroj událostí pro prostředí nebo aktualizuje existující zdroje událostí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Načte dostupné metriky pro prostředí |
> | Akce | Microsoft.TimeSeriesInsights/environments/read | Umožňuje získáte vlastnosti prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Odstraní odkaz na datovou sadu. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Umožňuje získáte vlastnosti referenční datové sady. |
> | Akce | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Vytvoří nový odkaz datové sady pro prostředí nebo aktualizuje existující sadu dat odkaz. |
> | Akce | Microsoft.TimeSeriesInsights/environments/status/read | Načíst stav prostředí, stav jeho přidružené operací, jako je příjem příchozích dat. |
> | Akce | Microsoft.TimeSeriesInsights/environments/write | Vytvoří nového prostředí nebo aktualizuje stávající prostředí. |
> | Akce | Microsoft.TimeSeriesInsights/register/action | Zaregistruje předplatné pro poskytovatele prostředků časové řady přehledy a povolí vytvoření prostředí Statistika časové řady. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.VisualStudio/Account/Delete | Odstranit účet |
> | Akce | Microsoft.VisualStudio/Account/Read | Účet pro čtení |
> | Akce | Microsoft.VisualStudio/Account/Write | Nastavit účet |
> | Akce | Microsoft.VisualStudio/Extension/Delete | Odstranit rozšíření |
> | Akce | Microsoft.VisualStudio/Extension/Read | Rozšíření pro čtení |
> | Akce | Microsoft.VisualStudio/Extension/Write | Nastavit rozšíření |
> | Akce | Microsoft.VisualStudio/Project/Delete | Odstranit projekt |
> | Akce | Microsoft.VisualStudio/Project/Read | Projekt pro čtení |
> | Akce | Microsoft.VisualStudio/Project/Write | Nastavte projekt |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.Web/apimanagementaccounts/apiacls/Read | Získáte rozhraní Api správy účtů Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/apiacls/DELETE | Odstraňte rozhraní Api správy účtů rozhraní API Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Read | Získáte rozhraní Api správy účtů rozhraní API Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Aktualizujte rozhraní Api správy účtů rozhraní API Apiacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/connectionacls/Read | Získáte rozhraní Api správy účtů rozhraní API Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Potvrďte souhlas kódu rozhraní Api správy účtů rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/DELETE | Odstraňte rozhraní Api správy účtů rozhraní API připojení Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Read | Získáte rozhraní Api správy účtů rozhraní API připojení Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Aktualizujte rozhraní Api správy účtů rozhraní API připojení Connectionacls. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Odstraňte rozhraní Api správy účtů rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Získáte odkazy souhlasu pro rozhraní Api správy účtů rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Seznam připojení klíče rozhraní Api správy účtů rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Seznam tajné klíče rozhraní Api správy účtů rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/Read | Získáte rozhraní Api správy účtů rozhraní API pro připojení. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Aktualizujte připojení rozhraní Api správy účtů rozhraní API. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Odstraňte rozhraní API účty pro správu rozhraní Api. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/DELETE | Odstranit rozhraní Api správy rozhraní API pro účty lokalizované definice. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Získat rozhraní Api správy rozhraní API pro účty lokalizované definice. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Rozhraní API účty pro správu rozhraní Api aktualizace lokalizované definice. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Read | Získáte rozhraní API účty pro správu rozhraní Api. |
> | Akce | Microsoft.Web/apimanagementaccounts/APIs/Write | Aktualizujte rozhraní API účty pro správu rozhraní Api. |
> | Akce | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Získáte rozhraní Api správy účtů Connectionacls. |
> | Akce | Microsoft.Web/availablestacks/Read | Získáte zásobníky k dispozici. |
> | Akce | Microsoft.Web/billingmeters/Read | Získejte seznam fakturace měřidla. |
> | Akce | Microsoft.Web/certificates/Delete | Odstraňte existující certifikát. |
> | Akce | Microsoft.Web/certificates/Read | Získáte seznam certifikátů. |
> | Akce | Microsoft.Web/certificates/Write | Přidat nový certifikát nebo aktualizovat existující. |
> | Akce | Microsoft.Web/checknameavailability/Read | Zkontrolujte, zda název prostředku je k dispozici. |
> | Akce | Microsoft.Web/classicmobileservices/Read | Získáte Classic mobilních služeb. |
> | Akce | Microsoft.Web/connectionGateways/Delete | Odstraní připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Join/Action | Spojí připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/ListStatus/Action | Zobrazí stav připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Move/Action | Přesune připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Read | Získejte seznam připojení brány. |
> | Akce | Microsoft.Web/connectionGateways/Write | Vytvoří nebo aktualizuje připojení brány. |
> | Akce | Microsoft.Web/Connections/confirmconsentcode/Action | Potvrďte souhlas kód připojení. |
> | Akce | Microsoft.Web/connections/Delete | Odstraní připojení. |
> | Akce | Microsoft.Web/connections/Join/Action | Spojí připojení. |
> | Akce | Microsoft.Web/Connections/listconsentlinks/Action | Seznam odkazů souhlasu pro připojení. |
> | Akce | Microsoft.Web/connections/Move/Action | Přesune připojení. |
> | Akce | Microsoft.Web/connections/Read | Získejte seznam připojení. |
> | Akce | Microsoft.Web/connections/Write | Vytvoří nebo aktualizuje připojení. |
> | Akce | Microsoft.Web/customApis/Delete | Odstraní vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Definice rozhraní API extrahuje z WSDL. |
> | Akce | Microsoft.Web/customApis/Join/Action | Spojí vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/listWsdlInterfaces/Action | Seznam WSDL rozhraní pro vlastní API. |
> | Akce | Microsoft.Web/customApis/Move/Action | Přesune vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/Read | Získejte seznam vlastní rozhraní API. |
> | Akce | Microsoft.Web/customApis/Write | Vytvoří nebo aktualizuje vlastní API. |
> | Akce | Microsoft.Web/deletedSites/Read | Umožňuje získat vlastnosti odstranit webové aplikace |
> | Akce | Microsoft.Web/deploymentlocations/Read | Získáte umístění nasazení. |
> | Akce | Microsoft.Web/geoRegions/Read | Získejte seznam geografických oblastech. |
> | Akce | Microsoft.Web/hostingenvironments/capacities/Read | Získat hostování prostředí kapacity. |
> | Akce | Microsoft.Web/hostingEnvironments/Delete | Odstranění služby App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/detectors/Read | Získat hostování detektory prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/Diagnostics/Read | Získat hostování prostředí diagnostiky. |
> | Akce | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Získáte koncových bodů sítě všechny příchozí závislostí. |
> | Akce | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Získat hostování prostředí definice metrik. |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Získat hostování definice metrik u fondy prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Získat hostování prostředí u fondy metriky. |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Načte dostupné metriky pro MultiRole prostředí App Service |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Umožňuje získat vlastnosti fondu front-endu ve službě App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Získat hostování prostředí u fondy SKU. |
> | Akce | Microsoft.Web/hostingenvironments/multirolepools/usages/Read | Získat hostování prostředí u fondy použití. |
> | Akce | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Vytvořit nový fond front-endové služby App Service Environment, nebo aktualizaci existující |
> | Akce | Microsoft.Web/hostingenvironments/Operations/Read | Získat hostování prostředí operace. |
> | Akce | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Získáte koncových bodů sítě všechny odchozí závislostí. |
> | Akce | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Web/hostingEnvironments/Read | Umožňuje získat vlastnosti služby App Service Environment |
> | Akce | Microsoft.Web/hostingEnvironments/reboot/Action | Restartujte všechny počítače ve službě App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/Resume/Action | Obnovte hostitelská prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/serverfarms/Read | Získáte hostování prostředí plány služby App Service. |
> | Akce | Microsoft.Web/hostingenvironments/Sites/Read | Získáte hostování prostředí webové aplikace. |
> | Akce | Microsoft.Web/hostingenvironments/suspend/Action | Pozastavte hostitelská prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/usages/Read | Získat hostování použití prostředí. |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Získat hostování prostředí Workerpools metrika definice. |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Získat hostování prostředí Workerpools metriky. |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Načte dostupné metriky pro WorkerPool prostředí App Service |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/Read | Umožňuje získat vlastnosti fondu pracovních procesů ve službě App Service Environment |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Získat hostování prostředí Workerpools SKU. |
> | Akce | Microsoft.Web/hostingenvironments/workerpools/usages/Read | Získat hostování prostředí Workerpools použití. |
> | Akce | Microsoft.Web/hostingEnvironments/workerPools/Write | Vytvořit nový fond pracovních procesů ve službě App Service Environment nebo aktualizovat stávající |
> | Akce | Microsoft.Web/hostingEnvironments/Write | Vytvoření nové služby App Service Environment nebo aktualizovat existující |
> | Akce | Microsoft.Web/ishostingenvironmentnameavailable/Read | Získáte, pokud je k dispozici hostování název prostředí. |
> | Akce | Microsoft.Web/ishostnameavailable/Read | Zkontrolujte, zda název hostitele je k dispozici. |
> | Akce | Microsoft.Web/isusernameavailable/Read | Zkontrolujte, zda uživatelské jméno je k dispozici. |
> | Akce | Microsoft.Web/listSitesAssignedToHostName/Read | Získáte názvy lokalit, které jsou přiřazeny k hostitele. |
> | Akce | Microsoft.Web/Locations/apioperations/Read | Získejte operace umístění rozhraní API. |
> | Akce | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Získáte umístění připojení brány instalace. |
> | Akce | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Extrahuje definice rozhraní Api z WSDL pro umístění. |
> | Akce | Microsoft.Web/Locations/listwsdlinterfaces/Action | Seznam WSDL rozhraní pro umístění. |
> | Akce | Microsoft.Web/Locations/managedapis/apioperations/Read | Získáte operace umístění spravované rozhraní API. |
> | Akce | Microsoft.Web/locations/managedapis/Join/Action | Spojí spravované rozhraní API. |
> | Akce | Microsoft.Web/Locations/managedapis/Read | Získáte umístění spravovaná rozhraní API. |
> | Akce | Microsoft.Web/Operations/Read | Získejte operace. |
> | Akce | Microsoft.Web/publishingusers/Read | Uživatelé získat publikování. |
> | Akce | Microsoft.Web/publishingusers/Write | Aktualizace publikování uživatelů. |
> | Akce | Microsoft.Web/recommendations/Read | Získejte seznam doporučení pro předplatné. |
> | Akce | Microsoft.Web/Register/Action | Registrace poskytovatele prostředků Microsoft.Web pro předplatné. |
> | Akce | Microsoft.Web/resourcehealthmetadata/Read | Získáte Metadata stavu prostředků. |
> | Akce | Microsoft.Web/serverfarms/Capabilities/Read | Získáte možnosti plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Delete | Odstranit existující plán služby App Service |
> | Akce | Microsoft.Web/serverfarms/firstpartyapps/Settings/DELETE | Odstraňte první strany aplikace nastavení plánů služby App Service. |
> | Akce | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | Získáte nastavení první strany aplikací plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Aktualizujte nastavení první strany aplikací plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/DELETE | Odstranění služby App Service plány hybridní připojení předávací službu obory názvů. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | Získáte služby App Service plány hybridní připojení předávací službu obory názvů. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | Získáte plány služby App Service hybridní připojení obory názvů předávací webové aplikace. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | Získáte limity plán služby App Service plány hybridní připojení. |
> | Akce | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | Získáte předávací připojení hybridní plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/metricdefinitions/Read | Získáte definice metrik plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Metrics/Read | Získáte metriky plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/operationresults/Read | Získáte výsledky operace plány služby App Service. |
> | Akce | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Načte dostupné metriky pro plán služby App Service |
> | Akce | Microsoft.Web/serverfarms/Read | Umožňuje získat vlastnosti na plán služby App Service |
> | Akce | Microsoft.Web/serverfarms/restartSites/Action | Restartujte všechny webové aplikace v plán služby App Service |
> | Akce | Microsoft.Web/serverfarms/Sites/Read | Získáte webové aplikace plány aplikační služby. |
> | Akce | Microsoft.Web/serverfarms/skus/Read | Získáte SKU plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/usages/Read | Získáte použití plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/gateways/Write | Aktualizace brány připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Získání připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/DELETE | Odstraňte směrování připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/Read | Získáte trasy připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/virtualnetworkconnections/Routes/Write | Aktualizace tras připojení virtuální sítě plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/workers/reboot/Action | Restartování pracovních procesů plány služby App Service. |
> | Akce | Microsoft.Web/serverfarms/Write | Nový plán služby App Service umožňuje vytvořit nebo aktualizovat stávající |
> | Akce | Microsoft.Web/Sites/analyzecustomhostname/Read | Analýza vlastní název hostitele. |
> | Akce | Microsoft.Web/sites/applySlotConfig/Action | Použít konfiguraci webové aplikace slotu z cílového slotu na aktuální webové aplikace |
> | Akce | Microsoft.Web/sites/backup/Action | Vytvoří novou zálohu webové aplikace |
> | Akce | Microsoft.Web/Sites/Backup/Read | Získáte zálohování webové aplikace. |
> | Akce | Microsoft.Web/Sites/Backup/Write | Aktualizace webové aplikace zálohování. |
> | Akce | Microsoft.Web/Sites/backups/Action | Zjistí zálohu existující aplikace, kterou lze obnovit z objektu blob v úložišti Azure. |
> | Akce | Microsoft.Web/Sites/backups/DELETE | Odstraňte webových aplikací zálohování. |
> | Akce | Microsoft.Web/Sites/backups/list/Action | Seznam webových aplikací zálohování. |
> | Akce | Microsoft.Web/sites/backups/Read | Umožňuje získat vlastnosti zálohování webové aplikace |
> | Akce | Microsoft.Web/Sites/backups/Restore/Action | Obnovte zálohy webové aplikace. |
> | Akce | Microsoft.Web/Sites/backups/Write | Aktualizujte webovou aplikací zálohování. |
> | Akce | Microsoft.Web/Sites/config/DELETE | Odstraňte konfigurační webové aplikace. |
> | Akce | Microsoft.Web/sites/config/list/Action | Seznam webové aplikace citlivé nastavení zabezpečení, jako je například publikování přihlašovací údaje, nastavení aplikace a připojovacích řetězců |
> | Akce | Microsoft.Web/sites/config/Read | Získat nastavení konfigurace webové aplikace |
> | Akce | Microsoft.Web/sites/config/Write | Aktualizovat nastavení konfigurace webové aplikace |
> | Akce | Microsoft.Web/Sites/containerlogs/Action | Získat ZIP kontejneru protokoly pro webovou aplikaci. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/DELETE | Odstranění webové aplikace nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/Read | Získáte webové aplikace nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Spuštění webové aplikace nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/Sites/continuouswebjobs/stop/Action | Zastavení webové aplikace nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/sites/Delete | Odstranit stávající webovou aplikaci |
> | Akce | Microsoft.Web/Sites/Deployments/DELETE | Odstraňte nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Deployments/log/Read | Získání protokolu nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Deployments/Read | Získá nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Deployments/Write | Aktualizace nasazení webové aplikace. |
> | Akce | Microsoft.Web/Sites/detectors/Read | Získáte detektory webové aplikace. |
> | Akce | microsoft.web/sites/diagnostics/analyses/execute/Action | Spuštění webové aplikace diagnostiky analýzy. |
> | Akce | Microsoft.Web/Sites/Diagnostics/analyses/Read | Získáte webové analýzy diagnostiky aplikace. |
> | Akce | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Získání diagnostiky webové aplikace pro aplikaci ASP.NET Core. |
> | Akce | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Získáte funkce Autoheal webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/Deployment/Read | Získání diagnostiky aplikací nasazení webu. |
> | Akce | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Získá nasazení webové aplikace diagnostiky. |
> | Akce | microsoft.web/sites/diagnostics/detectors/execute/Action | Spuštění webové aplikace diagnostiky detektor. |
> | Akce | Microsoft.Web/Sites/Diagnostics/detectors/Read | Získáte detektor webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Získáte webové aplikace diagnostiky neúspěšných požadavků na identifikátor Uri. |
> | Akce | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Získáte webové analýzy FREB diagnostiky aplikace. |
> | Akce | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Získáte webové aplikace diagnostiky protokolu analyzátoru. |
> | Akce | Microsoft.Web/Sites/Diagnostics/Read | Získáte webové aplikace diagnostiky kategorií. |
> | Akce | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Získáte dostupnosti webové aplikace diagnostiky modulu Runtime. |
> | Akce | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Získání stavu služby webových aplikací diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Získáte analýza procesoru webu webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Získáte webové aplikace diagnostiky webu dojde k chybě. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Získáte webové aplikace diagnostiky lokality latence. |
> | Akce | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Získáte analýza paměti webu webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Získáte aktualizace nastavení webové aplikace diagnostiky lokality restartování. |
> | Akce | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Získáte webové aplikace diagnostiky lokality restartování uživatelem iniciované. |
> | Akce | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Získáte prohození webové aplikace diagnostiky lokality. |
> | Akce | Microsoft.Web/Sites/Diagnostics/ThreadCount/Read | Získáte počet vláken webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Získáte Workeravailability webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Získáte recyklace procesu Worker webové aplikace diagnostiky. |
> | Akce | Microsoft.Web/Sites/domainownershipidentifiers/Read | Získání identifikátorů vlastnictví webové aplikace domény. |
> | Akce | Microsoft.Web/Sites/domainownershipidentifiers/Write | Aktualizace webové aplikace domény vlastnictví identifikátory. |
> | Akce | Microsoft.Web/Sites/Functions/Action | Funkce Web Apps. |
> | Akce | Microsoft.Web/Sites/Functions/DELETE | Odstraňte funkce webové aplikace. |
> | Akce | Microsoft.Web/Sites/Functions/listsecrets/Action | Funkce, webové aplikace seznamu tajných klíčů. |
> | Akce | Microsoft.Web/Sites/Functions/masterkey/Read | Získáte Masterkey funkce webové aplikace. |
> | Akce | Microsoft.Web/Sites/Functions/Read | Získáte funkce Web Apps. |
> | Akce | Microsoft.Web/Sites/Functions/token/Read | Get webové aplikace funkce Token. |
> | Akce | Microsoft.Web/Sites/Functions/Write | Aktualizace funkcí webové aplikace. |
> | Akce | Microsoft.Web/Sites/hostnamebindings/DELETE | Odstranění webové aplikace Hostname vazby. |
> | Akce | Microsoft.Web/Sites/hostnamebindings/Read | Získáte webové aplikace Hostname vazby. |
> | Akce | Microsoft.Web/Sites/hostnamebindings/Write | Update Web Apps Hostname Bindings. |
> | Akce | Microsoft.Web/Sites/hybridconnection/DELETE | Odstranění webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnection/Read | Získáte webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnection/Write | Aktualizace webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Odstranění webové aplikace hybridní připojení obory názvů předávání. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Seznam klíčů webové aplikace hybridní připojení obory názvů předávání. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Získáte předávací službu obory názvů webové aplikace hybridní připojení. |
> | Akce | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Aktualizace webové aplikace hybridní připojení obory názvů předávání. |
> | Akce | Microsoft.Web/Sites/hybridconnectionrelays/Read | Získáte webové aplikace hybridní připojení předávání. |
> | Akce | Microsoft.Web/Sites/instances/Deployments/DELETE | Odstraňte nasazení instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Deployments/Read | Získá nasazení instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Extensions/log/Read | Získáte webové aplikace instance rozšíření protokolu. |
> | Akce | Microsoft.Web/Sites/instances/Extensions/Read | Získáte rozšíření instance webových aplikací. |
> | Akce | Microsoft.Web/Sites/instances/Processes/DELETE | Odstraňte procesy instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Processes/Read | Získáte procesy instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/instances/Read | Získá instance webové aplikace. |
> | Akce | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Seznam synchronizace funkce aktivační událost stavu webových aplikací. |
> | Akce | Microsoft.Web/Sites/metricdefinitions/Read | Získáte webové aplikace metrika definice. |
> | Akce | Microsoft.Web/Sites/Metrics/Read | Získáte metriky webové aplikace. |
> | Akce | Microsoft.Web/Sites/metricsdefinitions/Read | Získáte definice webové aplikace metriky. |
> | Akce | Microsoft.Web/Sites/migratemysql/Action | Migraci databáze MySql webové aplikace. |
> | Akce | Microsoft.Web/Sites/migratemysql/Read | Získat webové aplikace migraci databáze MySql. |
> | Akce | Microsoft.Web/Sites/networktrace/Action | Webové aplikace, síťové trasování. |
> | Akce | Microsoft.Web/Sites/newpassword/Action | Nové_heslo webové aplikace. |
> | Akce | Microsoft.Web/Sites/operationresults/Read | Získáte výsledky operace webové aplikace. |
> | Akce | Microsoft.Web/Sites/Operations/Read | Získejte operace webové aplikace. |
> | Akce | Microsoft.Web/Sites/perfcounters/Read | Získáte čítače výkonu webové aplikace. |
> | Akce | Microsoft.Web/Sites/premieraddons/DELETE | Odstraňte rozšíření Premier webové aplikace. |
> | Akce | Microsoft.Web/Sites/premieraddons/Read | Získáte rozšíření Premier webové aplikace. |
> | Akce | Microsoft.Web/Sites/premieraddons/Write | Aktualizace doplňků Premier webové aplikace. |
> | Akce | Microsoft.Web/Sites/Processes/Read | Získáte procesy webové aplikace. |
> | Akce | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.web/sites/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro webovou aplikaci |
> | Akce | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Načte dostupné metriky pro webovou aplikaci |
> | Akce | Microsoft.Web/Sites/publiccertificates/DELETE | Odstranění webové aplikace veřejné certifikáty. |
> | Akce | Microsoft.Web/Sites/publiccertificates/Read | Získáte webové aplikace veřejné certifikáty. |
> | Akce | Microsoft.Web/Sites/publiccertificates/Write | Aktualizace webové aplikace veřejné certifikáty. |
> | Akce | Microsoft.Web/sites/publish/Action | Publikování webové aplikace |
> | Akce | Microsoft.Web/sites/publishxml/Action | Získat publikování profil xml pro webovou aplikaci |
> | Akce | Microsoft.Web/Sites/publishxml/Read | Získat publikování webové aplikace XML. |
> | Akce | Microsoft.Web/sites/Read | Umožňuje získat vlastnosti webové aplikace |
> | Akce | Microsoft.Web/Sites/recommendationhistory/Read | Zobrazit historii doporučení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Recommendations/disable/Action | Zakážete doporučení webové aplikace. |
> | Akce | Microsoft.Web/sites/recommendations/Read | Získejte seznam doporučení pro webovou aplikaci. |
> | Akce | Microsoft.Web/Sites/Recover/Action | Obnovte webové aplikace. |
> | Akce | Microsoft.Web/sites/resetSlotConfig/Action | Resetovat konfiguraci webové aplikace |
> | Akce | Microsoft.Web/Sites/resourcehealthmetadata/Read | Získáte Metadata stavu webových aplikací prostředků. |
> | Akce | Microsoft.Web/sites/restart/Action | Restartování webové aplikace |
> | Akce | Microsoft.Web/Sites/Restore/Read | Získáte obnovení webové aplikace. |
> | Akce | Microsoft.Web/Sites/Restore/Write | Obnovení webové aplikace. |
> | Akce | Microsoft.Web/Sites/restorefromdeletedwebapp/Action | Obnovení webové aplikace z odstraněné aplikace. |
> | Akce | Microsoft.Web/Sites/restoresnapshot/Action | Obnovte snímky webové aplikace. |
> | Akce | Microsoft.Web/Sites/siteextensions/DELETE | Odstraňte rozšíření webové aplikace webů. |
> | Akce | Microsoft.Web/Sites/siteextensions/Read | Získáte rozšíření webové aplikace webů. |
> | Akce | Microsoft.Web/Sites/siteextensions/Write | Aktualizujte rozšíření webové aplikace webů. |
> | Akce | Microsoft.Web/Sites/slots/analyzecustomhostname/Read | Získat webové aplikace sloty analyzovat vlastní název hostitele. |
> | Akce | Microsoft.Web/sites/slots/applySlotConfig/Action | Použít konfiguraci webové aplikace slotu z cílového slotu na aktuální pozici. |
> | Akce | Microsoft.Web/sites/slots/backup/Action | Vytvoří novou zálohu Slot webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Backup/Read | Získáte zálohování sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Backup/Write | Aktualizujte zálohování sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/backups/DELETE | Odstraňte zálohy sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/backups/list/Action | Seznam webových aplikací sloty zálohování. |
> | Akce | Microsoft.Web/sites/slots/backups/Read | Umožňuje získat vlastnosti zálohování se sloty webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/backups/Restore/Action | Obnovte zálohy sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/config/DELETE | Odstraňte konfigurační sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/config/list/Action | Seznam Slot webové aplikace citlivé nastavení zabezpečení, jako je například publikování přihlašovací údaje, nastavení aplikace a připojovacích řetězců |
> | Akce | Microsoft.Web/sites/slots/config/Read | Získat nastavení konfigurace Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/config/Write | Aktualizovat nastavení konfigurace Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/DELETE | Odstranění webové aplikace sloty nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/Read | Získáte webové aplikace sloty nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/Start/Action | Spuštění webové aplikace sloty nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/continuouswebjobs/stop/Action | Zastavení webové aplikace sloty nepřetržité webové úlohy. |
> | Akce | Microsoft.Web/sites/slots/Delete | Odstraňte existující Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Deployments/DELETE | Odstraňte nasazení sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Deployments/log/Read | Získáte webové aplikace sloty nasazení protokolu. |
> | Akce | Microsoft.Web/Sites/slots/Deployments/Read | Získá nasazení sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Deployments/Write | Aktualizace nasazení sloty webové aplikace. |
> | Akce | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Spuštění webové aplikace sloty diagnostiky analýzy. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/analyses/Read | Získáte webové aplikace sloty diagnostiky analýzy. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/aspnetcore/Read | Získání diagnostiky sloty webové aplikace pro aplikaci ASP.NET Core. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/autoheal/Read | Získáte funkce Autoheal diagnostiky webové aplikace sloty. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/Deployment/Read | Získáte nasazení diagnostiky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/Deployments/Read | Získá webové aplikace sloty diagnostiky nasazení. |
> | Akce | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Spuštění webové aplikace sloty diagnostiky detektor. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/detectors/Read | Získáte webové aplikace sloty diagnostiky detektor. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/frebanalysis/Read | Získáte webové aplikace sloty diagnostiky FREB analýzy. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/loganalyzer/Read | Získáte webové aplikace sloty diagnostiky protokolu analyzátoru. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/Read | Získání diagnostiky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/runtimeavailability/Read | Získáte webové aplikace sloty diagnostiky běhovou dostupnost. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/servicehealth/Read | Získání stavu služby webových aplikací sloty diagnostiky. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitecpuanalysis/Read | Získáte procesoru analýza diagnostiky webu webové aplikace sloty. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitecrashes/Read | Získáte webové aplikace sloty diagnostiky webu dojde k chybě. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitelatency/Read | Získáte webové aplikace sloty diagnostiky lokality latence. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/sitememoryanalysis/Read | Získáte analýza lokality paměti sloty diagnostiky webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/siterestartsettingupdate/Read | Získáte aktualizace nastavení restartování diagnostiky webové aplikace sloty lokality. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/siterestartuserinitiated/Read | Získáte webové aplikace sloty diagnostiky lokality restartování inicializované uživatelem. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/siteswap/Read | Získáte prohození lokality webové aplikace sloty diagnostiky. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/ThreadCount/Read | Získáte počet vláken webové aplikace sloty diagnostiky. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Získáte webové aplikace sloty diagnostiky Workeravailability. |
> | Akce | Microsoft.Web/Sites/slots/Diagnostics/workerprocessrecycle/Read | Získáte recyklace pracovní procesu sloty diagnostiky webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/domainownershipidentifiers/Read | Získání webové aplikace sloty domény vlastnictví identifikátorů. |
> | Akce | Microsoft.Web/Sites/slots/hostnamebindings/DELETE | Odstraňte vazby názvů hostitelů sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hostnamebindings/Read | Získáte vazby názvů hostitelů sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hostnamebindings/Write | Aktualizujte vazby názvů hostitelů sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnection/DELETE | Odstranění webové aplikace sloty hybridní připojení. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnection/Read | Získáte webové aplikace sloty hybridní připojení. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnection/Write | Aktualizace webové aplikace sloty hybridní připojení. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/DELETE | Odstranění webové aplikace sloty hybridní připojení předávací službu obory názvů. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/Write | Aktualizace webové aplikace sloty hybridní připojení předávací službu obory názvů. |
> | Akce | Microsoft.Web/Sites/slots/hybridconnectionrelays/Read | Získáte předávací službu webové aplikace sloty hybridní připojení. |
> | Akce | Microsoft.Web/Sites/slots/instances/Deployments/Read | Získá webové aplikace sloty instancí nasazení. |
> | Akce | Microsoft.Web/Sites/slots/instances/Processes/DELETE | Odstranění webové aplikace sloty instancí procesy. |
> | Akce | Microsoft.Web/Sites/slots/instances/Processes/Read | Získáte webové aplikace sloty instancí procesy. |
> | Akce | Microsoft.Web/Sites/slots/instances/Read | Získá instance sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/metricdefinitions/Read | Získáte definice metrik sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Metrics/Read | Získáte metriky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/migratemysql/Read | Získat webové aplikace sloty migraci databáze MySql. |
> | Akce | Microsoft.Web/Sites/slots/networktrace/Action | Sloty síťové trasování webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/newpassword/Action | Sloty nové_heslo webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/operationresults/Read | Získáte výsledky operace sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Operations/Read | Získáte operace pro sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/perfcounters/Read | Získáte čítače výkonu sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/phplogging/Read | Získáte Phplogging sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/premieraddons/DELETE | Odstranění webové aplikace sloty Premier rozšíření. |
> | Akce | Microsoft.Web/Sites/slots/premieraddons/Read | Získáte webové aplikace sloty Premier rozšíření. |
> | Akce | Microsoft.Web/Sites/slots/premieraddons/Write | Aktualizace webové aplikace sloty Premier doplňků. |
> | Akce | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Získá nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek. |
> | Akce | microsoft.web/sites/slots/providers/Microsoft.Insights/logDefinitions/read | Získá dostupné protokoly pro sloty webové aplikace |
> | Akce | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Načte dostupné metriky pro Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/publiccertificates/DELETE | Odstraňte certifikáty veřejného sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/publiccertificates/Read | Získání certifikátů veřejných sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/publiccertificates/Write | Vytvořit nebo aktualizovat veřejné certifikáty sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/publish/Action | Publikování Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/publishxml/Action | Získat publikování profil xml pro Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/Read | Umožňuje získat vlastnosti nasazovací slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Recover/Action | Obnovte sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/resetSlotConfig/Action | Resetovat konfiguraci slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/resourcehealthmetadata/Read | Získáte Metadata stavu Web Apps sloty prostředků. |
> | Akce | Microsoft.Web/sites/slots/restart/Action | Restartujte Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Restore/Read | Získáte obnovení sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/Restore/Write | Obnovte sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/restorefromdeletedwebapp/Action | Obnovte sloty webové aplikace z odstraněné aplikace. |
> | Akce | Microsoft.Web/Sites/slots/restoresnapshot/Action | Obnovte snímky sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/siteextensions/DELETE | Odstraňte rozšíření lokality sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/siteextensions/Read | Získáte rozšíření lokality sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/siteextensions/Write | Aktualizujte rozšíření lokality sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/slotsdiffs/Action | Získat rozdíly v konfiguraci mezi sloty a webové aplikace |
> | Akce | Microsoft.Web/sites/slots/slotsswap/Action | Prohodit sloty nasazení webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/snapshots/Read | Získáte snímky sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Delete | Odstranění nastavení konfigurace správy zdrojů Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Read | Získat nastavení konfigurace Slot webové aplikace zdrojového kódu |
> | Akce | Microsoft.Web/sites/slots/sourcecontrols/Write | Aktualizovat nastavení konfigurace správy zdrojů Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/start/Action | Spustit Slot webové aplikace |
> | Akce | Microsoft.Web/sites/slots/stop/Action | Zastavit Slot webové aplikace |
> | Akce | Microsoft.Web/Sites/slots/Sync/Action | Sloty webové aplikace synchronizace. |
> | Akce | Microsoft.Web/Sites/slots/triggeredwebjobs/DELETE | Odstraňte spouštěná WebJobs sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/triggeredwebjobs/Read | Získáte spouštěná WebJobs sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/triggeredwebjobs/Run/Action | Sloty spuštění webové aplikace aktivované webové úlohy. |
> | Akce | Microsoft.Web/Sites/slots/usages/Read | Získáte použití sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/DELETE | Odstraňte připojení virtuální sítě sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/gateways/Write | Aktualizace webové aplikace sloty virtuální síť připojení brány. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/Read | Získání připojení virtuální sítě sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/virtualnetworkconnections/Write | Aktualizujte připojení virtuální sítě sloty webové aplikace. |
> | Akce | Microsoft.Web/Sites/slots/webjobs/Read | Načtení úloh Webjob sloty webové aplikace. |
> | Akce | Microsoft.Web/sites/slots/Write | Vytvořit nový Slot webové aplikace nebo aktualizovat stávající |
> | Akce | Microsoft.Web/sites/slotsdiffs/Action | Získat rozdíly v konfiguraci mezi sloty a webové aplikace |
> | Akce | Microsoft.Web/sites/slotsswap/Action | Prohodit sloty nasazení webové aplikace |
> | Akce | Microsoft.Web/Sites/snapshots/Read | Získáte snímky webové aplikace. |
> | Akce | Microsoft.Web/sites/sourcecontrols/Delete | Odstranění nastavení konfigurace správy zdrojů webové aplikace |
> | Akce | Microsoft.Web/sites/sourcecontrols/Read | Získat nastavení konfigurace zdrojového kódu webové aplikace |
> | Akce | Microsoft.Web/sites/sourcecontrols/Write | Aktualizovat nastavení konfigurace správy zdrojů webové aplikace |
> | Akce | Microsoft.Web/sites/start/Action | Spustí webovou aplikaci |
> | Akce | Microsoft.Web/sites/stop/Action | Zastavení webové aplikace |
> | Akce | Microsoft.Web/Sites/Sync/Action | Synchronizace webové aplikace. |
> | Akce | Microsoft.Web/Sites/syncfunctiontriggers/Action | Synchronizace aktivačních událostí funkce pro webové aplikace. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Odstraňte spouštěná WebJobs webové aplikace. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/history/Read | Získáte webová aplikace aktivované webové úlohy historie. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/Read | Získáte spouštěná WebJobs webové aplikace. |
> | Akce | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Spuštění webové aplikace aktivované webové úlohy. |
> | Akce | Microsoft.Web/Sites/usages/Read | Získáte použití webové aplikace. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/DELETE | Odstraňte připojení virtuální sítě webové aplikace. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/gateways/Read | Získáte brány připojení virtuální sítě webové aplikace. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/gateways/Write | Aktualizace brány připojení virtuální sítě webové aplikace. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/Read | Získání připojení virtuální sítě webové aplikace. |
> | Akce | Microsoft.Web/Sites/virtualnetworkconnections/Write | Aktualizujte připojení virtuální sítě webové aplikace. |
> | Akce | Microsoft.Web/Sites/webjobs/Read | Načtení úloh Webjob webové aplikace. |
> | Akce | Microsoft.Web/sites/Write | Vytvořit novou webovou aplikaci nebo aktualizovat stávající |
> | Akce | Microsoft.Web/skus/Read | Získáte SKU. |
> | Akce | Microsoft.Web/sourcecontrols/Read | Získáte ovládací prvky zdroje. |
> | Akce | Microsoft.Web/sourcecontrols/Write | Aktualizace ovládacích prvků zdroje. |
> | Akce | Microsoft.Web/unregister/Action | Zrušte registraci poskytovatele prostředků Microsoft.Web pro předplatné. |
> | Akce | Microsoft.Web/Validate/Action | Ověření. |
> | Akce | Microsoft.Web/verifyhostingenvironmentvnet/Action | Ověřte, hostování prostředí virtuální sítě. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Typ akce | Operace | Popis |
> | --- | --- | --- |
> | Akce | Microsoft.WorkloadMonitor/components/read | Prostředky operace čtení |
> | Akce | Microsoft.WorkloadMonitor/healthInstances/read | Prostředky operace čtení |
> | Akce | Microsoft.WorkloadMonitor/Operations/read | Prostředky operace čtení |
> | Akce | Microsoft.WorkloadMonitor/workloads/delete | Odstraní prostředek pracovního vytížení |
> | Akce | Microsoft.WorkloadMonitor/workloads/read | Načte prostředek pracovního vytížení |
> | Akce | Microsoft.WorkloadMonitor/workloads/write | Zapíše zatížení prostředků |

## <a name="next-steps"></a>Další postup

- [Vlastní role](custom-roles.md)
- [Předdefinované role](built-in-roles.md)
