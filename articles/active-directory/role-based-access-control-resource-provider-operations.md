---
title: Operace poskytovatele Azure Resource Manager | Microsoft Docs
description: Podrobnosti o operací dostupných na poskytovateli prostředků Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operace v Azure Resource Manager poskytovatele prostředků

Tento dokument obsahuje seznam operací dostupných pro každý poskytovatel prostředků Microsoft Azure Resource Manager. To umožňuje v vlastní role poskytují podrobné řízení přístupu na základě Role (RBAC) oprávnění k prostředkům v Azure. Poznámka: Toto není kompletní seznam a operace může přidat nebo odebrat, protože každý poskytovatel je aktualizovat. Operace řetězce použijte formát `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Pro komplexní a aktuální seznam použijte `Get-AzureRmProviderOperation` (v prostředí PowerShell) nebo `az provider operation list` (v Azure CLI verze 2) k operacím seznamu zprostředkovatelů prostředků Azure.

## <a name="microsoftaad"></a>Microsoft.AAD

| Operace | Popis |
|---|---|
|/domainServices/delete|Deletes Domain Services.|
|/domainServices/read|Reads Domain Services.|
|/domainServices/write|Write Domain Services|
|/Locations/operationresults/Read|Načíst stav asynchronní operace.|
|/ Operací/čtení|Lokalizované popisný název pro operaci, protože by měl být zobrazí uživateli.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Operace | Popis |
|---|---|
|/diagnosticsettings/delete|Odstraňuje se nastavení diagnostiky|
|/diagnosticsettings/read|Načítání nastavení diagnostiky|
|/ diagnosticsettings/zápisu|Zápis nastavení diagnostiky|
|/diagnosticsettingscategories/Read|Načítání nastavení diagnostiky kategorií|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/tenants/providers/Microsoft.Insights/logDefinitions/Read|Získá dostupné protokoly pro klienty|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operace | Popis |
|---|---|
|/ configuration/akce|Konfigurace aktualizací klienta.|
|/Configuration/Read|Načte konfiguraci klienta.|
|/ configuration/zápisu|Vytvoří konfiguraci klienta.|
|/ services/akce|Aktualizace instance služby v klientovi.|
|/services/alerts/read|Přečte výstrahy pro službu.|
|/services/alerts/read|Přečte výstrahy pro službu.|
|/services/delete|Odstraní instanci služby v klientovi.|
|/Services/Read|Přečte instance služby v klientovi.|
|/Services/servicemembers/Action|Vytvoří instanci člen služby ve službě.|
|/Services/servicemembers/Alerts/Read|Přečte výstrahy pro člena služby.|
|/services/servicemembers/delete|Odstraní instanci člen služby ve službě.|
|/Services/servicemembers/Read|Přečte člen instance služby ve službě.|
|/ services/zápisu|Vytvoří instanci služby v klientovi.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operace | Popis |
|---|---|
|/Configurations/Read|Získání konfigurace|
|/ Konfigurace a zápis|Vytvoření nebo aktualizace konfigurace|
|/ generateRecommendations nebo akce|Vygeneruje doporučení|
|/generateRecommendations/Read|Získá generovat stav doporučení|
|/Operations/Read|Získá operací pro Microsoft Advisor|
|/Recommendations/Read|Přečte doporučení|
|/Recommendations/suppressions/DELETE|Odstraní potlačení|
|/Recommendations/suppressions/Read|Získá suppressions|
|/Recommendations/suppressions/Write|Vytvoření nebo aktualizace suppressions|
|/ registrace nebo akce|Zaregistruje předplatné pro Microsoft Advisor|
|/suppressions/DELETE|Odstraní potlačení|
|/suppressions/Read|Získá suppressions|
|/ suppressions/zápisu|Vytvoření nebo aktualizace suppressions|
|nebo zrušit registraci nebo akce|Zrušení registrace předplatného pro Microsoft Advisor|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Operace | Popis |
|---|---|
|/alerts/read|Získáte všechny výstrahy pro vstupní filtry.|
|/Alerts/Resolve/Action|Změnit stav výstrahy, vyřešte.|
|/alertsSummary/Read|Získejte souhrn výstrah|
|/ Operací/čtení|Přečte Zadaná operace|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operace | Popis |
|---|---|
|/locations/checkNameAvailability/action|Kontroluje, zda zadaný Analysis Server je platný název a ne v používání.|
|/Locations/operationresults/Read|Načte informace o zadanou operaci výsledku.|
|/Locations/operationstatuses/Read|Načte informace o stavu Zadaná operace.|
|/Operations/Read|Načte informace o operací|
|/ registrace nebo akce|Registruje zprostředkovatele prostředků služby Analysis Services.|
|/Servers/DELETE|Odstraní Analysis serveru.|
|/servers/listGatewayStatus/action|Zobrazí seznam stav brány přidružené k serveru.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro Analysis serveru|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro Analysis serveru|
|/Servers/providers/Microsoft.Insights/logDefinitions/Read|Získá dostupné protokoly pro servery|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro Analysis serveru|
|/Servers/Read|Načte informace zadaný Analysis serveru.|
|/Servers/Resume/Action|Obnoví Analysis serveru.|
|/Servers/skus/Read|Načíst dostupné informace o SKU pro server|
|/Servers/suspend/Action|Pozastaví Analysis serveru.|
|/ servery a zápis|Vytvoří nebo aktualizuje zadaný Analysis serveru.|
|/skus/Read|Načte informace o SKU|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operace | Popis |
|---|---|
|/checkNameAvailability/read|Ověří, zda za předpokladu, že název služby je k dispozici|
|/Operations/Read|Číst všechny operace rozhraní API k dispozici pro prostředek Microsoft.ApiManagement|
|/ registrace nebo akce|Registrace předplatného pro poskytovatele prostředků Microsoft.ApiManagement|
|/Reports/Read|Získáte sestavy agregaci časová období, geografické oblasti, vývojáři, produktů, rozhraní API, operace, předplatné a byRequest.|
|/service/apis/delete|Odeberte existující rozhraní API|
|/service/apis/diagnostics/delete|Odeberte existující Diagnostika|
|/Service/APIs/Diagnostics/loggers/DELETE|Odeberte mapování protokolovacího nástroje se nastavení diagnostiky|
|/Service/APIs/Diagnostics/loggers/Read|Získání seznamu existující diagnostiky protokolovacích nástrojů|
|/Service/APIs/Diagnostics/loggers/Write|Mapování protokolovacího nástroje na nastavení diagnostiky|
|/service/apis/diagnostics/read|Získání seznamu diagnostiky nebo získáte podrobnosti diagnostiky|
|/service/apis/diagnostics/write|Přidat nové diagnostiky nebo aktualizovat existující diagnostické informace|
|/service/apis/operations/delete|Odeberte existující operace rozhraní API|
|/Service/APIs/Operations/Policies/DELETE|Odebrat konfiguraci zásad ze zásad operace rozhraní API|
|/Service/APIs/Operations/Policies/Read|Získat zásady pro operace rozhraní API nebo podrobnosti o konfiguraci zásad Get pro operace rozhraní API|
|/Service/APIs/Operations/Policies/Write|Nastavit podrobnosti konfigurace zásady pro operace rozhraní API|
|/Service/APIs/Operations/Policy/DELETE|Odebrat konfiguraci zásad z operace|
|/Service/APIs/Operations/Policy/Read|Získat podrobnosti o konfiguraci zásad pro operaci|
|/Service/APIs/Operations/Policy/Write|Nastavení zásad konfigurace podrobnosti operace|
|/Service/APIs/Operations/Read|Získání seznamu existující operace rozhraní API nebo získat podrobnosti operace rozhraní API|
|/Service/APIs/Operations/Tags/DELETE|Odstraňte přidružení stávající značky s existující operace|
|/Service/APIs/Operations/Tags/Read|Získat značky přidružené podrobnosti operace nebo získat značky|
|/Service/APIs/Operations/Tags/Write|Přidružení stávající značky k existující operace|
|/Service/APIs/Operations/Write|Operaci nového rozhraní API umožňuje vytvořit nebo aktualizovat existující operace rozhraní API|
|/service/apis/operationsByTags/read|Získejte seznam přidružení operaci nebo značky|
|/service/apis/policies/delete|Odebrat konfiguraci zásad ze zásad rozhraní API|
|/Service/APIs/Policies/Read|Získat zásady rozhraní API nebo Get zásad podrobnosti o konfiguraci pro rozhraní API|
|/Service/APIs/Policies/Write|Podrobnosti o konfiguraci zásad nastavení pro rozhraní API|
|/Service/APIs/Policy/DELETE|Odebrat konfiguraci zásad z rozhraní API|
|/Service/APIs/Policy/Read|Získat podrobnosti o konfiguraci zásad pro rozhraní API|
|/Service/APIs/Policy/Write|Podrobnosti o konfiguraci zásad nastavení pro rozhraní API|
|/Service/APIs/Products/Read|Získat všechny produkty, které je součástí rozhraní API|
|/Service/APIs/Read|Získání seznamu všech registrované rozhraní API nebo Get podrobné informace o rozhraní API|
|/service/apis/releases/delete|Odebere všechny verze rozhraní API nebo odebrat rozhraní API verze|
|/Service/APIs/releases/Read|Získání verze podrobnosti rozhraní API nebo Get reelase rozhraní API|
|/Service/APIs/releases/Write|Vytvořit novou verzi rozhraní API nebo aktualizovat existující verzi rozhraní API|
|/service/apis/revisions/delete|Odebere všechny revize rozhraní API|
|/Service/APIs/revisions/Read|Získat revize patřící do rozhraní API|
|/service/apis/schemas/delete|Odebere existující schéma|
|/service/apis/schemas/document/read|Získat dokument popisující schématu|
|/service/apis/schemas/document/write|Aktualizace dokumentace popisující schématu|
|/service/apis/schemas/read|Získá všechny schémata pro dané rozhraní API nebo získá schémata používá rozhraní API|
|/service/apis/schemas/write|Nastaví schémata používá rozhraní API|
|/service/apis/tagDescriptions/delete|Odebrání značky popis rozhraní API|
|/Service/APIs/tagDescriptions/Read|Získání značek popisů v rozsahu tohoto rozhraní API nebo získat značky popis v rozsahu tohoto rozhraní API|
|/Service/APIs/tagDescriptions/Write|Vytvořit nebo změnit značky popis v oboru rozhraní API|
|/Service/APIs/Tags/DELETE|Odeberte stávající přidružení rozhraní API nebo značky|
|/Service/APIs/Tags/Read|Získání všech rozhraní API nebo značky přidružení pro rozhraní API nebo Get podrobnosti o rozhraní API nebo značky přidružení|
|/Service/APIs/Tags/Write|Přidání nového rozhraní API nebo značky přidružení|
|/Service/APIs/Write|Nové rozhraní API umožňuje vytvořit nebo aktualizovat existující podrobnosti o rozhraní API|
|/Service/apisByTags/Read|Získejte seznam přidružení rozhraní API nebo značky|
|/service/api-version-sets/delete|Odeberte existující VersionSet|
|/service/api-version-sets/read|Získání seznamu verze skupiny entit nebo získá podrobnosti VersionSet|
|/service/api-version-sets/versions/read|Získání seznamu verze entit|
|/service/api-version-sets/write|Vytvořit nový VersionSet nebo aktualizovat existující VersionSet podrobnosti|
|/service/applynetworkconfigurationupdates/action|Aktualizuje Microsoft.ApiManagement prostředky spuštěna ve virtuální síti a vyberte aktualizované nastavení sítě.|
|/service/authorizationServers/delete|Odeberte existující serveru ověřování|
|/service/authorizationServers/read|Získat seznam serverů autorizace nebo získat podrobnosti o serveru ověřování|
|/service/authorizationServers/write|Vytvořit nový server autorizace nebo podrobné informace o aktualizaci existujícího serveru autorizace|
|/service/backends/delete|Odeberte existující back-end|
|/service/backends/read|Získání seznamu back-EndY nebo získat podrobnosti o back-end|
|/service/backends/reconnect/action|Vytvořit žádost o opětovné připojení|
|/service/backends/write|Přidat nový back-end nebo aktualizovat existující podrobnosti back-end|
|/service/backup/action|Zálohování službu správy rozhraní API a zadaného kontejneru. v uživatel zadaný účet úložiště|
|/service/certificates/delete|Odebrat existující certifikát|
|/Service/Certificates/Read|Získání seznamu certifikátů nebo získat podrobnosti o certifikát|
|/Service/Certificates/Write|Přidat nový certifikát|
|/Service/DELETE|Odstranění instance služby API Management|
|/service/diagnostics/delete|Odeberte existující Diagnostika|
|/Service/Diagnostics/loggers/DELETE|Odeberte mapování protokolovacího nástroje se nastavení diagnostiky|
|/Service/Diagnostics/loggers/Read|Získání seznamu existující diagnostiky protokolovacích nástrojů|
|/Service/Diagnostics/loggers/Write|Mapování protokolovacího nástroje na nastavení diagnostiky|
|/service/diagnostics/read|Získání seznamu diagnostiky nebo získáte podrobnosti diagnostiky|
|/Service/Diagnostics/Write|Přidat nové diagnostiky nebo aktualizovat existující diagnostické informace|
|/service/getssotoken/action|Získá token jednotného přihlašování, který lze použít k přihlášení na portál starší verze služby API Management jako správce|
|/service/groups/delete|Odeberte existující skupinu|
|/Service/groups/Read|Získání seznamu skupin nebo získá podrobnosti skupiny|
|/Service/groups/Users/DELETE|Odebrat existující uživatele z existující skupiny|
|/Service/groups/Users/Read|Získání seznamu skupin uživatelů|
|/Service/groups/Users/Write|Přidat stávajícího uživatele do stávající skupiny|
|/Service/groups/Write|Vytvořit novou skupinu nebo aktualizovat existující skupiny podrobnosti|
|/service/identityProviders/delete|Odeberte existující zprostředkovatele Identity|
|/service/identityProviders/read|Získání seznamu zprostředkovatelů Identity nebo získáte podrobnosti zprostředkovatele Identity|
|/service/identityProviders/write|Vytvořit nový zprostředkovatel Identity nebo aktualizace podrobnosti existujícího poskytovatele Identity|
|/service/locations/networkstatus/read|Získá stav přístupu k síti prostředků, na kterých služba závisí na v umístění.|
|/Service/loggers/DELETE|Odeberte existující protokolovacího nástroje.|
|/Service/loggers/Read|Získání seznamu protokolovačů nebo získat podrobnosti o protokolovacího nástroje|
|/Service/loggers/Write|Přidat nové protokoly nebo aktualizovat existující podrobnosti protokolovacího nástroje|
|/Service/managedeployments/Action|Změnit SKU nebo jednotky, přidat nebo odebrat místní nasazení služby API Management|
|/service/networkstatus/read|Získá stav přístupu k síti prostředků, na kterých služba závisí na.|
|/service/notifications/action|Odešle oznámení pro zadaného uživatele|
|/Service/Notifications/Read|Získá všechny API Management vydavatele oznámení nebo získat API Management vydavatele podrobnosti oznámení|
|/service/notifications/recipientEmails/delete|Odebere existující přidružená oznámení e-mailu|
|/service/notifications/recipientEmails/read|Získat příjemců e-mailu, které jsou přidružené k rozhraní API správy vydavatele oznámení|
|/service/notifications/recipientEmails/write|Vytvořit nový příjemce e-mailové oznámení.|
|/service/notifications/recipientUsers/delete|Odebere uživatele přidružené k příjemce oznámení|
|/service/notifications/recipientUsers/read|Získat příjemce uživatelé přidružení oznámení.|
|/service/notifications/recipientUsers/write|Přidejte uživatele k příjemce oznámení|
|/Service/Notifications/Write|Vytvoření nebo aktualizace API Management vydavatele oznámení|
|/service/openidConnectProviders/delete|Odebrat existující OpenID Connect zprostředkovatele|
|/Service/openidConnectProviders/Read|Získat seznam poskytovatelů OpenID Connect nebo získáte podrobnosti OpenID Connect zprostředkovatele|
|/service/openidConnectProviders/write|Vytvořit nové podrobnosti OpenID Connect zprostředkovatele nebo aktualizaci existujícího poskytovatele připojení OpenID|
|/Service/operationresults/Read|Získá aktuální stav operace probíhající dlouhou dobu|
|/Service/Policies/DELETE|Odebrat konfiguraci zásad ze zásad klienta|
|/Service/Policies/Read|Získání zásad klienta nebo Get zásad podrobnosti o konfiguraci pro klienta|
|/Service/Policies/Write|Nastavit podrobnosti o konfiguraci zásad pro klienta|
|/Service/policySnippets/Read|Získání všech zásad fragmenty kódu|
|/service/portalsettings/read|Získat přihlašovací nastavení pro portál nebo Get přihlašovací nastavení pro tento portál nebo získat nastavení delegování pro portál|
|/Service/portalsettings/Write|Aktualizovat nastavení zaregistrovat nebo nastavení aktualizace zaregistrovat nebo přihlásit k aktualizaci nastavení nebo nastavení aktualizace přihlásit nebo nastavení aktualizovat delegování nebo aktualizovat delegování nastavení|
|/Service/Products/APIs/DELETE|Odeberte existující rozhraní API z existující produktu|
|/Service/Products/APIs/Read|Získání seznamu rozhraní API, které jsou přidány do existujících produktu|
|/service/products/apis/write|Přidání existujícího rozhraní API pro stávající produkt|
|/Service/Products/DELETE|Odeberte existující produktu|
|/service/products/groups/delete|Odstraňte přidružení existující produkt existující skupinu pro vývojáře|
|/service/products/groups/read|Získání seznamu skupin vývojáře spojené s produktem|
|/Service/Products/groups/Write|Existující produkt přidružit existující skupinu pro vývojáře|
|/service/products/policies/delete|Odebrat konfiguraci zásad z produktu zásady|
|/Service/Products/Policies/Read|Získat zásady produktu nebo Get zásad podrobnosti o konfiguraci produktu|
|/service/products/policies/write|Nastavení zásad podrobnosti konfigurace produktu|
|/Service/Products/Policy/DELETE|Odebrat konfiguraci zásad z existující produktu|
|/Service/Products/Policy/Read|Získání konfigurace zásad existující produktu|
|/Service/Products/Policy/Write|Nastavení zásad konfigurace pro stávající produkt|
|/Service/Products/Read|Získat seznam produktů nebo získat podrobnosti o produktu|
|/Service/Products/Subscriptions/Read|Získání seznamu předplatných produktu|
|/Service/Products/Tags/DELETE|Odstraňte přidružení stávající značky s existující produktu|
|/Service/Products/Tags/Read|Získání značek, které jsou přidružené k produktu nebo značka, získat podrobnosti|
|/Service/Products/Tags/Write|Existující produkt přidružit existující značky|
|/Service/Products/Write|Vytvořit nového produktu nebo aktualizovat existující podrobnosti o produktu|
|/service/properties/delete|Odebere existující vlastnost|
|/service/properties/read|Získá seznam všech vlastností nebo získá podrobnosti o zadanou vlastnost|
|/Service/Properties/Write|Vytvoří novou vlastnost nebo aktualizuje hodnotu pro zadanou vlastnost|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro služba API Management|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro služba API Management|
|/service/providers/Microsoft.Insights/logDefinitions/read|Získá dostupné protokoly služby API Management|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro služba API Management|
|/service/quotas/periods/read|Získat hodnotu čítače kvóty pro období|
|/Service/Quotas/Periods/Write|Nastavit aktuální hodnota čítače kvóty|
|/service/quotas/read|Získá hodnoty pro kvótu|
|/service/quotas/write|Nastavit aktuální hodnota čítače kvóty|
|/service/read|Číst metadata pro instanci služby API Management|
|/service/reports/read|Získáte sestavy agregovat v časových období nebo sestavě Get agregovat v zeměpisné oblasti nebo sestava Get agregovat vývojáři. nebo získat sestavy agregaci produkty. nebo získat sestavy agregovat v rozhraní API nebo Get sestavu agregaci operace nebo sestavě Get agregovat tímto odběrem. nebo získání žádostí o data pro vytváření sestav|
|/service/restore/action|Obnovení ze zadaného kontejneru v uživatel zadaný účet úložiště služby API Management|
|/Service/Subscriptions/DELETE|Odstraňte odběr. Tato operace slouží k odstranění předplatného|
|/Service/Subscriptions/Read|Získat seznam předplatných produktů nebo získat podrobnosti o odběru produktu|
|/service/subscriptions/regeneratePrimaryKey/action|Znova vygenerovat primární klíč předplatného|
|/service/subscriptions/regenerateSecondaryKey/action|Znova vygenerovat sekundární klíč předplatného|
|/Service/Subscriptions/Write|Přihlášení k odběru stávajícího uživatele k existujícímu produktu nebo aktualizovat existující podrobnosti o předplatném. Tato operace slouží k obnovení odběru|
|/Service/tagResources/Read|Získání seznamu značek přidružených zdrojů.|
|/Service/Tags/DELETE|Odebrat existující značku|
|/Service/Tags/Read|Získání seznamu značek nebo získat podrobnosti značky|
|/Service/Tags/Write|Přidat novou značku nebo aktualizovat existující podrobnosti značky|
|/service/templates/delete|Obnovit výchozí šablona služby API Management e-mailu|
|/service/templates/read|Získá všechny e-mailových šablon nebo získá rozhraní API správy e-mailové šablony podrobnosti|
|/service/templates/write|Vytvořit nebo aktualizovat rozhraní API správy e-mailu nebo šablona e-mailu aktualizace API Management|
|/Service/tenant/DELETE|Odebrat konfiguraci zásad pro klienta|
|/Service/tenant/Deploy/Action|Spustí úlohu nasazení provést změny z větve zadaný git konfigurace v databázi.|
|/service/tenant/operationResults/read|Získejte seznam výsledky operace nebo získat výsledek určité operace|
|/Service/tenant/Read|Získat konfiguraci zásad pro klienta nebo klienta Get podrobné informace o přístupu|
|/service/tenant/regeneratePrimaryKey/action|Znova vygenerovat primární přístupový klíč|
|/service/tenant/regenerateSecondaryKey/action|Znova vygenerovat sekundární přístupový klíč|
|/Service/tenant/Save/Action|Vytvoří potvrzení s snímku konfigurace pro zadaný větve v úložišti|
|/service/tenant/syncState/read|Získat stav poslední synchronizace git|
|/Service/tenant/Validate/Action|Ověří změny z větve zadaný git|
|/service/tenant/write|Nastavení zásad konfigurace pro klienta nebo podrobné informace o aktualizaci klienta přístup informace|
|/service/updatecertificate/action|Nahrajte certifikát SSL pro službu správy rozhraní API|
|/service/updatehostname/action|Instalační program, aktualizovat nebo odebrat vlastní názvy domén pro služby API Management|
|/Service/Users/Action|Registrace nového uživatele|
|/Service/Users/Applications/Attachments/DELETE|Odebere přílohu|
|/Service/Users/Applications/Attachments/Read|Získá aplikaci přílohy nebo získá přílohy|
|/service/users/applications/attachments/write|Přidat připojení k aplikaci|
|/Service/Users/Applications/DELETE|Odebere existující aplikace|
|/Service/Users/Applications/Read|Získání seznamu všechny uživatelské aplikace nebo podrobnosti o aplikace získá API Management|
|/Service/Users/Applications/Write|Zaregistruje aplikaci API Management nebo aktualizace podrobností o aplikaci|
|/Service/Users/DELETE|Odebrat uživatelský účet|
|/Service/Users/generateSsoUrl/Action|Generování adresy URL jednotné přihlašování. Adresa URL slouží pro přístup k portálu pro správu|
|/Service/Users/groups/Read|Získání seznamu skupin uživatelů|
|/Service/Users/Keys/Read|Získat seznam klíčů uživatele|
|/Service/Users/Read|Získat seznam registrovaní uživatelé nebo získat podrobnosti o účtu uživatele|
|/Service/Users/Subscriptions/Read|Získat seznam uživatelů odběrů|
|/Service/Users/token/Action|Získání tokenu tokenu přístupu pro uživatele|
|/Service/Users/Write|Zaregistrovat nového uživatele nebo účet podrobnosti aktualizace stávajícího uživatele|
|/ service/zápisu|Vytvoření nové instance služby API Management|
|nebo zrušit registraci nebo akce|Zrušení registrace předplatného pro poskytovatele prostředků Microsoft.ApiManagement|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operace | Popis |
|---|---|
|/checkAccess/action|Zkontroluje, jestli má volající autorizaci provést určitou akci.|
|/classicAdministrators/DELETE|Umožňuje odebrat správce z předplatného.|
|/classicAdministrators/read|Umožňuje načíst správce předplatného.|
|/ classicAdministrators/zápisu|Umožňuje přidat nebo změnit správce předplatného.|
|/ elevateAccess nebo akce|Udělí volajícímu přístup Správce uživatelských přístupů v oboru tenanta.|
|/Locks/DELETE|Umožňuje odstranit zámky v zadaném oboru.|
|/Locks/Read|Umožňuje načíst zámky v zadaném oboru.|
|/ zámky a zápis|Umožňuje přidat zámky v zadaném oboru.|
|/Permissions/Read|Umožňuje vypsat seznam všech oprávnění, která má volající v daném oboru.|
|/policyAssignments/DELETE|Odstranění přiřazení zásady v zadaném oboru|
|/policyAssignments/Read|Získání informací o přiřazení zásady|
|/ policyAssignments/zápisu|Vytvoření přiřazení zásady v zadaném oboru|
|/policyDefinitions/DELETE|Odstranění definice zásady|
|/policyDefinitions/Read|Získání informací o definici zásady|
|/ policyDefinitions/zápisu|Vytvoření vlastní definice zásady|
|/policySetDefinitions/DELETE|Odstranění definice sady zásad|
|/policySetDefinitions/Read|Získá informace o definici sady zásad.|
|/ policySetDefinitions/zápisu|Vytvoření vlastní definice sady zásad|
|/providerOperations/Read|Získejte operace pro všechny poskytovatele prostředků, které je možné použít v definicích rolí.|
|/roleAssignments/DELETE|Umožňuje odstranit přiřazení role v zadaném oboru.|
|/roleAssignments/Read|Umožňuje načíst informace o přiřazení role.|
|/ roleAssignments/zápisu|Umožňuje vytvořit přiřazení role v zadaném oboru.|
|/roleDefinitions/DELETE|Odstraňte zadané definice vlastních rolí.|
|/roleDefinitions/Read|Umožňuje načíst informace o definici role.|
|/ roleDefinitions/zápisu|Odstraňte nebo aktualizujte definice vlastních rolí s určenými oprávněními a přiřaditelnými obory.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operace | Popis |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Přečtěte si informace o registraci Azure Automation DSC|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Zapíše požadavek na opětovné vygenerování klíčů Azure Automation DSC.|
|/automationAccounts/Certificates/DELETE|Odstraní prostředek certifikátu automatizace Azure|
|/automationAccounts/Certificates/Read|Načte prostředek certifikátu automatizace Azure.|
|/automationAccounts/Certificates/Write|Vytvoří nebo aktualizuje prostředek certifikátu automatizace Azure|
|/automationAccounts/compilationjobs/read|Přečte kompilace Azure Automation DSC|
|/automationAccounts/compilationjobs/write|Zapíše kompilace Azure Automation DSC|
|/automationAccounts/configurations/delete|Odstraní obsah DSC Azure Automation.|
|/automationAccounts/configurations/getCount/action|Přečte počet obsah DSC Azure Automation.|
|/automationAccounts/configurations/read|Získá obsah DSC Azure Automation.|
|/automationAccounts/configurations/write|Zapíše obsah DSC Azure Automation.|
|/automationAccounts/connections/delete|Odstraní prostředek připojení Azure Automation.|
|/automationAccounts/connections/read|Načte prostředek připojení Azure Automation.|
|/automationAccounts/connections/write|Vytvoří nebo aktualizuje prostředek připojení Azure Automation.|
|/automationAccounts/connectionTypes/delete|Odstraní prostředek typu připojení automatizace Azure.|
|/automationAccounts/connectionTypes/read|Získá prostředek typu připojení automatizace Azure.|
|/automationAccounts/connectionTypes/write|Vytvoří prostředek typu připojení automatizace Azure.|
|/automationAccounts/credentials/DELETE|Odstraní prostředek přihlašovacích údajů Azure Automation.|
|/automationAccounts/credentials/read|Získá prostředek přihlašovacích údajů Azure Automation.|
|/automationAccounts/credentials/Write|Vytvoří nebo aktualizuje prostředek přihlašovacích údajů Azure Automation.|
|/automationAccounts/delete|Odstraní účet Azure Automation|
|/automationAccounts/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/automationAccounts/diagnosticSettings/write|Nastaví nastavení diagnostiky pro prostředek|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Odstraní prostředky Hybrid Runbook Worker|
|/automationAccounts/hybridRunbookWorkerGroups/read|Čte prostředky Hybrid Runbook Worker|
|/automationAccounts/jobs/output/action|Načte výstup úlohy|
|/automationAccounts/jobs/output/action|Načte výstup úlohy|
|/automationAccounts/Jobs/Read|Získá úlohu automatizace Azure|
|/automationAccounts/Jobs/Read|Získá úlohu automatizace Azure|
|/automationAccounts/Jobs/Resume/Action|Obnoví úlohu automatizace Azure|
|/automationAccounts/Jobs/Resume/Action|Obnoví úlohu automatizace Azure|
|/automationAccounts/jobs/runbookContent/action|Získá obsah runbooku automatizace Azure při provádění úlohy|
|/automationAccounts/jobs/runbookContent/action|Získá obsah runbooku automatizace Azure při provádění úlohy|
|/automationAccounts/jobs/stop/action|Zastaví úlohu Azure Automation|
|/automationAccounts/jobs/stop/action|Zastaví úlohu Azure Automation|
|/automationAccounts/jobs/streams/read|Získá stream úloh Azure Automation|
|/automationAccounts/jobs/streams/read|Získá stream úloh Azure Automation|
|/automationAccounts/Jobs/suspend/Action|Pozastaví úlohu Azure Automation|
|/automationAccounts/Jobs/suspend/Action|Pozastaví úlohu Azure Automation|
|/automationAccounts/jobs/write|Vytvoří úlohu Azure Automation|
|/automationAccounts/jobs/write|Vytvoří úlohu Azure Automation|
|/automationAccounts/jobSchedules/DELETE|Odstraní plán úloh Azure Automation.|
|/automationAccounts/jobSchedules/read|Získá plán úloh Azure Automation.|
|/automationAccounts/jobSchedules/Write|Vytvoří plán úloh Azure Automation.|
|/automationAccounts/linkedWorkspace/read|Získá prostoru propojí s účtem automation.|
|/automationAccounts/logDefinitions/Read|Získá dostupné protokoly pro účet služby automation|
|/automationAccounts/Modules/Activities/Read|Získá aktivity automatizace Azure|
|/automationAccounts/modules/delete|Odstraní modul Azure Automation|
|/automationAccounts/modules/read|Získá modul automatizace Azure|
|/automationAccounts/Modules/Write|Vytvoří nebo aktualizuje modul Azure Automation|
|/automationAccounts/nodeConfigurations/delete|Odstraní konfigurace uzlu DSC Azure Automation.|
|/automationAccounts/nodeConfigurations/Read|Přečte konfigurace uzlu DSC Azure Automation.|
|/automationAccounts/nodeConfigurations/readContent/action|Čte obsah konfigurace uzlu DSC Azure Automation.|
|/automationAccounts/nodeConfigurations/write|Zapíše konfigurace uzlu DSC Azure Automation.|
|/automationAccounts/nodes/delete|Odstraní uzlů Azure Automation DSC.|
|/automationAccounts/nodes/read|Přečte uzlů Azure Automation DSC.|
|/automationAccounts/nodes/reports/read|Přečte contentss sestavy Azure Automation DSC.|
|/automationAccounts/nodes/reports/read|Přečte sestavy Azure Automation DSC.|
|/automationAccounts/objectDataTypes/fields/read|Získá TypeFields služby Azure Automation|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Získá definice metrik automatizace|
|/automationAccounts/read|Získá účet automatizace Azure|
|/automationAccounts/runbooks/delete|Odstraní runbook služby automatizace Azure|
|/automationAccounts/runbooks/draft/publish/action|Publikuje koncept runbooku služby Azure Automation.|
|/automationAccounts/runbooks/draft/read|Získá koncept runbooku služby Azure Automation.|
|/automationAccounts/runbooks/draft/readContent/action|Získá obsah konceptu runbooku služby Azure Automation.|
|/automationAccounts/runbooks/draft/testJob/read|Získá testovací úlohu konceptu runbooku automatizace Azure.|
|/automationAccounts/runbooks/draft/testJob/resume/action|Obnoví testovací úlohu konceptu runbooku automatizace Azure.|
|/automationAccounts/runbooks/draft/testJob/stop/action|Zastaví testovací úlohu konceptu runbooku automatizace Azure.|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Pozastaví testovací úlohu konceptu runbooku automatizace Azure.|
|/automationAccounts/runbooks/draft/testJob/write|Vytvoří testovací úlohu konceptu runbooku automatizace Azure.|
|/automationAccounts/runbooks/draft/undoEdit/action|Vrátit zpět úpravy konceptu runbooku služby Azure Automation.|
|/automationAccounts/runbooks/draft/writeContent/action|Vytvoří obsah konceptu runbooku služby Azure Automation.|
|/automationAccounts/runbooks/read|Načte runbook služby automatizace Azure|
|/automationAccounts/runbooks/readContent/action|Získá obsah runbooku automatizace Azure|
|/automationAccounts/runbooks/write|Vytvoří nebo aktualizuje runbook služby automatizace Azure|
|/automationAccounts/Schedules/DELETE|Odstraní prostředek plánování Azure Automation.|
|/automationAccounts/schedules/read|Získá prostředek plánování Azure Automation.|
|/automationAccounts/Schedules/Write|Vytvoří nebo aktualizuje prostředek plánování Azure Automation.|
|/automationAccounts/statistics/read|Získá statistiku objektů služby Azure Automation|
|/automationAccounts/usages/read|Získá využití služby Azure Automation|
|/automationAccounts/Variables/DELETE|Odstraní variabilní prostředek Azure Automation.|
|/automationAccounts/Variables/Read|Přečte variabilní prostředek Azure Automation.|
|/automationAccounts/Variables/Write|Vytvoří nebo aktualizuje variabilní prostředek Azure Automation.|
|/automationAccounts/watchers/streams/read|Získá stream úloh Azure Automation sledovacích procesů|
|/automationAccounts/webhooks/delete|Odstraní webhook automatizace Azure |
|/automationAccounts/webhooks/generateUri/action|Vygeneruje URI pro webhook automatizace Azure|
|/automationAccounts/webhooks/read|Přečte webhook automatizace Azure|
|/automationAccounts/webhooks/write|Vytvoří nebo aktualizuje webhook automatizace Azure|
|/automationAccounts/write|Vytvoří nebo aktualizuje účet Azure Automation.|
|/automationAccounts/write|Vytvoří nebo aktualizuje účet Azure Automation.|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Operace | Popis |
|---|---|
|/b2cDirectories/DELETE|Odstranit prostředek B2C Directory|
|/b2cDirectories/Read|Zobrazit prostředek B2C Directory|
|/ b2cDirectories/zápisu|Vytvořit nebo aktualizovat prostředek B2C Directory|
|/Operations/Read|Přečte všechny operace rozhraní API dostupné pro poskytovatele prostředků Microsoft.AzureActiveDirectory.|
|/ registrace nebo akce|Zaregistruje předplatné poskytovatele prostředků Microsoft.AzureActiveDirectory.|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Operace | Popis |
|---|---|
|/ Operací/čtení|Získá vlastnosti operace poskytovatele prostředků|
|/ registrace nebo akce|Zaregistruje předplatné se poskytovatel prostředků Microsoft.AzureStack|
|/registrations/customerSubscriptions/delete|Odstraní předplatné zákazníka Azure zásobníku|
|/registrations/customerSubscriptions/read|Získá vlastnosti předplatné Azure zásobníku zákazníka|
|/Registrations/customerSubscriptions/Write|Vytvoří nebo aktualizuje předplatné Azure zásobníku zákazníka|
|/Registrations/DELETE|Odstraní registraci Azure zásobníku|
|/registrations/getActivationKey/action|Získá nejnovější aktivační klíč Azure zásobníku|
|/registrations/products/listDetails/action|Načte Rozšířené podrobnosti o produkt Azure Marketplace zásobníku|
|/Registrations/Products/Read|Získá vlastnosti o produkt Azure Marketplace zásobníku|
|/Registrations/Read|Získá vlastnosti zápisu Azure zásobníku|
|/ registrace a zápis|Vytvoří nebo aktualizuje registraci Azure zásobníku|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operace | Popis |
|---|---|
|/batchAccounts/Applications/DELETE|Odstraní aplikaci|
|/batchAccounts/Applications/Read|Seznam aplikací, nebo načte vlastnosti aplikace|
|/batchAccounts/Applications/versions/Activate/Action|Aktivuje balíček aplikace|
|/batchAccounts/Applications/versions/DELETE|Odstraní balíček aplikace|
|/batchAccounts/Applications/versions/Read|Získá vlastnosti balíčku aplikace|
|/batchAccounts/Applications/versions/Write|Vytvoří nový balíček aplikace nebo aktualizuje existující balíček aplikace|
|/batchAccounts/Applications/Write|Vytvoří novou aplikaci nebo aktualizuje existující aplikace|
|/batchAccounts/certificateOperationResults/read|Získá výsledky dlouhotrvající operace certifikát na účtu Batch|
|/batchAccounts/certificates/cancelDelete/action|Zruší se nezdařilo vytvoření certifikátu na účtu Batch|
|/batchAccounts/Certificates/DELETE|Odstraní certifikát z účtu Batch|
|/batchAccounts/Certificates/Read|Zobrazí certifikáty na účtu Batch nebo načte vlastnosti certifikátu|
|/batchAccounts/Certificates/Write|Vytvoří nový certifikát účtu Batch nebo aktualizuje existující certifikát|
|/batchAccounts/DELETE|Odstraní účet Batch|
|/batchAccounts/listkeys/action|Seznamy přístupové klíče pro účet Batch|
|/batchAccounts/operationResults/read|Získá výsledky dlouhotrvající operace účtu Batch|
|/batchAccounts/poolOperationResults/Read|Získá výsledky dlouhotrvající operace fondu na účtu Batch|
|/batchAccounts/pools/DELETE|Odstraní fond z účtu Batch|
|/batchAccounts/pools/disableAutoscale/Action|Zakáže Automatické škálování pro účet fondu služby Batch|
|/batchAccounts/pools/Read|Zobrazí seznam fondů na účtu Batch nebo načte vlastnosti fondu|
|/batchAccounts/pools/stopResize/action|Resize – zastaví na probíhající operace v účtu fondu služby Batch|
|/batchAccounts/pools/upgradeOs/Action|Upgraduje operační systém fondu účtu Batch|
|/batchAccounts/pools/Write|Vytvoří nový fond na účtu Batch nebo aktualizuje existující fond|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/Write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/Read|Získá dostupné protokoly pro službu Batch|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro službu Batch|
|/batchAccounts/Read|Zobrazí seznam účtů Batch nebo načte vlastnosti účtu Batch|
|/batchAccounts/regeneratekeys/Action|Obnoví přístupové klíče pro účet Batch|
|/batchAccounts/syncAutoStorageKeys/action|Synchronizuje přístupové klíče pro účet úložiště automaticky nakonfigurován pro účet Batch|
|/ batchAccounts/zápisu|Vytvoří nový účet Batch nebo aktualizuje existující účet Batch|
|/locations/checkNameAvailability/action|Ověří, že název účtu není platný a není používán.|
|/locations/quotas/read|Získá Batch kvóty předplatného zadané v zadané oblasti Azure|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků Batch a povolí vytvoření účtů Batch|
|nebo zrušit registraci nebo akce|Zrušení registrace předplatného pro poskytovatele prostředků Batch brání vytváření účtů Batch|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Operace | Popis |
|---|---|
|/Clusters/DELETE|Odstraní cluster Batch AI|
|/Clusters/Read|Uvádí Batch AI clustery nebo načte vlastnosti clusteru Batch AI|
|/clusters/remoteLoginInformation/action|Uvádí vzdálené přihlašovací informace pro cluster s podporou Batch AI|
|/ clustery a zápis|Vytvoří nový cluster Batch AI nebo aktualizuje existující cluster Batch AI|
|/fileservers/delete|Odstraní u souborového serveru Batch AI|
|/fileservers/Read|Uvádí Batch AI fileservers nebo získá vlastnosti u souborového serveru Batch AI|
|/fileservers/Resume/Action|Obnoví u souborového serveru Batch AI|
|/fileservers/suspend/Action|Pozastaví u souborového serveru Batch AI|
|/ fileservers/zápisu|Vytvoří nový souborovém serveru Batch AI nebo aktualizuje souborovém serveru existující Batch AI|
|/Jobs/DELETE|Odstraní úlohu Batch AI|
|/Jobs/Read|Uvádí úlohy Batch AI nebo načte vlastnosti úlohy Batch AI|
|/jobs/remoteLoginInformation/action|Zobrazí informace o vzdálené přihlášení pro úlohy Batch AI|
|/Jobs/Terminate/Action|Ukončí úlohu Batch AI|
|/ úlohy/zápisu|Vytvoří novou úlohu Batch AI nebo aktualizuje existující úlohy Batch AI|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků AI Batch a umožňuje vytváření prostředků Batch AI|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operace | Popis |
|---|---|
|/billingPeriods/Read|Zobrazí seznam dostupných fakturační období|
|/Invoices/Read|K dispozici faktury seznamy|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operace | Popis |
|---|---|
|/mapApis/Delete|Operace odstranění|
|/mapApis/listSecrets/action|Uvádí tajné klíče.|
|/mapApis/listSingleSignOnToken/action|Přečíst autorizační token jednotného přihlašování pro prostředek|
|/mapApis/Read|Operace čtení|
|/mapApis/regenerateKey/action|Znovu vygeneruje klíč.|
|/mapApis/Write|Operace zápisu|
|/ Operací/čtení|Popis operace.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operace | Popis |
|---|---|
|/ checknameavailability nebo akce|Zkontroluje, jestli je název k dispozici, aby se dal použít spolu s novou mezipamětí Redis Cache.|
|/Operations/Read|Obsahuje seznam operací, které podporuje zprostředkovatel 'Microsoft.Cache'.|
|/redis/DELETE|Odstranit celou službu Redis Cache|
|/redis/export/action|Export dat Redis v určeném formátu do objektů blob úložiště označených prefixem|
|/redis/firewallRules/delete|Odstraní pravidla firewallu protokolu IP mezipaměti Redis Cache.|
|/redis/firewallRules/read|Získá pravidla firewallu protokolu IP mezipaměti Redis Cache.|
|/redis/firewallRules/write|Upraví pravidla firewallu protokolu IP mezipaměti Redis Cache.|
|/redis/forceReboot/action|Vynuťte restartování instance mezipaměti, což ale může mít za následek ztrátu dat.|
|/redis/import/action|Import dat určeného formátu z víc objektů blob do Redis|
|/redis/linkedservers/delete|Odstranit propojený server z mezipaměti Redis Cache|
|/redis/linkedservers/read|Získat propojené servery přidružené k mezipaměti Redis Cache|
|/redis/linkedservers/Write|Přidat propojený server do mezipaměti Redis Cache|
|/redis/listKeys/action|Zobrazit hodnotu přístupových klíčů služby Redis Cache na portálu pro správu|
|/redis/listUpgradeNotifications/read|Vypíše seznam nejnovějších upozornění na upgrady pro klienta mezipaměti.|
|/redis/Locations/operationresults/Read|Získá výsledek dlouho běžící operaci, pro který byla hlavička 'Umístění' dříve vrácen do klienta|
|/redis/metricDefinitions/Read|Načte dostupné metriky pro Redis Cache.|
|/redis/patchSchedules/delete|Odstraní plán oprav mezipaměti Redis Cache.|
|/redis/patchSchedules/read|Získá plán oprav mezipaměti Redis Cache.|
|/redis/patchSchedules/write|Upraví plán oprav mezipaměti Redis Cache.|
|/redis/Read|Zobrazit nastavení a konfiguraci Redis Cache na portálu pro správu|
|/redis/regenerateKey/action|Změnit hodnotu přístupových klíčů služby Redis Cache na portálu pro správu|
|/redis/Start/Action|Spuštění instance mezipaměti|
|/redis/stop/Action|Zastavení instance mezipaměti|
|/ redis a zápis|Upravit nastavení a konfiguraci služby Redis Cache na portálu pro správu|
|/ registrace nebo akce|Zaregistruje poskytovatele prostředků Microsoft.Cache u předplatného.|
|nebo zrušit registraci nebo akce|Zruší registraci poskytovatele prostředků Microsoft.Cache u předplatného.|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Operace | Popis |
|---|---|
|/ registrace nebo akce|Registruje zprostředkovatele prostředků kapacity a umožňuje vytváření kapacity prostředků.|
|/ reservationorders nebo akce|Aktualizovat všechny rezervace|
|/reservationorders/DELETE|Odstranit všechny rezervace|
|/reservationorders/read|Číst všechny rezervace|
|/reservationorders/reservations/Action|Aktualizovat všechny rezervace|
|/reservationorders/reservations/DELETE|Odstranit všechny rezervace|
|/reservationorders/reservations/Read|Číst všechny rezervace|
|/reservationorders/reservations/revisions/Read|Číst všechny rezervace|
|/reservationorders/reservations/Write|Vytvořte všechny rezervace|
|/ reservationorders/zápisu|Vytvořte všechny rezervace|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Operace | Popis |
|---|---|
|/ CheckNameAvailability nebo akce||
|/ CheckResourceUsage nebo akce||
|/edgenodes/DELETE||
|/edgenodes/Read||
|/ edgenodes/zápisu||
|/operationresults/DELETE||
|/operationresults/profileresults/CheckResourceUsage/Action||
|/operationresults/profileresults/DELETE||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/Action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/Read||
|/operationresults/profileresults/endpointresults/customdomainresults/Write||
|/operationresults/profileresults/endpointresults/DELETE||
|/operationresults/profileresults/endpointresults/Load/Action||
|/operationresults/profileresults/endpointresults/originresults/DELETE||
|/operationresults/profileresults/endpointresults/originresults/Read||
|/operationresults/profileresults/endpointresults/originresults/Write||
|/operationresults/profileresults/endpointresults/PURGE/Action||
|/operationresults/profileresults/endpointresults/Read||
|/operationresults/profileresults/endpointresults/Start/Action||
|/operationresults/profileresults/endpointresults/stop/Action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/Write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/Read||
|/operationresults/profileresults/Write||
|/operationresults/Read||
|/ operationresults/zápisu||
|/Operations/Read||
|/Profiles/CheckResourceUsage/Action||
|/Profiles/DELETE||
|/Profiles/Endpoints/CheckResourceUsage/Action||
|/Profiles/Endpoints/customdomains/DELETE||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/Profiles/Endpoints/customdomains/Read||
|/Profiles/Endpoints/customdomains/Write||
|/Profiles/Endpoints/DELETE||
|/Profiles/Endpoints/Load/Action||
|/Profiles/Endpoints/origins/DELETE||
|/Profiles/Endpoints/origins/Read||
|/Profiles/Endpoints/origins/Write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro prostředek|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek|
|/Profiles/Endpoints/providers/Microsoft.Insights/logDefinitions/Read|Získá dostupné protokoly pro Microsoft.Cdn|
|/Profiles/Endpoints/PURGE/Action||
|/Profiles/Endpoints/Read||
|/Profiles/Endpoints/Start/Action||
|/Profiles/Endpoints/stop/Action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/Profiles/Endpoints/Write||
|/profiles/GenerateSsoUri/action||
|/Profiles/GetSupportedOptimizationTypes/Action||
|/Profiles/Read||
|/ profily a zápis||
|/ registrace nebo akce|Zaregistruje předplatné u poskytovatele prostředků CDN a povolí vytváření profilů CDN.|
|/ ValidateProbe nebo akce||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operace | Popis |
|---|---|
|/certificateOrders/Certificates/DELETE|Odstraňte existující certifikát|
|/certificateOrders/Certificates/Read|Získat seznam certifikátů|
|/certificateOrders/Certificates/Write|Přidat nový certifikát nebo aktualizovat stávající|
|/ certificateOrders/odstranit|Odstraňte existující AppServiceCertificate|
|/certificateOrders/Operations/Read|Zobrazí seznam všech operací z registrace certifikátu služby aplikace|
|/ certificateOrders/čtení|Získání seznamu CertificateOrders|
|/certificateOrders/reissue/Action|Opakujte existující certificateorder|
|/certificateOrders/renew/Action|Obnovit existující certificateorder|
|/certificateOrders/resendEmail/Action|Znovu odeslat e-mail certifikátu|
|/certificateOrders/resendRequestEmails/Action|Znovu odeslat žádost o e-mailů jinou e-mailová adresa|
|/certificateOrders/resendRequestEmails/Action|Načtení lokality zapečetění vydaného certifikátu služby aplikace|
|/certificateOrders/retrieveCertificateActions/Action|Načtení seznamu akce certifikátu|
|/certificateOrders/retrieveEmailHistory/Action|Načtení historie certifikát e-mailu|
|/certificateOrders/verifyDomainOwnership/Action|Ověřit vlastnictví domény|
|/ certificateOrders/zápisu|Přidat nové certificateOrder nebo aktualizovat stávající|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Zřízení instančního objektu pro objekt zabezpečení služby aplikace|
|/ registrace nebo akce|Registrace poskytovatele prostředků Certificates společnosti Microsoft pro předplatné|
|/validateCertificateRegistrationInformation/Action|Ověření objektu nákupu certifikát bez odeslání|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operace | Popis |
|---|---|
|/Capabilities/Read|Umožňuje zobrazit schopnosti.|
|/checkDomainNameAvailability/action|Umožňuje zkontrolovat dostupnost zadaného názvu domény.|
|/domainNames/active/write|Slouží k nastavení názvu aktivní domény.|
|/domainNames/availabilitySets/read|Umožňuje zobrazit skupinu dostupnosti pro prostředek.|
|/domainNames/capabilities/read|Ukazuje možnosti názvu domény|
|/domainNames/delete|Umožňuje odebrat názvy domén pro prostředky.|
|/domainNames/extensions/delete|Odebere rozšíření názvu domény.|
|/domainNames/extensions/operationStatuses/read|Přečte stav operace pro rozšíření názvů domén.|
|/domainNames/extensions/read|Vrátí rozšíření názvu domény.|
|/domainNames/extensions/write|Přidá rozšíření názvu domény.|
|/domainNames/internalLoadBalancers/delete|Umožňuje odebrat interní nástroj pro vyrovnávání zatížení.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Přečte stav operace pro interní služby Load Balancer názvů domén.|
|/domainNames/internalLoadBalancers/read|Umožňuje načíst interní nástroje pro vyrovnávání zatížení.|
|/domainNames/internalLoadBalancers/write|Umožňuje vytvořit nový interní nástroj pro vyrovnávání zatížení.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/Read|Přečte stav operace pro sady koncových bodů s vyrovnaným zatížením u názvů domén.|
|/domainNames/loadBalancedEndpointSets/read|Umožňuje zobrazit skupiny koncových bodů s vyrovnáváním zatížení.|
|/domainNames/read|Umožňuje načíst názvy domén pro prostředky.|
|/domainNames/serviceCertificates/delete|Umožňuje odstranit používané certifikáty služeb.|
|/domainNames/serviceCertificates/operationStatuses/read|Přečte stav operace pro certifikáty služeb názvů domén.|
|/domainNames/serviceCertificates/read|Umožňuje načíst používané certifikáty služeb.|
|/domainNames/serviceCertificates/write|Umožňuje přidat nebo změnit používané certifikáty služeb.|
|/domainNames/slots/delete|Umožňuje odstranit zadaný slot pro nasazení.|
|/domainNames/slots/operationStatuses/read|Přečte stav operace pro sloty názvů domén.|
|/domainNames/slots/read|Umožňuje zobrazit sloty pro nasazení.|
|/domainNames/slots/roles/extensionReferences/delete|Odebere odkaz na rozšíření u role slotu nasazení.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Přečte stav operace pro reference na rozšíření rolí slotů názvů domén.|
|/domainNames/slots/roles/extensionReferences/read|Vrátí odkaz na rozšíření u role slotu nasazení.|
|/domainNames/slots/roles/extensionReferences/write|Přidá nebo upraví odkaz na rozšíření u role slotu nasazení.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Umožňuje načíst nastavení diagnostiky.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Umožňuje přidat nebo změnit nastavení diagnostiky.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Umožňuje načíst definice metrik.|
|/domainNames/slots/roles/read|Umožňuje načíst roli slotu pro nasazení.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Přečte stav operace pro instance rolí u rolí slotů názvů domén.|
|/domainNames/slots/roles/roleInstances/read|Umožňuje načíst instanci role.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Znovu sestaví instanci role.|
|/domainNames/slots/roles/roleInstances/reimage/action|Umožňuje obnovit instanci role z image.|
|/domainNames/slots/roles/roleInstances/restart/action|Umožňuje restartovat instance role.|
|/domainNames/slots/start/action|Umožňuje spustit slot pro nasazení.|
|/domainNames/slots/state/start/write|Umožňuje změnit stav slotu pro nasazení na Zastavený.|
|/domainNames/slots/state/stop/write|Umožňuje změnit stav slotu pro nasazení na Spuštěný.|
|/domainNames/slots/stop/action|Umožňuje pozastavit slot pro nasazení.|
|/domainNames/slots/upgradeDomain/write|Umožňuje upgradovat doménu.|
|/domainNames/slots/write|Umožňuje vytvořit nebo aktualizovat slot pro nasazení.|
|/domainNames/swap/action|Umožňuje přepnout přípravný slot na produkční.|
|/domainNames/write|Umožňuje přidat nebo změnit názvy domén pro prostředky.|
|/ moveSubscriptionResources nebo akce|Přesune všechny klasické prostředky do jiného předplatného.|
|/operatingSystemFamilies/read|Vypíše seznam řad hostovaných operačních systémů, které jsou dostupné v Microsoft Azure, a taky vypíše verze operačních systémů dostupné pro každou řadu.|
|/operatingSystems/read|Vypíše seznam verzí hostovaných operačních systémů, které jsou aktuálně dostupné v Microsoft Azure.|
|/quotas/read|Umožňuje načíst kvótu pro předplatné.|
|/ registrace nebo akce|Registrovat pro klasické výpočetní prostředky|
|/resourceTypes/skus/Read|Načte seznam skladových položek pro podporované typy prostředků.|
|/ validateSubscriptionMoveAvailability nebo akce|Ověří dostupnost klasické operace přesunu u předplatného.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Odstraní skupinu zabezpečení sítě spojenou s virtuálním počítačem.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Přečte stav operace pro skupiny zabezpečení sítě spojené s virtuálními počítači.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Načte skupinu zabezpečení sítě spojenou s virtuálním počítačem.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Přidá skupinu zabezpečení sítě spojenou s virtuálním počítačem.|
|/virtualMachines/asyncOperations/read|Umožňuje získat seznam možných asynchronních operací.|
|/virtualMachines/attachDisk/action|Umožňuje připojit datový disk k virtuálnímu počítači.|
|/virtualMachines/DELETE|Umožňuje odebrat virtuální počítače.|
|/virtualMachines/detachDisk/action|Umožňuje odpojit datový disk od virtuálního počítače.|
|/virtualMachines/disks/read|Umožňuje načíst seznam datových disků.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Umožňuje stáhnout soubor RDP pro virtuální počítač.|
|/virtualMachines/Extensions/operationStatuses/Read|Přečte stav operace pro rozšíření virtuálních počítačů.|
|/virtualMachines/Extensions/Read|Umožňuje načíst rozšíření virtuálního počítače.|
|/virtualMachines/Extensions/Write|Umožňuje vložit rozšíření virtuálního počítače.|
|/virtualMachines/Metrics/Read|Umožňuje načíst metriky.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Odstraní skupinu zabezpečení sítě spojenou se síťovým rozhraním.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Přečte stav operace pro skupiny zabezpečení sítě spojené s virtuálními počítači.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Načte skupinu zabezpečení sítě spojenou se síťovým rozhraním.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Přidá skupinu zabezpečení sítě spojenou se síťovým rozhraním.|
|/virtualMachines/operationStatuses/Read|Přečte stav operace pro virtuální počítače|
|/virtualMachines/performMaintenance/Action|Provede údržbu na virtuálním počítači.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Umožňuje načíst nastavení diagnostiky.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Umožňuje přidat nebo změnit nastavení diagnostiky.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Umožňuje načíst definice metrik.|
|/virtualMachines/read|Umožňuje načíst seznam virtuálních počítačů.|
|/virtualMachines/redeploy/Action|Znovu nasadí virtuální počítač.|
|/virtualMachines/restart/Action|Umožňuje restartovat virtuální počítače.|
|/virtualMachines/Shutdown/Action|Umožňuje vypnout virtuální počítač.|
|/virtualMachines/Start/Action|Umožňuje spustit virtuální počítač.|
|/virtualMachines/stop/action|Umožňuje zastavit virtuální počítač.|
|/ virtuálních počítačů a zápis|Umožňuje přidat nebo upravit virtuální počítače.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operace | Popis |
|---|---|
|/gatewaySupportedDevices/read|Umožňuje načíst seznam podporovaných zařízení.|
|/networkSecurityGroups/delete|Odstraní skupinu zabezpečení sítě.|
|/networkSecurityGroups/operationStatuses/read|Přečte stav operace pro skupinu zabezpečení sítě.|
|/networkSecurityGroups/read|Načte skupinu zabezpečení sítě.|
|/networkSecurityGroups/securityRules/delete|Odstraní pravidlo zabezpečení.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Přečte stav operace pro pravidla zabezpečení skupiny u zabezpečení sítě.|
|/networkSecurityGroups/securityRules/read|Načte pravidlo zabezpečení.|
|/networkSecurityGroups/securityRules/write|Přidá nebo aktualizuje pravidlo zabezpečení.|
|/networkSecurityGroups/write|Přidá novou skupinu zabezpečení sítě.|
|/quotas/read|Umožňuje načíst kvótu pro předplatné.|
|/ registrace nebo akce|Registrace pro klasickou síť|
|/reservedIps/delete|Umožňuje odstranit vyhrazenou IP adresu.|
|/reservedIps/join/action|Připojit se k rezervované IP adrese|
|/reservedIps/link/action|Odkaz na rezervovanou IP adresu|
|/reservedIps/operationStatuses/read|Přečte stav operace pro rezervované IP adresy.|
|/reservedIps/read|Umožňuje načíst vyhrazené IP adresy.|
|/reservedIps/write|Umožňuje přidat novou vyhrazenou IP adresu.|
|/virtualNetworks/capabilities/read|Umožňuje zobrazit schopnosti.|
|/virtualNetworks/checkIPAddressAvailability/action|Zkontroluje dostupnost dané IP adresy ve virtuální síti.|
|/virtualNetworks/delete|Umožňuje odstranit virtuální síť.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Umožňuje zrušit odvolání klientského certifikátu.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Umožňuje načíst odvolané klientské certifikáty.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Umožňuje odvolat klientský certifikát.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Umožňuje odstranit klientský certifikát brány virtuální sítě.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Umožňuje stáhnout certifikát podle kryptografického otisku.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Vypíše balíček certifikátů bran virtuální sítě.|
|/virtualNetworks/gateways/clientRootCertificates/read|Umožňuje vyhledat klientské kořenové certifikáty.|
|/virtualNetworks/gateways/clientRootCertificates/write|Umožňuje nahrát nový klientský kořenový certifikát.|
|/virtualNetworks/gateways/connections/connect/action|Umožňuje připojit lokalitu k připojení brány lokality.|
|/virtualNetworks/gateways/connections/disconnect/action|Umožňuje odpojit lokalitu od připojení brány lokality.|
|/virtualNetworks/gateways/connections/read|Umožňuje načíst seznam připojení.|
|/virtualNetworks/gateways/connections/test/action|Umožňuje otestovat připojení lokality k bráně lokality.|
|/virtualNetworks/gateways/delete|Umožňuje odstranit bránu virtuální sítě.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Umožňuje stáhnout skript pro konfiguraci zařízení.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Umožňuje stáhnout diagnostiku brány.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Umožňuje načíst klíč služby okruhů.|
|/virtualNetworks/gateways/listPackage/action|Umožňuje vypsat balíček bran virtuální sítě.|
|/virtualNetworks/gateways/operationStatuses/read|Přečte stav operace pro brány virtuálních sítí.|
|/virtualNetworks/gateways/packages/read|Umožňuje načíst balíček bran virtuální sítě.|
|/virtualNetworks/gateways/read|Umožňuje načíst brány virtuální sítě.|
|/virtualNetworks/gateways/startDiagnostics/action|Umožňuje spustit diagnostiku pro bránu virtuální sítě.|
|/virtualNetworks/gateways/stopDiagnostics/action|Umožňuje zastavit diagnostiku pro bránu virtuální sítě.|
|/virtualNetworks/gateways/write|Umožňuje přidat bránu virtuální sítě.|
|/virtualNetworks/join/action|Připojí se k virtuální síti.|
|/virtualNetworks/operationStatuses/read|Přečte stav operace pro virtuální sítě.|
|/virtualNetworks/peer/action|Vytvoří partnerskou virtuální síť s jinou virtuální sítí.|
|/virtualNetworks/read|Umožňuje načíst virtuální síť.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Odstraní skupinu zabezpečení sítě spojenou s podsítí.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Přečte stav operace pro skupinu zabezpečení sítě spojenou s podsítí virtuální sítě.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Načte skupinu zabezpečení sítě spojenou s podsítí.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Přidá skupinu zabezpečení sítě spojenou s podsítí.|
|/virtualNetworks/write|Umožňuje přidat novou virtuální síť.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operace | Popis |
|---|---|
|/Capabilities/Read|Umožňuje zobrazit schopnosti.|
|/checkStorageAccountAvailability/action|Umožňuje zkontrolovat dostupnost účtu úložiště.|
|/disks/read|Vrátí disk účtu úložiště.|
|/Images/Read|Vrátí image.|
|/osImages/Read|Vrátí image operačního systému.|
|/publicImages/Read|Načte veřejnou image virtuálního počítače.|
|/quotas/read|Umožňuje načíst kvótu pro předplatné.|
|/ registrace nebo akce|Umožňuje registraci v klasickém úložišti.|
|/storageAccounts/DELETE|Umožňuje odstranit účet úložiště.|
|/storageAccounts/disks/delete|Odstraní daný disk účtu úložiště.|
|/storageAccounts/disks/operationStatuses/read|Umožňuje načíst stav operace pro prostředek.|
|/storageAccounts/disks/read|Vrátí disk účtu úložiště.|
|/storageAccounts/disks/write|Přidá disk účtu úložiště.|
|/storageAccounts/images/delete|Odstraní danou image účtu storage.|
|/storageAccounts/images/read|Vrátí image účtu storage.|
|/storageAccounts/listKeys/action|Umožňuje vypsat seznam přístupových klíčů pro účty úložiště.|
|/storageAccounts/operationStatuses/Read|Umožňuje načíst stav operace pro prostředek.|
|/storageAccounts/osImages/delete|Odstraní danou image operačního systému pro účet Storage.|
|/storageAccounts/osImages/read|Vrátí image operačního systému pro účet Storage.|
|/storageAccounts/Read|Umožňuje načíst účet úložiště se zadaným účtem.|
|/storageAccounts/regenerateKey/action|Umožňuje znovu vytvořit existující přístupové klíče pro účet úložiště.|
|/storageAccounts/services/diagnosticSettings/read|Umožňuje načíst nastavení diagnostiky.|
|/storageAccounts/services/diagnosticSettings/write|Umožňuje přidat nebo změnit nastavení diagnostiky.|
|/storageAccounts/services/metricDefinitions/read|Umožňuje načíst definice metrik.|
|/storageAccounts/Services/Metrics/Read|Umožňuje načíst metriky.|
|/storageAccounts/Services/Read|Umožňuje načíst dostupné služby.|
|/ storageAccounts/zápisu|Umožňuje přidat nový účet úložiště.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operace | Popis |
|---|---|
|/Accounts/DELETE|Odstraní účty rozhraní API.|
|/accounts/listKeys/action|Zobrazit seznam klíčů|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky prostředku.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky prostředku.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Získá dostupné metriky pro službu Cognitive Services.|
|/Accounts/Read|Načte účty rozhraní API.|
|/accounts/regenerateKey/action|Znovu vygenerovat klíč|
|/Accounts/skus/Read|Načte dostupné skladové položky (SKU) pro existující prostředek.|
|/Accounts/usages/Read|Získá využití kvóty pro existující prostředek.|
|/ účty a zápis|Zapíše účty rozhraní API.|
|/locations/checkSkuAvailability/action|Přečte dostupná SKU pro předplatné.|
|/ Operací/čtení|Zobrazí seznam všech dostupných operací|
|/ registrace nebo akce|Zaregistruje předplatné pro kognitivní služby|
|/skus/Read|Načte dostupné edice kognitivní služeb.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operace | Popis |
|---|---|
|/RateCard/read|Vrátí nabízejí data, prostředku nebo měření metadata a sazby pro dané předplatné.|
|/UsageAggregates/read|Získá předplatné Microsoft Azure spotřeby. Výsledek obsahuje agreguje data o využití, předplatné a prostředků související informace, na konkrétní časové rozmezí.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operace | Popis |
|---|---|
|/availabilitySets/delete|Umožňuje odstranit sadu dostupnosti.|
|/availabilitySets/read|Umožňuje získat vlastnosti sady dostupnosti.|
|/availabilitySets/vmSizes/read|Umožňuje zobrazit seznam dostupných velikostí pro vytváření nebo aktualizaci virtuálního počítače v sadě dostupnosti.|
|/ availabilitySets/zápisu|Umožňuje vytvořit novou nebo aktualizovat stávající sadu dostupnosti.|
|/disks/beginGetAccess/action|Získat identifikátor URI SAS disku pro přístup k objektu blob|
|/disks/delete|Odstraní disky.|
|/disks/endGetAccess/action|Odvolá identifikátor URI SAS disku|
|/disks/read|Získá vlastnosti disku.|
|/ disků a zápis|Vytvoří nový disk nebo aktualizuje stávající.|
|/images/delete|Odstraní image.|
|/Images/Read|Získá vlastnosti image.|
|/ bitové kopie a zápis|Vytvoří novou image nebo aktualizuje nějakou existující.|
|/Locations/capsOperations/Read|Získá stav asynchronní operace CAP k vzdálené ploše|
|/locations/diskOperations/read|Získá stav asynchronní operace disku|
|/Locations/Operations/Read|Umožňuje získat stav asynchronní operace.|
|/Locations/Publishers/artifacttypes/OFFERS/Read|Umožňuje získat vlastnosti nabízejí Image platformy|
|/Locations/Publishers/artifacttypes/OFFERS/skus/Read|Umožňuje získat vlastnosti Sku Image platformy|
|/Locations/Publishers/artifacttypes/OFFERS/skus/versions/Read|Umožňuje získat vlastnosti bitové kopie verze platformy|
|/Locations/Publishers/artifacttypes/Types/Read|Umožňuje získat vlastnosti rozšíření VMExtension typu|
|/Locations/Publishers/artifacttypes/Types/versions/Read|Umožňuje získat vlastnosti rozšíření VMExtension verze|
|/Locations/Publishers/Read|Umožňuje získat vlastnosti vydavatelem|
|/Locations/runCommands/Read|Seznam dostupných spouštěcích příkazů v umístění|
|/Locations/usages/Read|Umožňuje získat limity služby a aktuální využívaná množství výpočetních prostředků předplatného v daném umístění.|
|/locations/vmSizes/read|Umožňuje zobrazit seznam dostupných velikostí virtuálních počítačů v daném umístění.|
|/Operations/Read|Umožňuje zobrazit seznam operací dostupných na zprostředkovateli prostředků Microsoft.Compute.|
|/ registrace nebo akce|Umožňuje registrovat předplatné zprostředkovatele prostředků Microsoft.Compute.|
|/restorePointCollections/delete|Odstraní kolekci bodů obnovení a v ní obsažené body obnovení.|
|/restorePointCollections/read|Získá vlastnosti kolekce bodů obnovení.|
|/restorePointCollections/restorePoints/delete|Odstraní bod obnovení.|
|/restorePointCollections/restorePoints/read|Získá vlastnosti bodu obnovení.|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Získá vlastnosti bodu obnovení spolu s identifikátory URI SAS objektů blob.|
|/restorePointCollections/restorePoints/write|Vytvoří nový bod obnovení|
|/restorePointCollections/write|Vytvoří novou kolekci bodů obnovení nebo aktualizuje nějakou, která už existuje.|
|/sharedVMImages/delete|Odstraní SharedVMImage|
|/sharedVMImages/Read|Umožňuje získat vlastnosti SharedVMImage|
|/sharedVMImages/versions/delete|Odstranit SharedVMImageVersion|
|/sharedVMImages/versions/Read|Umožňuje získat vlastnosti SharedVMImageVersion|
|/sharedVMImages/versions/Replicate/Action|Replikace SharedVMImageVersion do cílové oblasti|
|/sharedVMImages/versions/Write|Vytvořit nový SharedVMImageVersion nebo aktualizovat stávající|
|/ sharedVMImages/zápisu|Vytvoří nový SharedVMImage nebo aktualizuje existující|
|/skus/Read|Získá seznam Microsoft.Compute SKU, které jsou k dispozici pro vaše předplatné|
|/snapshots/beginGetAccess/action|Získat identifikátor URI SAS snímku pro přístup k objektu blob|
|/snapshots/DELETE|Odstranit snímek|
|/snapshots/endGetAccess/action|Odvolá identifikátor URI SAS snímku|
|/snapshots/Read|Získat vlastnosti snímku|
|/ snímky a zápis|Vytvoří nový snímek nebo aktualizuje stávající.|
|/virtualMachines/Capture/Action|Umožňuje zachytit virtuální počítač zkopírováním virtuálních pevných disků a vygenerováním šablony, kterou je možné použít k vytvoření podobných virtuálních počítačů.|
|/virtualMachines/convertToManagedDisks/action|Převede disky virtuálního počítače založené na objektech blob na spravované disky.|
|/virtualMachines/deallocate/Action|Umožňuje vypnout virtuální počítač a uvolnit výpočetní prostředky.|
|/virtualMachines/DELETE|Umožňuje odstranit virtuální počítač.|
|/virtualMachines/Extensions/DELETE|Umožňuje odstranit rozšíření virtuálního počítače.|
|/virtualMachines/Extensions/Read|Umožňuje získat vlastnosti rozšíření virtuálního počítače.|
|/virtualMachines/Extensions/Write|Umožňuje vytvořit nové nebo aktualizovat stávající rozšíření virtuálního počítače.|
|/virtualMachines/generalize/Action|Umožňuje nastavit stav virtuálního počítače na Zobecněno a připravit virtuální počítač na zachycení.|
|/virtualMachines/instanceView/read|Umožňuje získat podrobný stav runtime virtuálního počítače a jeho prostředků.|
|/virtualMachines/performMaintenance/Action|Provádí operace údržby ve virtuálním počítači.|
|/virtualMachines/powerOff/Action|Umožňuje vypnout virtuální počítač. Všimněte si, že virtuální počítač bude dál účtovat.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Přečte definice metriky Virtuální počítač.|
|/virtualMachines/read|Umožňuje získat vlastnosti virtuálního počítače.|
|/virtualMachines/redeploy/Action|Znovu nasadí virtuální počítač.|
|/virtualMachines/reimage/Action|Obnoví virtuální počítač, který používá rozdílový disk, z image.|
|/virtualMachines/restart/Action|Umožňuje restartovat virtuální počítač.|
|/virtualMachines/runCommand/Action|Spustí na virtuálním počítači předem definovaný skript.|
|/virtualMachines/Start/Action|Umožňuje spustit virtuální počítač.|
|/virtualMachines/vmSizes/read|Umožňuje zobrazit seznam dostupných velikostí, na které je možné virtuální počítač aktualizovat.|
|/ virtuálních počítačů a zápis|Umožňuje vytvořit nový nebo aktualizovat stávající virtuální počítač.|
|/virtualMachineScaleSets/deallocate/action|Umožňuje vypnout instance škálovací sady virtuálních počítačů a uvolnit výpočetní prostředky, které tyto instance zabíraly. |
|/virtualMachineScaleSets/delete|Umožňuje odstranit škálovací sadu virtuálních počítačů.|
|/virtualMachineScaleSets/delete/action|Umožňuje odstranit instance škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/extensions/delete|Odstraní rozšíření škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/extensions/read|Načte vlastnosti rozšíření škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/extensions/write|Vytvoří nové rozšíření škálovací sady virtuálních počítačů nebo aktualizuje existující rozšíření.|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Ručně provede aktualizace domén platformy service Fabric, sady škálování virtuálního počítače na dokončení čeká na aktualizace, která je zablokované|
|/virtualMachineScaleSets/instanceView/read|Umožňuje získat zobrazení instance škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/manualUpgrade/Action|Umožňuje ručně aktualizovat instance na poslední model škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/networkInterfaces/read|Získat vlastnosti všech síťových rozhraní systému sadu škálování virtuálního počítače|
|/virtualMachineScaleSets/osUpgradeHistory/read|Získá historii upgrady operačního systému pro sadu škálování virtuálního počítače|
|/virtualMachineScaleSets/performMaintenance/action|Provede plánované údržby v instancích systému sadu škálování virtuálního počítače|
|/virtualMachineScaleSets/powerOff/action|Umožňuje vypnout instance škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Přečte definice metriky Škálovací sada virtuálních počítačů.|
|/virtualMachineScaleSets/publicIPAddresses/read|Získat vlastnosti všechny veřejné IP adresy z sadu škálování virtuálního počítače|
|/virtualMachineScaleSets/read|Umožňuje získat vlastnosti škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/redeploy/Action|Znovu nasaďte instance sadu škálování virtuálního počítače|
|/virtualMachineScaleSets/reimage/action|Umožňuje obnovit instance škálovací sady virtuálních počítačů z image.|
|/virtualMachineScaleSets/restart/action|Umožňuje restartovat instance škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Zruší upgrade škálovací sady virtuálních počítačů se zajištěním provozu.|
|/virtualMachineScaleSets/rollingUpgrades/read|Získá nejnovější stav upgradu škálovací sady virtuálních počítačů se zajištěním provozu.|
|/virtualMachineScaleSets/scale/action|Ověřte, že existující škálovací sada virtuálních počítačů může horizontálně snížit nebo navýšit kapacitu na zadaný počet instancí.|
|/virtualMachineScaleSets/skus/read|Umožňuje zobrazit seznam platných SKU pro existující škálovací sadu virtuálních počítačů.|
|/virtualMachineScaleSets/start/action|Umožňuje spustit instance škálovací sady virtuálních počítačů.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Umožňuje vypnout a uvolnit výpočetní prostředky pro virtuální počítač ve škálovací sadě virtuálních počítačů.|
|/virtualMachineScaleSets/virtualMachines/delete|Umožňuje odstranit konkrétní virtuální počítač ve škálovací sadě virtuálních počítačů.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Umožňuje načíst zobrazení instance virtuálního počítače ve škálovací sadě virtuálních počítačů.|
|/ virtualMachineScaleSets nebo virtuálních počítačů nebo networkInterfaces/konfigurace IP adresy/publicipaddresses, na které/čtení|Získáte vlastnosti veřejné IP adresy, které jsou vytvořené pomocí sady škálování virtuálního počítače. Sadu škálování virtuálního počítače můžete vytvořit maximálně jednu veřejnou IP adresu na ipconfiguration (privátní IP)|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Získáte vlastnosti jednoho nebo všech konfigurace protokolu IP síťového rozhraní vytvořený sadu škálování virtuálního počítače. Konfigurace protokolu IP představují privátních IP adres|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Získat vlastnosti jednoho nebo všech síťových rozhraní virtuálního počítače vytvořené pomocí sady škálování virtuálního počítače|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Provádí plánovanou údržbu instanci virtuálního počítače ve Škálovací sadě virtuálního počítače|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Umožňuje vypnout instanci virtuálního počítače ve škálovací sadě virtuálních počítačů.|
|/ virtualMachineScaleSets/virtuálních počítačů nebo poskytovatelé/Microsoft.Insights/metricDefinitions/read|Přečte definice metriky Virtuální počítač ve škálovací sadě.|
|/virtualMachineScaleSets/virtualMachines/read|Umožňuje načíst vlastnosti virtuálního počítače ve škálovací sadě virtuálních počítačů.|
|/virtualMachineScaleSets/virtualMachines/redeploy/Action|Opětovně nasadí instanci virtuálního počítače ve Škálovací sadě virtuálního počítače|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Obnoví instanci virtuálního počítače ve škálovací sadě virtuálních počítačů z image.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Umožňuje restartovat instanci virtuálního počítače ve škálovací sadě virtuálních počítačů.|
|/virtualMachineScaleSets/virtualMachines/start/action|Umožňuje spustit instanci virtuálního počítače ve škálovací sadě virtuálních počítačů.|
|/virtualMachineScaleSets/virtualMachines/write|Umožňuje aktualizovat vlastnosti virtuálního počítače ve škálovací sadě virtuálních počítačů.|
|/virtualMachineScaleSets/write|Umožňuje vytvořit novou nebo aktualizovat stávající škálovací sadu virtuálních počítačů.|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Operace | Popis |
|---|---|
|/balances/Read|Zobrazí seznam využití shrnutí fakturační období pro skupinu pro správu.|
|/budgets/Read|Zobrazí seznam rozpočty předplatné nebo skupinu pro správu.|
|/ rozpočty a zápis|Vytvoří, aktualizovat a Odstranit rozpočty předplatné nebo skupinu pro správu.|
|/marketplaces/Read|Zobrazí seznam podrobnosti o obor odběrů EA a WebDirect využití prostředků marketplace.|
|/Operations/Read|Zobrazí seznam všech podporovaných operací Microsoft.Consumption poskytovatele prostředků.|
|/pricesheets/Read|Zobrazí seznam dat Pricesheets pro předplatné nebo skupinu pro správu.|
|/reservationDetails/read|Zobrazí seznam podrobnosti o využití pro vyhrazené instance rezervace pořadí nebo správu skupiny. Podrobnosti o data jsou za instance pro každý den úroveň.|
|/reservationSummaries/read|Zobrazí seznam využití shrnutí vyhrazená instance podle rezervace pořadí nebo správu skupin. Souhrnná data je buď na úrovni denní nebo měsíční.|
|/reservationTransactions/read|Zobrazí seznam historie transakcí vyhrazená instance podle skupiny pro správu.|
|/Terms/Read|Zobrazí seznam podmínek pro předplatné nebo skupinu pro správu.|
|/usageDetails/Read|Zobrazí seznam podrobnosti o využití pro obor pro EA i WebDirect předplatné.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Operace | Popis |
|---|---|
|/containerGroups/Containers/Logs/Read|Získá protokoly pro konkrétní kontejner.|
|/containerGroups/delete|Odstraní konkrétní skupinu kontejnerů.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro kontejner skupiny.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro kontejner skupiny.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro skupinu kontejneru.|
|/containerGroups/read|Získá všechny skupiny kontejnerů.|
|/containerGroups/write|Vytvoří nebo aktualizuje konkrétní skupinu kontejnerů.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operace | Popis |
|---|---|
|/checkNameAvailability/read|Zkontroluje, zda je k dispozici pro použití registru název kontejneru.|
|/Locations/operationResults/Read|Získá výsledek asynchronní operace|
|/Operations/Read|Zobrazí seznam všech dostupných operací REST API služby Azure kontejneru registru|
|/ registrace nebo akce|Zaregistruje předplatné pro zprostředkovatele prostředků registru kontejneru a umožňuje vytvoření kontejneru registrech.|
|/registries/DELETE|Odstraní kontejner registru.|
|/registries/eventGridFilters/delete|Filtr událostí mřížky odstraní z registru kontejneru.|
|/registries/eventGridFilters/read|Získá vlastnosti filtru mřížky zadanou událost nebo vypíše všechny filtry mřížky událostí v registru zadaného kontejneru.|
|/registries/eventGridFilters/write|Vytvoří nebo aktualizuje filtr událostí mřížky pro kontejner registru se zadanými parametry.|
|/registries/listCredentials/Action|Uvádí přihlašovací údaje pro zadaný kontejner registru.|
|/registries/listUsages/read|Uvádí použití kvóty pro zadaný kontejner registru.|
|/registries/operationStatuses/Read|Získá stav registru asynchronní operace|
|/registries/Read|Získá vlastnosti registru zadaný kontejner nebo vypíše všechny registrech kontejneru pod zadaná skupina prostředků nebo předplatného.|
|/registries/regenerateCredential/Action|Regeneruje mezi přihlašovací údaje pro zadaný kontejner registru.|
|/registries/replications/DELETE|Odstraní replikace z registru kontejneru.|
|/registries/replications/operationStatuses/Read|Získá stav asynchronní operace replikace|
|/registries/replications/Read|Načte vlastnosti zadaného replikace nebo vypíše všechny replikace pro zadaný kontejner registru.|
|/registries/replications/Write|Vytvoří nebo aktualizuje replikace pro kontejner registru se zadanými parametry.|
|/registries/webhooks/delete|Odstraní webhook, jehož z registru kontejneru.|
|/registries/webhooks/getCallbackConfig/action|Získá konfiguraci URI služby a vlastní hlavičky pro webhooku.|
|/registries/webhooks/listEvents/Action|Seznam posledních událostí zadaný webhooku.|
|/registries/webhooks/operationStatuses/Read|Získá stav webhooku asynchronní operace|
|/registries/webhooks/ping/Action|Aktivuje událost ping k odeslání do webhooku.|
|/registries/webhooks/Read|Načte vlastnosti zadaného webhooku nebo vypíše všechny webhooků, které jsou pro zadaný kontejner registru.|
|/registries/webhooks/write|Vytvoří nebo aktualizuje webhook pro kontejner registru se zadanými parametry.|
|/ registrech a zápis|Vytvoří nebo aktualizuje registr kontejneru se zadanými parametry.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operace | Popis |
|---|---|
|/containerServices/delete|Odstraní určenou službu kontejneru.|
|/containerServices/read|Získá určenou službu kontejneru.|
|/containerServices/write|Použije nebo aktualizuje určenou službu kontejneru.|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operace | Popis |
|---|---|
|/Applications/DELETE|Operace odstranění|
|/applications/listSecrets/action|Uvést tajné klíče|
|/applications/listSingleSignOnToken/action|Přečíst jednotného přihlašování tokeny|
|/Applications/Read|Operace čtení|
|/ aplikace/zápisu|Operace zápisu|
|/ aplikace/zápisu|Operace zápisu|
|/listCommunicationPreference/action|Seznam komunikace předvoleb|
|/Operations/Read|operace čtení|
|/updateCommunicationPreference/action|Předvolby komunikace aktualizace|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operace | Popis |
|---|---|
|/hubs/adobemetadata/action|Vytvořit nebo aktualizovat veškerá Metadata, Adobe Statistika Azure zákazníků|
|/hubs/adobemetadata/read|Číst veškerá Metadata, Adobe Statistika Azure zákazníků|
|/hubs/authorizationPolicies/DELETE|Odstranit všechny zásady Azure zákazníků Statistika sdílený přístupový podpis|
|/hubs/authorizationPolicies/Read|Přečtěte si, že žádné přehledy Azure zákazníků sdílené zásady přístupu podpis|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Znova vygenerovat primární klíč Azure zákazníka Statistika zásada sdíleného přístupu podpis|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Znova vygenerovat sekundární klíč Azure zákazníka Statistika zásada sdíleného přístupu podpis|
|/hubs/authorizationPolicies/Write|Vytvořit nebo aktualizovat žádné zásady Azure zákazníků Statistika sdílený přístupový podpis|
|/hubs/Connectors/Activate/Action|Aktivovat všechny konektor služby Statistika Azure zákazníků|
|/hubs/Connectors/Activate/Action|Aktivovat všechny konektor služby Statistika Azure zákazníků|
|/hubs/Connectors/DELETE|Odstranit všechny konektory Statistika Azure zákazníků|
|/hubs/connectors/getruntimestatus/action|Získání běhového stavu všech konektor služby Statistika zákazníka Azure|
|/hubs/Connectors/Mappings/Activate/Action|Aktivovat žádné mapování Statistika konektor Azure zákazníků|
|/hubs/Connectors/Mappings/DELETE|Odstranit všechny Azure zákazníků Statistika konektor mapování|
|/hubs/Connectors/Mappings/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika konektor mapování|
|/hubs/Connectors/Mappings/Read|Číst všechny Azure zákazníků Statistika konektor mapování|
|/hubs/Connectors/Mappings/Write|Vytvořit nebo aktualizovat všechny Azure zákazníků Statistika konektor mapování|
|/hubs/Connectors/Operations/Read|Číst libovolný výsledek operace konektor služby Statistika zákazníka Azure|
|/hubs/Connectors/Read|Číst všechny konektor služby Statistika Azure zákazníků|
|/hubs/Connectors/saveauthinfo/Action|Vytvořit nebo aktualizovat všechny bráně Azure zákazníků Statistika konektor ověřování|
|/hubs/Connectors/Update/Action|Aktualizovat všechny konektor služby Statistika Azure zákazníků|
|/hubs/Connectors/Write|Vytvořit nebo aktualizovat všechny konektor služby Statistika Azure zákazníků|
|/hubs/crmmetadata/Action|Vytvořit nebo aktualizovat veškerá Crm Metadata, Azure zákazníků statistiky|
|/hubs/crmmetadata/Read|Číst veškerá Crm Metadata, Azure zákazníků statistiky|
|/hubs/delete|Odstranit všechny statistiky Centrum Azure zákazníků|
|/hubs/gdpr/delete|Odstranit všechny statistiky Gdpr Azure zákazníků|
|/hubs/gdpr/Read|Číst všechny statistiky Gdpr Azure zákazníků|
|/hubs/gdpr/Write|Vytvořit nebo aktualizovat všechny statistiky Gdpr Azure zákazníků|
|/hubs/getbillingcredits/Read|Získání Azure zákazníků Statistika rozbočovače fakturace kredity|
|/hubs/getbillinghistory/read|Získání historie fakturace rozbočovače Statistika Azure zákazníka|
|/hubs/images/delete|Odstranit všechny Image Statistika Azure zákazníků|
|/hubs/Images/Read|Číst všechny statistiky Image Azure zákazníků|
|/hubs/Images/Write|Vytvořit nebo aktualizovat žádný obrázek Statistika Azure zákazníků|
|/hubs/interactions/DELETE|Odstranit všechny azure interakcí statistiky|
|/hubs/interactions/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika interakce|
|/hubs/interactions/Read|Číst interakce Statistika Azure zákazníků|
|/hubs/interactions/suggestrelationshiplinks/Action|Navrhněte vztah odkazy pro všechny interakce Statistika Azure zákazníků|
|/hubs/interactions/Write|Vytvořit nebo aktualizovat interakce Statistika Azure zákazníků|
|/hubs/kpi/delete|Odstranit všechny Azure zákazníků Statistika klíčového ukazatele výkonu|
|/hubs/KPI/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika klíčové ukazatele výkonu|
|/hubs/kpi/read|Číst všechny Azure zákazníků Statistika klíčového ukazatele výkonu|
|/hubs/kpi/reprocess/action|Znovu zpracovat žádné Azure zákazníků přehled klíčových ukazatelů výkonu|
|/hubs/KPI/Write|Vytvořit nebo aktualizovat všechny Azure zákazníků Statistika klíčového ukazatele výkonu|
|/hubs/links/delete|Odstranit všechny odkazy Statistika Azure zákazníků|
|/hubs/Links/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika odkazy|
|/hubs/links/read|Číst všechny odkazy Statistika Azure zákazníků|
|/hubs/Links/Write|Vytvořit nebo aktualizovat jakékoli odkazy Azure zákazníků|
|/hubs/msemetadata/Action|Vytvořit nebo aktualizovat veškerá Metadata, Mse Statistika Azure zákazníků|
|/hubs/msemetadata/Read|Číst veškerá Metadata, Mse Statistika Azure zákazníků|
|/hubs/operationresults/Read|Získat výsledky operace rozbočovače Statistika Azure zákazníků|
|/hubs/predictions/DELETE|Odstranit všechny statistiky předpovědi Azure zákazníků|
|/hubs/predictions/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika předpovědi|
|/hubs/predictions/Read|Číst všechny statistiky předpovědi Azure zákazníků|
|/hubs/predictions/Write|Vytvořit nebo aktualizovat všechny předpovědi Azure zákazníků|
|/hubs/predictivematchpolicies/delete|Odstranit všechny zásady Azure zákazníků Statistika prediktivní shody|
|/hubs/predictivematchpolicies/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika prediktivní shodu zásad|
|/hubs/predictivematchpolicies/Read|Číst všechny zásady Azure zákazníků Statistika prediktivní shody|
|/hubs/predictivematchpolicies/write|Vytvořit nebo aktualizovat všechny zásady Azure zákazníků Statistika prediktivní shody|
|/hubs/profiles/delete|Odstranit žádný profil Statistika Azure zákazníků|
|/hubs/Profiles/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika profilu|
|/hubs/Profiles/Read|Číst všechny statistiky profil Azure zákazníků|
|/hubs/Profiles/Write|Zápis žádný profil Statistika Azure zákazníků|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/hubs/providers/Microsoft.Insights/logDefinitions/Read|Získá dostupné protokoly pro prostředek|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro prostředek|
|/hubs/Read|Číst všechny statistiky centra Azure zákazníků|
|/hubs/relationshiplinks/DELETE|Odstraňte jakékoli odkazy Statistika vztah Azure zákazníků|
|/hubs/relationshiplinks/Operations/Read|Číst libovolný výsledek operace Azure zákazníka Statistika vztah odkazy|
|/hubs/relationshiplinks/Read|Přečtěte si jakékoli odkazy Statistika vztah Azure zákazníků|
|/hubs/relationshiplinks/Write|Vytvořit nebo aktualizovat jakékoli odkazy Statistika vztah Azure zákazníků|
|/hubs/Relationships/DELETE|Odstranit všechny vztahy Statistika Azure zákazníků|
|/hubs/Relationships/Operations/Read|Číst libovolný výsledek operace Statistika vztahů se zákazníky Azure|
|/hubs/Relationships/Read|Číst všechny vztahy Statistika Azure zákazníků|
|/hubs/Relationships/Write|Vytvořit nebo aktualizovat všechny vztahy Statistika Azure zákazníků|
|/hubs/roleAssignments/delete|Odstranit všechny Azure zákazníků Statistika Rbac přiřazení|
|/hubs/roleAssignments/operations/read|Číst libovolný výsledek operace Azure zákazníka Statistika Rbac přiřazení|
|/hubs/roleAssignments/read|Číst všechny Azure zákazníků Statistika Rbac přiřazení|
|/hubs/roleAssignments/write|Vytvořit nebo aktualizovat všechny Azure zákazníků Statistika Rbac přiřazení|
|/hubs/Roles/Read|Číst všechny role Rbac Statistika Azure zákazníků|
|/hubs/salesforcemetadata/Action|Vytvořit nebo aktualizovat veškerá Metadata, SalesForce Statistika Azure zákazníků|
|/hubs/salesforcemetadata/Read|Číst veškerá Metadata, SalesForce Statistika Azure zákazníků|
|/hubs/segments/delete|Odstranit všechny segmenty Statistika Azure zákazníků|
|/hubs/segments/dynamic/action|Segmentuje žádné Azure zákazníků přehled dynamické správy|
|/hubs/Segments/Read|Číst všechny segmenty Statistika Azure zákazníků|
|/hubs/Segments/static/Action|Segmentuje všechny statické Azure zákazníků přehled správy|
|/hubs/segments/write|Vytvořit nebo aktualizovat všechny segmenty Statistika Azure zákazníků|
|/hubs/sqlconnectionstrings/delete|Odstranit všechny statistiky SqlConnectionStrings Azure zákazníků|
|/hubs/sqlconnectionstrings/read|Číst všechny statistiky SqlConnectionStrings Azure zákazníků|
|/hubs/sqlconnectionstrings/write|Vytvořit nebo aktualizovat všechny statistiky SqlConnectionStrings Azure zákazníků|
|/hubs/suggesttypeschema/Action|Generování navrhnout typ schématu z ukázkových dat|
|/hubs/tenantmanagement/read|Spravovat nastavení centra zákazníka Statistika Azure|
|/hubs/views/DELETE|Odstranit všechna zobrazení statistiky aplikace Azure zákazníků|
|/hubs/views/Read|Přečtěte si všechna zobrazení statistiky aplikace Azure zákazníků|
|/hubs/views/Write|Vytvořit nebo aktualizovat všechna zobrazení statistiky aplikace Azure zákazníků|
|/hubs/widgettypes/Read|Číst všechny typy pomůcky App Insights Azure zákazníků|
|/ hubs a zápis|Vytvořit nebo aktualizovat všechny statistiky centra Azure zákazníků|
|/Operations/Read|Přečtěte si Azure zákazníků Insights Api Metadatas|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků Statistika zákazníka a umožňuje vytváření prostředků Statistika zákazníka|
|nebo zrušit registraci nebo akce|Zrušení registrace předplatného pro poskytovatele prostředků Statistika zákazníka|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operace | Popis |
|---|---|
|/catalogs/DELETE|Odstraní katalogu.|
|/catalogs/Read|Načíst vlastnosti pro katalogu nebo katalogů pod předplatné nebo skupinu prostředků.|
|/ katalogů a zápis|Katalog vytvoří nebo aktualizuje značky a vlastnosti pro katalog.|
|/checkNameAvailability/action|Zkontroluje dostupnost názvu katalogu pro klienta.|
|/Operations/Read|Seznam operací dostupných na zprostředkovateli prostředků Microsoft.DataCatalog.|
|/ registrace nebo akce|Registruje zprostředkovatele prostředků Microsoft.DataCatalog předplatné.|
|nebo zrušit registraci nebo akce|Zruší registraci předplatného od zprostředkovatele prostředků Microsoft.DataCatalog.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operace | Popis |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro datafactories|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Získá dostupné protokoly pro objekty Factory|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro objekty Factory|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operace | Popis |
|---|---|
|/Accounts/computePolicies/DELETE|Odstraníte zásadu výpočty.|
|/Accounts/computePolicies/Read|Získáte informace o výpočetní zásad.|
|/Accounts/computePolicies/Write|Vytvořit nebo aktualizovat zásadu výpočty.|
|/accounts/dataLakeStoreAccounts/delete|Zrušit propojení účtu DataLakeStore z DataLakeAnalytics účtu.|
|/accounts/dataLakeStoreAccounts/read|Získání informací o propojeném účtu DataLakeStore DataLakeAnalytics účtu.|
|/accounts/dataLakeStoreAccounts/write|Vytvořit nebo aktualizovat účet propojené DataLakeStore DataLakeAnalytics účtu.|
|/Accounts/DELETE|Odstranění účtu DataLakeAnalytics.|
|/accounts/firewallRules/delete|Odstraňte pravidlo brány firewall.|
|/accounts/firewallRules/read|Získání informací o pravidlo brány firewall.|
|/accounts/firewallRules/write|Vytvořit nebo aktualizovat pravidlo brány firewall.|
|/Accounts/operationResults/Read|Získání výsledku operace DataLakeAnalytics účtu.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Získáte nastavení diagnostiky pro DataLakeAnalytics účet.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Vytvořit nebo aktualizovat nastavení diagnostiky pro DataLakeAnalytics účet.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Získáte dostupné protokoly pro účet DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Získáte dostupné metriky pro účet DataLakeAnalytics.|
|/Accounts/Read|Získání informací o stávající účet DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Zobrazí seznam tokeny SAS pro kontejnery úložiště DataLakeAnalytics účtu propojeného účtu úložiště.|
|/Accounts/storageAccounts/Containers/Read|Získáte kontejnery DataLakeAnalytics účtu propojeného účtu úložiště.|
|/accounts/storageAccounts/delete|Zrušit propojení účtu úložiště z účtu DataLakeAnalytics.|
|/Accounts/storageAccounts/Read|Získání informací o propojený účet úložiště DataLakeAnalytics účtu.|
|/Accounts/storageAccounts/Write|Vytvořit nebo aktualizovat účet úložiště propojené DataLakeAnalytics účtu.|
|/Accounts/TakeOwnerShip/Action|Udělení oprávnění k zrušení úlohy, odeslané jinými uživateli.|
|/ účty a zápis|Vytvořit nebo aktualizovat účet DataLakeAnalytics.|
|/Locations/capability/Read|Získání informací o odběru týkající se používání DataLakeAnalytics.|
|/locations/checkNameAvailability/action|Zkontrolujte dostupnost názvu účtu DataLakeAnalytics.|
|/Locations/operationResults/Read|Získání výsledku operace DataLakeAnalytics účtu.|
|/Operations/Read|Získáte dostupné operace DataLakeAnalytics.|
|/ registrace nebo akce|Zaregistrujte předplatné DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operace | Popis |
|---|---|
|/Accounts/DELETE|Odstranění účtu DataLakeStore.|
|/accounts/enableKeyVault/action|Povolte KeyVault DataLakeStore účet.|
|/accounts/firewallRules/delete|Odstraňte pravidlo brány firewall.|
|/accounts/firewallRules/read|Získání informací o pravidlo brány firewall.|
|/accounts/firewallRules/write|Vytvořit nebo aktualizovat pravidlo brány firewall.|
|/Accounts/operationResults/Read|Získání výsledku operace DataLakeStore účtu.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Získáte nastavení diagnostiky pro DataLakeStore účet.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Vytvořit nebo aktualizovat nastavení diagnostiky pro DataLakeStore účet.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Získáte dostupné protokoly pro účet DataLakeStore.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Získáte dostupné metriky pro účet DataLakeStore.|
|/Accounts/Read|Získání informací o stávající účet DataLakeStore.|
|/Accounts/superuser/Action|Udělit superuživatel v Data Lake Store s Microsoft.Authorization/roleAssignments/write udělení.|
|/accounts/trustedIdProviders/delete|Odstraňte zprostředkovatele důvěryhodné identity.|
|/accounts/trustedIdProviders/read|Získáte informace o zprostředkovateli důvěryhodné identity.|
|/accounts/trustedIdProviders/write|Vytvořit nebo aktualizovat zprostředkovatele důvěryhodné identity.|
|/ účty a zápis|Vytvořit nebo aktualizovat účet DataLakeStore.|
|/Locations/capability/Read|Získání informací o odběru týkající se používání DataLakeStore.|
|/locations/checkNameAvailability/action|Zkontrolujte dostupnost názvu účtu DataLakeStore.|
|/Locations/operationResults/Read|Získání výsledku operace DataLakeStore účtu.|
|/Operations/Read|Získáte dostupné operace DataLakeStore.|
|/ registrace nebo akce|Zaregistrujte předplatné DataLakeStore.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Operace | Popis |
|---|---|
|/locations/performanceTiers/read|Vrátí seznam úrovně výkonu, které jsou k dispozici.|
|/performanceTiers/read|Vrátí seznam úrovně výkonu, které jsou k dispozici.|
|/Servers/DELETE|Odstraní existující server.|
|/servers/firewallRules/delete|Odstraní existující pravidlo brány firewall.|
|/servers/firewallRules/read|Vrátí seznam brány firewall pravidla pro server nebo získá vlastnostech pro pravidlo brány firewall zadaný.|
|/servers/firewallRules/write|Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci existujícího pravidla.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Získá disagnostic nastavení pro prostředek.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Návratové typy metriky, které jsou k dispozici pro databáze|
|/Servers/Read|Vrátí seznam serverů nebo získá vlastnosti pro zadaný server.|
|/Servers/recoverableServers/Read|Vrátí informace o obnovitelných MySQL serveru|
|/servers/virtualNetworkRules/delete|Odstraní existující pravidlo virtuální sítě|
|/servers/virtualNetworkRules/read|Vrátí seznam virtuální sítě pravidla nebo získá vlastnosti pro pravidlo zadané virtuální síti.|
|/servers/virtualNetworkRules/write|Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální síti.|
|/ servery a zápis|Vytvoří server, se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Operace | Popis |
|---|---|
|/locations/performanceTiers/read|Vrátí seznam úrovně výkonu, které jsou k dispozici.|
|/performanceTiers/read|Vrátí seznam úrovně výkonu, které jsou k dispozici.|
|/Servers/DELETE|Odstraní existující server.|
|/servers/firewallRules/delete|Odstraní existující pravidlo brány firewall.|
|/servers/firewallRules/read|Vrátí seznam brány firewall pravidla pro server nebo získá vlastnostech pro pravidlo brány firewall zadaný.|
|/servers/firewallRules/write|Vytvoří pravidlo brány firewall se zadanými parametry nebo aktualizaci existujícího pravidla.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Získá disagnostic nastavení pro prostředek.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/Servers/providers/Microsoft.Insights/logDefinitions/Read|Návratové typy protokolů, které jsou k dispozici pro databáze|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Návratové typy metriky, které jsou k dispozici pro databáze|
|/Servers/Read|Vrátí seznam serverů nebo získá vlastnosti pro zadaný server.|
|/Servers/recoverableServers/Read|Vrátí informace o obnovitelných PostgreSQL serveru|
|/servers/virtualNetworkRules/delete|Odstraní existující pravidlo virtuální sítě|
|/servers/virtualNetworkRules/read|Vrátí seznam virtuální sítě pravidla nebo získá vlastnosti pro pravidlo zadané virtuální síti.|
|/servers/virtualNetworkRules/write|Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální síti.|
|/ servery a zápis|Vytvoří server, se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operace | Popis |
|---|---|
|/checkNameAvailability/Action|Zkontrolujte, zda je název IotHub-li k dispozici|
|/checkProvisioningServiceNameAvailability/Action|Zkontrolujte, zda je název IotHub-li k dispozici|
|/ElasticPools/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/ElasticPools/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/elasticPools/iotHubTenants/Delete|Odstranit prostředek klienta IotHub|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Odstranění skupiny příjemců EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Získat EventHub příjemce skupiny|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Vytvoření skupiny příjemců EventHub|
|/elasticPools/iotHubTenants/exportDevices/Action|Export zařízení|
|/elasticPools/iotHubTenants/getStats/Read|Získá klienta IotHub statistiky prostředků|
|/elasticPools/iotHubTenants/importDevices/Action|Import zařízení|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Získá klíč tenanta IotHub|
|/elasticPools/iotHubTenants/jobs/Read|Získání úloh Podrobnosti odeslaného na daný IotHub|
|/elasticPools/iotHubTenants/listKeys/Action|Získá klíče klienta IotHub|
|/ElasticPools/IotHubTenants/logDefinitions/read|Získá definice dostupné protokolu pro službu IotHub|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Načte dostupné metriky pro službu IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Získat metriky kvóty|
|/elasticPools/iotHubTenants/Read|Získá prostředek klienta IotHub|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Testovací zpráva proti všechny existující trasy|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Testovací zpráva proti testu zadané trasy|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Získá stav všech směrování koncové body pro platformou IotHub|
|/elasticPools/iotHubTenants/Write|Vytvořit nebo aktualizovat prostředek klienta IotHub|
|/ElasticPools/metricDefinitions/read|Načte dostupné metriky pro službu IotHub|
|/iotHubs/Certificates/generateVerificationCode/Action|Generovat ověřovací kód|
|/iotHubs/Certificates/Verify/Action|Ověření certifikátu prostředků|
|/iotHubs/Delete|Odstranit prostředek IotHub|
|/IotHubs/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/IotHubs/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/iotHubs/eventGridFilters/DELETE|Odstraní filtr událostí mřížky|
|/iotHubs/eventGridFilters/Read|Získá filtr událostí mřížky|
|/iotHubs/eventGridFilters/Write|Vytvořit nový nebo aktualizovat existující filtr událostí mřížky|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Odstranění skupiny příjemců EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Získat EventHub příjemce skupiny|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Vytvoření skupiny příjemců EventHub|
|/iotHubs/exportDevices/Action|Export zařízení|
|/iotHubs/importDevices/Action|Import zařízení|
|/iotHubs/iotHubKeys/listkeys/Action|Získat klíč IotHub pro daným názvem.|
|/iotHubs/iotHubStats/Read|Získat statistiku IotHub|
|/iotHubs/Jobs/Read|Získání úloh Podrobnosti odeslaného na daný IotHub|
|/iotHubs/listkeys/Action|Získat všechny klíče IotHub|
|/ IotHubs/logDefinitions/čtení|Získá definice dostupné protokolu pro službu IotHub|
|/ IotHubs/metricDefinitions/čtení|Načte dostupné metriky pro službu IotHub|
|/iotHubs/quotaMetrics/Read|Získat metriky kvóty|
|/ iotHubs/čtení|Získá prostředky IotHub|
|/iotHubs/Routing/$ testall nebo akce|Testovací zpráva proti všechny existující trasy|
|/iotHubs/Routing/$ testnew nebo akce|Testovací zpráva proti testu zadané trasy|
|/iotHubs/routingEndpointsHealth/Read|Získá stav všech směrování koncové body pro platformou IotHub|
|/iotHubs/skus/Read|Získání platných SKU IotHub|
|/ iotHubs/zápisu|Vytvořit nebo aktualizovat prostředek IotHub|
|/ operací/čtení|Získat všechny operace ResourceProvider|
|/provisioningServices/certificates/generateVerificationCode/Action|Generovat ověřovací kód|
|/provisioningServices/Certificates/Verify/Action|Ověření certifikátu prostředků|
|/provisioningServices/Delete|Odstranit prostředek IotDps|
|/provisioningServices/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/provisioningServices/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/provisioningServices/listkeys/Action|Získání všech IotDps klíčů|
|/provisioningServices/logDefinitions/Read|Získá definice dostupné protokolu pro službu zřizování|
|/provisioningServices/metricDefinitions/read|Načte dostupné metriky pro službu zřizování|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Získání IotDps klíčů pro název klíče|
|/provisioningServices/Read|Získat prostředek IotDps|
|/provisioningServices/skus/Read|Získání platných SKU IotDps|
|/provisioningServices/Write|Vytvořte prostředek IotDps|
|/ registrace nebo akce|Registrace předplatného pro poskytovatele prostředků IotHub a umožňuje vytváření prostředků IotHub|
|/ registrace nebo akce|Registrace předplatného pro poskytovatele prostředků IotHub a umožňuje vytváření prostředků IotHub|
|/ použití nebo pro čtení|Získáte podrobnosti o použití předplatné pro tohoto zprostředkovatele.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operace | Popis |
|---|---|
|/labCenters/delete|Odstraňte centrech testovacího prostředí.|
|/labCenters/read|Přečtěte si centrech testovacího prostředí.|
|/labCenters/write|Přidat nebo změnit centrech testovacího prostředí.|
|/labs/artifactSources/armTemplates/read|Přečtěte si šablony správce prostředků azure.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Generuje šablony ARM pro danou artefaktů, odešle požadovaných souborů do účtu úložiště a ověří generovaného artefaktů.|
|/labs/artifactSources/artifacts/read|Přečtěte si artefakty.|
|/labs/artifactSources/delete|Odstraňte artefaktů zdroje.|
|/labs/artifactSources/read|Přečtěte si artefaktů zdroje.|
|/labs/artifactSources/write|Přidat nebo změnit artefaktů zdroje.|
|/labs/ClaimAnyVm/action|Deklarace identity náhodných vymahatelných virtuálního počítače v testovacím prostředí.|
|/labs/costs/read|Přečtěte si náklady.|
|/Labs/costs/Write|Přidat nebo změnit náklady.|
|/Labs/CreateEnvironment/Action|Vytvoření virtuálních počítačů v testovacím prostředí.|
|/labs/customImages/delete|Odstraňte vlastní Image.|
|/labs/customImages/read|Přečtěte si vlastních bitových kopií.|
|/labs/customImages/write|Přidat nebo upravit vlastní Image.|
|/Labs/DELETE|Odstraňte laboratoře.|
|/labs/ExportResourceUsage/action|Exportuje využití prostředků testovacího prostředí do účtu úložiště|
|/Labs/Formulas/DELETE|Odstraňte vzorce.|
|/Labs/Formulas/Read|Přečtěte si vzorce.|
|/Labs/Formulas/Write|Přidat nebo upravit vzorce.|
|/labs/galleryImages/read|Přečtěte si Galerie obrázků.|
|/labs/GenerateUploadUri/action|Generovat identifikátor URI pro nahrávání vlastní diskové Image do testovacího prostředí.|
|/Labs/ImportVirtualMachine/Action|Virtuální počítač importujte do jiné testovacím prostředí.|
|/Labs/ListVhds/Action|Zobrazí seznam bitových kopií disku k dispozici pro vytvoření vlastní image.|
|/Labs/notificationChannels/DELETE|Odstraňte notificationchannels.|
|/Labs/notificationChannels/Notify/Action|Odeslat oznámení na zadané kanálu.|
|/labs/notificationChannels/read|Přečtěte si notificationchannels.|
|/Labs/notificationChannels/Write|Přidat nebo změnit notificationchannels.|
|/Labs/policySets/EvaluatePolicies/Action|Vyhodnotí zásady testovacího prostředí.|
|/Labs/policySets/Policies/DELETE|Odstraňte zásady.|
|/Labs/policySets/Policies/Read|Přečtěte si zásady.|
|/Labs/policySets/Policies/Write|Přidat nebo upravit zásady.|
|/Labs/Read|Přečtěte si laboratoře.|
|/Labs/Schedules/DELETE|Odstraňte plány.|
|/Labs/Schedules/Execute/Action|Spuštění plánu.|
|/Labs/Schedules/ListApplicable/Action|Zobrazí všechny příslušné plány|
|/Labs/Schedules/Read|Plánuje pro čtení.|
|/Labs/Schedules/Write|Přidat nebo změnit plány.|
|/labs/serviceRunners/delete|Odstraňte závodníky služby.|
|/labs/serviceRunners/read|Přečtěte si závodníky služby.|
|/labs/serviceRunners/write|Přidat nebo změnit závodníky služby.|
|/Labs/Users/DELETE|Odstraňte profily uživatelů.|
|/Labs/Users/Disks/Attach/Action|Připojit a vytvořit zapůjčení disku k virtuálnímu počítači.|
|/Labs/Users/Disks/DELETE|Odstraňte disky.|
|/Labs/Users/Disks/Detach/Action|Odpojte a rozdělit zapůjčení disk připojený k virtuálnímu počítači.|
|/Labs/Users/Disks/Read|Přečtěte si disky.|
|/Labs/Users/Disks/Write|Přidat nebo změnit disky.|
|/Labs/Users/Environments/DELETE|Odstraňte prostředí.|
|/Labs/Users/Environments/Read|Přečtěte si prostředí.|
|/Labs/Users/Environments/Write|Přidat nebo změnit prostředí.|
|/Labs/Users/Read|Číst profily uživatelů.|
|/Labs/Users/secrets/DELETE|Odstraňte tajných klíčů.|
|/Labs/Users/secrets/Read|Čtení tajných klíčů.|
|/Labs/Users/secrets/Write|Přidat nebo změnit tajných klíčů.|
|/labs/users/serviceFabrics/delete|Odstraňte service Fabric.|
|/Labs/Users/serviceFabrics/ListApplicableSchedules/Action|Zobrazí všechny příslušné plány|
|/labs/users/serviceFabrics/read|Prostředky infrastruktury služby pro čtení.|
|/labs/users/serviceFabrics/schedules/delete|Odstraňte plány.|
|/labs/users/serviceFabrics/schedules/Execute/action|Spuštění plánu.|
|/labs/users/serviceFabrics/schedules/read|Plánuje pro čtení.|
|/labs/users/serviceFabrics/schedules/write|Přidat nebo změnit plány.|
|/labs/users/serviceFabrics/Start/action|Spusťte service fabric.|
|/labs/users/serviceFabrics/Stop/action|Zastavit service fabric|
|/labs/users/serviceFabrics/write|Přidat nebo změnit service Fabric.|
|/Labs/Users/Write|Přidat nebo upravit profily uživatelů.|
|/labs/virtualMachines/AddDataDisk/action|Připojte nové nebo stávající datový disk k virtuálnímu počítači.|
|/Labs/virtualMachines/ApplyArtifacts/Action|Artefakty se vztahují na virtuální počítač.|
|/Labs/virtualMachines/Claim/Action|Převzetí vlastnictví existujícího virtuálního počítače|
|/Labs/virtualMachines/DELETE|Odstraňte virtuální počítače.|
|/labs/virtualMachines/DetachDataDisk/action|Zadaný disk z virtuálního počítače odpojte.|
|/Labs/virtualMachines/ListApplicableSchedules/Action|Zobrazí všechny příslušné plány|
|/labs/virtualMachines/read|Přečtěte si virtuálních počítačů.|
|/Labs/virtualMachines/restart/Action|Restartování virtuálního počítače.|
|/Labs/virtualMachines/Schedules/DELETE|Odstraňte plány.|
|/Labs/virtualMachines/Schedules/Execute/Action|Spuštění plánu.|
|/Labs/virtualMachines/Schedules/Read|Plánuje pro čtení.|
|/Labs/virtualMachines/Schedules/Write|Přidat nebo změnit plány.|
|/Labs/virtualMachines/Start/Action|Spuštění virtuálního počítače.|
|/Labs/virtualMachines/stop/Action|Zastavit virtuální počítač|
|/Labs/virtualMachines/TransferDisks/Action|Převést vlastnictví datových disků virtuálního počítače na sami|
|/Labs/virtualMachines/UnClaim/Action|Verze vlastnictví existujícího virtuálního počítače|
|/Labs/virtualMachines/Write|Umožňuje přidat nebo upravit virtuální počítače.|
|/labs/virtualNetworks/delete|Odstraňte virtuální sítě.|
|/labs/virtualNetworks/read|Čtení virtuální sítě.|
|/labs/virtualNetworks/write|Přidat nebo upravit virtuální sítě.|
|/ labs a zápis|Přidat nebo změnit laboratoře.|
|/Locations/Operations/Read|Operace čtení.|
|/ registrace nebo akce|Zaregistruje předplatné|
|/Schedules/DELETE|Odstraňte plány.|
|/Schedules/Execute/Action|Spuštění plánu.|
|/Schedules/Read|Plánuje pro čtení.|
|/Schedules/Retarget/Action|Aktualizace plán cílový prostředek ID.|
|/ plány a zápis|Přidat nebo změnit plány.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operace | Popis |
|---|---|
|/databaseAccountNames/read|Zkontroluje dostupnost názvu.|
|/databaseAccounts/changeResourceGroup/action|Změnit skupinu prostředků účtu databáze|
|/databaseAccounts/databases/collections/metricDefinitions/read|Načte kolekci definice metrik.|
|/databaseAccounts/Databases/Collections/Metrics/Read|Načte kolekci metriky.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Čtení databáze účet oddílu klíčové metriky na úrovni|
|/databaseAccounts/databases/collections/partitions/metrics/read|Číst databáze účet oddílu úrovně metriky|
|/databaseAccounts/databases/collections/partitions/usages/read|Číst účet databáze úrovně použití oddílu|
|/databaseAccounts/Databases/Collections/usages/Read|Načte kolekci použití.|
|/databaseAccounts/databases/metricDefinitions/read|Čte databázi definice metrik|
|/databaseAccounts/Databases/Metrics/Read|Přečte metriky databáze.|
|/databaseAccounts/Databases/usages/Read|Přečte použití databáze.|
|/databaseAccounts/DELETE|Odstraní účty databáze.|
|/databaseAccounts/failoverPriorityChange/action|Změnit priorit převzetí služeb při selhání oblastí databázového účtu. To se používá k provedení operace ruční převzetí služeb při selhání|
|/databaseAccounts/listConnectionStrings/action|Získat připojovací řetězce pro databázový účet|
|/databaseAccounts/listKeys/action|Seznam klíčů účtu databáze|
|/databaseAccounts/metricDefinitions/read|Čte databázi definice metrik účtu.|
|/databaseAccounts/Metrics/Read|Přečte metriky účtu databáze.|
|/databaseAccounts/operationResults/read|Stav asynchronní operace čtení|
|/databaseAccounts/percentile/metrics/read|Metriky latenci pro čtení|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Číst metriky latence pro konkrétní oblasti zdroje a cíle|
|/databaseAccounts/percentile/targetRegion/metrics/read|Číst metriky latence pro konkrétní cíl oblast|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Získá catageries dostupné protokolů pro účet databáze|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro databázi účtu|
|/databaseAccounts/read|Přečte databázového účtu.|
|/databaseAccounts/readonlykeys/action|Čte databázi, klíče účtu jen pro čtení.|
|/databaseAccounts/readonlykeys/read|Čte databázi, klíče účtu jen pro čtení.|
|/databaseAccounts/regenerateKey/action|Otočit klíče účtu databáze|
|/databaseAccounts/region/Databases/Collections/Metrics/Read|Přečte metriky místní kolekce.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Přečtěte si místní databáze účet oddílu klíčové metriky na úrovni|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Číst metriky úrovně oddílu v účtu místní databáze|
|/databaseAccounts/region/databases/collections/partitions/read|Číst účet oddíly databáze v kolekci|
|/databaseAccounts/region/Metrics/Read|Přečte účet metriky oblast a databáze.|
|/databaseAccounts/usages/read|Čte databázi použití účtu.|
|/ databaseAccounts/zápisu|Aktualizace databáze účtů.|
|/locations/deleteVirtualNetworkOrSubnets/action|Microsoft.DocumentDB upozorní, že se teď odstraňuje virtuální síť nebo podsíť|
|/operationResults/Read|Stav asynchronní operace čtení|
|/Operations/Read|Operace čtení documentdb Microsoft |
|/ registrace nebo akce| Registrace poskytovatele prostředků Microsoft DocumentDB pro předplatné|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operace | Popis |
|---|---|
|/checkDomainAvailability/Action|Zkontrolujte, jestli je možné zakoupit domény|
|/domains/Delete|Odstraňte existující domény.|
|/Domains/domainownershipidentifiers/DELETE|Odstranit identifikátor vlastnictví|
|/Domains/domainownershipidentifiers/Read|Seznam identifikátorů vlastnictví|
|/Domains/domainownershipidentifiers/Read|Získat identifikátor vlastnictví|
|/Domains/domainownershipidentifiers/Write|Vytvořit nebo aktualizovat identifikátor|
|/Domains/operationresults/Read|Získat operace domény|
|/Domains/Operations/Read|Zobrazí seznam všech operací z registraci domény aplikace služby|
|/ domény/čtení|Získat seznam domén|
|/ domény/čtení|Získat domény|
|/Domains/renew/Action|Obnovte existující domény.|
|/ domény/zápisu|Přidat novou doménu nebo aktualizovat stávající|
|/generateSsoRequest/Action|Generovat žádost o přihlášení k doméně řídicí centrum.|
|/listDomainRecommendations/Action|Načtení seznamu domény doporučení na základě klíčových slov|
|/ registrace nebo akce|Registrace poskytovatele prostředků Microsoft Domains pro předplatné|
|/topLevelDomains/listAgreements/Action|Seznam smlouvu akce|
|/topLevelDomains/Read|Získat toplevel domény|
|/topLevelDomains/Read|Získat toplevel domény|
|/validateDomainRegistrationInformation/Action|Ověření objektu nákupu domény bez odeslání|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operace | Popis |
|---|---|
|/lcsprojects/clouddeployments/Read|V projektu služby Microsoft Dynamics životního cyklu, které patří uživateli zobrazit Microsoft Dynamics AX 2012 R3 vyhodnocení nasazení|
|/lcsprojects/clouddeployments/Write|Vytvořte Microsoft Dynamics AX 2012 R3 vyhodnocení nasazení v projektu služby Microsoft Dynamics životního cyklu, které patří uživateli. Nasazení lze provádět z portálu správy Azure|
|/lcsprojects/Connectors/Read|Konektory pro čtení, které patří do služby Microsoft Dynamics životního cyklu projektu|
|/lcsprojects/Connectors/Write|Vytváření a aktualizaci konektory, které patří do služby Microsoft Dynamics životního cyklu projektu|
|/lcsprojects/delete|Odstranění služby životního cyklu aplikace Microsoft Dynamics projekty, které patří uživateli|
|/lcsprojects/read|Zobrazení služby životního cyklu aplikace Microsoft Dynamics projektů, které patří uživateli|
|/ lcsprojects/zápisu|Vytvářet a aktualizovat projekty služby Microsoft Dynamics životního cyklu, které patří uživateli. Lze aktualizovat pouze vlastnosti název a popis. Po vytvoření nelze aktualizovat předplatné a umístění přidružený k projektu|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Operace | Popis |
|---|---|
|/eventSubscriptions/DELETE|Odstranit eventSubscription|
|/eventSubscriptions/getFullUrl/action|Získat úplnou adresu url odběru událostí|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro odběry událostí|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro odběry událostí|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro eventSubscriptions|
|/eventSubscriptions/Read|Čtení eventSubscription|
|/ eventSubscriptions/zápisu|Vytvořit nebo aktualizovat eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro témata|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro témata|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro témata|
|/ registrace nebo akce|Zaregistruje eventSubscription pro zprostředkovatele prostředků EventGrid a povolí vytvoření odběrů událostí mřížky.|
|/topics/DELETE|Odstranit téma|
|/topics/listKeys/action|Seznam klíčů pro téma|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro témata|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro témata|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro témata|
|/topics/Read|Přečtěte si téma|
|/topics/regenerateKey/action|Znovu generovat klíč pro téma|
|/ témata a zápis|Vytvořit nebo aktualizovat téma|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operace | Popis |
|---|---|
|/checkNameAvailability/action|Zkontroluje dostupnost oboru názvů v daném předplatném.|
|/checkNamespaceAvailability/action|Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. místo toho použijte CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/Action|Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je depricated. Se aktualizovat autorizační pravidlo Namespace místo toho použijte volání PUT... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/authorizationRules/delete|Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit. |
|/namespaces/authorizationRules/listkeys/action|Získat připojovací řetězec k oboru názvů|
|/namespaces/authorizationRules/read|Získá seznam popisů autorizačních pravidel oboru názvů.|
|/namespaces/authorizationRules/regenerateKeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/authorizationRules/Write|Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat.|
|/namespaces/Delete|Odstraní prostředek oboru názvů.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Získá povolení pravidla klíče pro zotavení po havárii primární obor názvů|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Získání po havárii obnovení primární Namespace pro autorizační pravidla|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Zkontroluje dostupnost alias oboru názvů v rámci daného předplatného.|
|/namespaces/disasterRecoveryConfigs/delete|Odstraní konfigurace zotavení po havárii přidružené k oboru názvů. Tato operace může volat jenom přes primární obor názvů.|
|/namespaces/disasterRecoveryConfigs/failover/action|Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor.|
|/namespaces/disasterRecoveryConfigs/read|Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů.|
|/namespaces/disasterRecoveryConfigs/write|Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů.|
|/namespaces/eventhubs/authorizationRules/action|Operace k aktualizaci EventHub. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo.|
|/namespaces/eventhubs/authorizationRules/delete|Operace odstranění EventHub autorizační pravidla|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Získat připojovací řetězec k centru EventHub|
|/namespaces/eventhubs/authorizationRules/read| Získání seznamu EventHub autorizační pravidla|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/eventhubs/authorizationRules/write|Centrum EventHub autorizační pravidla vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat.|
|/namespaces/eventHubs/consumergroups/Delete|Operace odstranění ConsumerGroup prostředků|
|/namespaces/eventHubs/consumergroups/read|Získání seznamu popisů ConsumerGroup prostředků|
|/namespaces/eventHubs/consumergroups/write|Vytvoření nebo aktualizace ConsumerGroup vlastnosti.|
|/namespaces/eventhubs/Delete|Operace odstranění EventHub prostředků|
|/namespaces/eventhubs/Read|Získání seznamu popisů EventHub prostředků|
|/namespaces/eventhubs/write|Vytvoření nebo aktualizace EventHub vlastnosti.|
|/namespaces/messagingPlan/Read|Získá plánování zasílání zpráv pro obor názvů. Toto rozhraní API je zastaralá. Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/messagingPlan/Write|Aktualizace, zasílání zpráv plánování pro obor názvů. Toto rozhraní API je zastaralá. Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/operationresults/Read|Získá stav operace Namespace.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Získání seznamu protokolů Namespace popisy prostředků|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Získání seznamu Namespace metrik popisy prostředků|
|/namespaces/read|Získá seznam popisů prostředku oboru názvů.|
|/ obory názvů a zápis|Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat.|
|/Operations/Read|Získejte operace|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků EventHub a povolí vytváření prostředků EventHub.|
|/sku/read|Získání seznamu popisů Sku prostředků|
|/SKU/Regions/Read|Získání seznamu popisů SkuRegions prostředků|
|nebo zrušit registraci nebo akce|Zaregistrovat poskytovatele prostředků EventHub|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operace | Popis |
|---|---|
|/features/Read|Získá funkce předplatného.|
|/providers/features/Read|Získá funkci předplatného v daném zprostředkovateli prostředků.|
|/providers/features/Register/Action|Registruje funkci pro předplatné v daném zprostředkovateli prostředků.|
|/providers/features/unregister/Action|Zruší registraci funkce pro předplatné v daném poskytovateli prostředků.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operace | Popis |
|---|---|
|/Clusters/changerdpsetting/Action|Změna nastavení protokolu RDP pro HDInsight Cluster|
|/Clusters/Configurations/Action|Aktualizace konfigurace clusteru HDInsight|
|/Clusters/Configurations/Read|Získání konfigurace clusteru HDInsight|
|/Clusters/DELETE|Odstranění clusteru HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro prostředek clusteru HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro prostředek clusteru HDInsight|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro HDInsight Cluster|
|/Clusters/Read|Získejte podrobnosti o clusteru HDInsight|
|/Clusters/Roles/Resize/Action|Změnit velikost clusteru HDInsight|
|/ clustery a zápis|Vytvořit nebo aktualizovat HDInsight Cluster|
|/Locations/Capabilities/Read|Získat možnosti předplatného|
|/locations/checkNameAvailability/read|Zkontrolovat dostupnost názvu|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operace | Popis |
|---|---|
|/Jobs/DELETE|Odstraní stávající úloze.|
|/jobs/listBitLockerKeys/action|Získá klíče nástroje BitLocker pro zadanou úlohu.|
|/Jobs/Read|Získá vlastnosti pro zadanou úlohu nebo vrátí seznam úloh.|
|/ úlohy/zápisu|Vytvoří úlohu se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadanou úlohu.|
|/Locations/Read|Získá vlastnosti pro zadané umístění nebo vrátí seznam umístění.|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků importu a exportu a umožňuje vytvoření úlohy importu a exportu.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operace | Popis |
|---|---|
|/ActionGroups/Delete|Odstranění skupiny akcí|
|/ActionGroups/Read|Načtení skupiny akcí|
|/ActionGroups/Write|Zápis skupiny akcí|
|/ ActivityLogAlerts nebo aktivovat nebo akce|Aktivovalo se upozornění protokolu aktivit.|
|ActivityLogAlerts nebo odstranění|Odstranění upozornění protokolu aktivit|
|/ ActivityLogAlerts/čtení|Načtení upozornění protokolu aktivit|
|/ ActivityLogAlerts/zápis|Načtení upozornění protokolu aktivit|
|/ AlertRules nebo aktivovat nebo akce|Pravidlo výstrahy je aktivované.|
|/AlertRules/Delete|Odstraňuje se konfigurace pravidla výstrahy.|
|/ AlertRules/incidenty/čtení|Čte se konfigurace incidentu pravidla výstrahy.|
|/AlertRules/Read|Čte se konfigurace pravidla výstrahy.|
|/ AlertRules/vyřešit nebo akce|Pravidlo výstrahy je vyřešené.|
|/ AlertRules/omezeny nebo akce|Pravidlo výstrahy je omezené.|
|/AlertRules/Write|Zapisuje se do konfigurace pravidla výstrahy.|
|/AutoscaleSettings/Delete|Odstraňuje se konfigurace nastavení automatického škálování.|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Číst definice metrik|
|/ AutoscaleSettings/čtení|Čte se konfigurace nastavení automatického škálování.|
|/ AutoscaleSettings/Scaledown nebo akce|Operace automatického vertikálního snížení kapacity|
|/AutoscaleSettings/Scaleup/Action|Operace automatického vertikálního navýšení kapacity|
|/ AutoscaleSettings/zápis|Zapisuje se do konfigurace nastavení automatického škálování.|
|Součásti, AnalyticsItems nebo odstranění|Odstranění položky analytics Application Insights|
|/ Součásti nebo AnalyticsItems/čtení|Čtení položky analytics Application Insights|
|/Components/AnalyticsItems/Write|Zápis položku analytics Application Insights|
|/ Součásti nebo AnalyticsTables nebo akce|Analýza aplikace statistiky tabulky akce|
|Součásti, AnalyticsTables nebo odstranění|Odstranění Application Insights analytics tabulky schématu|
|/ Součásti nebo AnalyticsTables/čtení|Čtení Application Insights analytics tabulky schématu|
|A součásti nebo AnalyticsTables/zápis|Zápis Application Insights analytics tabulky schématu|
|Součásti, poznámky nebo odstranění|Odstranění poznámky Application Insights|
|/ Součásti nebo poznámky/čtení|Čtení anotaci Application Insights|
|A součásti nebo poznámky/zápis|Zápis poznámky Application Insights|
|/ Součásti/Api/čtení|Čtení dat součásti Application Insights API|
|/Components/ApiKeys/Action|Generování klíčem rozhraní API pro přehledy aplikace|
|/Components/ApiKeys/Delete|Odstranění Application Insights API klíče|
|/Components/ApiKeys/Read|Čtení klíčem rozhraní API pro přehledy aplikace|
|/Components/BillingPlanForComponent/Read|Čtení plán fakturace pro součást Application Insights|
|/Components/CurrentBillingFeatures/Read|Čtení aktuální fakturace funkce pro součást Application Insights|
|/Components/CurrentBillingFeatures/Write|Zápis aktuální fakturace funkce pro součást Application Insights|
|/Components/DefaultWorkItemConfig/Read|Čtení konfigurace integrace ALM výchozí aplikace Application Insights|
|Komponenty nebo odstranění|Odstraní konfiguraci komponenty Application Insights.|
|/Components/ExportConfiguration/Action|Application Insights exportovat nastavení akce|
|/Components/ExportConfiguration/Delete|Odstraňování Application Insights export nastavení|
|/Components/ExportConfiguration/Read|Čtení Application Insights export nastavení|
|/Components/ExportConfiguration/Write|Zápis Application Insights export nastavení|
|/ Součásti nebo ExtendQueries/čtení|Výsledky dotazu součást rozšířené čtení Application Insights|
|Součásti, oblíbených položek nebo odstranění|Odstranění Application Insights Oblíbené položky.|
|/ Součásti nebo oblíbených položek/čtení|Čtení oblíbenou položku Application Insights|
|A součásti nebo oblíbených položek/zápis|Zápis oblíbenou položku Application Insights|
|/Components/FeatureCapabilities/Read|Možností funkce součást čtení Application Insights|
|/Components/GetAvailableBillingFeatures/Read|Čtení Application Insights, součást dostupných fakturace funkcí|
|/ Součásti nebo gettoken – / čtení|Čtení token součásti Application Insights|
|/Components/ListMigrationDate/Action|Get back předplatné migrace datum|
|/Components/ListMigrationDate/Read|Data migrace back předplatné GET|
|/ Součásti nebo MetricDefinitions/čtení|Čtení definice metrik součásti Application Insights|
|/ Součásti nebo metriky/čtení|Metriky součást čtení Application Insights|
|/Components/MigrateToNewpricingModel/Action|Migrace předplatného na nový cenový model|
|/ Součásti nebo přesunutí nebo akce|Přesunout komponentu Application Insights do jiné skupiny prostředků nebo předplatného|
|/Components/MyAnalyticsItems/Delete|Odstranění položky osobní analytics Application Insights|
|/Components/MyAnalyticsItems/Read|Čtení položky osobní analytics Application Insights|
|/Components/MyAnalyticsItems/Write|Zápis položku osobní analytics Application Insights|
|/ Součásti nebo MyFavorites/čtení|Čtení osobní oblíbenou položku Application Insights|
|/ Součásti nebo PricingPlans/čtení|Čtení komponentu Application Insights ceny plán|
|A součásti nebo PricingPlans/zápis|Zápis komponentu Application Insights ceny plán|
|/Components/ProactiveDetectionConfigs/Read|Konfigurace zjištění proaktivní čtení Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Zápis konfigurace proaktivní zjištění Application Insights|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Číst definice metrik|
|/ Součásti nebo QuotaStatus/čtení|Čtení stav kvóty součásti Application Insights|
|/ Součásti/čtení|Přečte konfiguraci komponenty Application Insights.|
|/ Součásti nebo RollbackToLegacyPricingModel nebo akce|Vrácení zpět předplatné starší cenový model|
|/Components/SyntheticMonitorLocations/Read|Umístění testu webu čtení Application Insights|
|/Components/WorkItemConfigs/Delete|Odstraňuje se konfigurace integrace aplikace Application Insights ALM|
|/Components/WorkItemConfigs/Read|Čtení konfiguraci integrace Application Insights ALM|
|/Components/WorkItemConfigs/Write|Zápis konfiguraci integrace Application Insights ALM|
|A součásti/zápis|Provede zápis do konfigurace komponenty Application Insights.|
|DiagnosticSettings nebo odstranění|Odstraňuje se konfigurační nastavení diagnostiky.|
|/ DiagnosticSettings/čtení|Čte se konfigurační nastavení diagnostiky.|
|/DiagnosticSettings/Write|Zapisuje se do konfiguračního nastavení diagnostiky.|
|/ EventCategories/čtení|Načtení kategorie události|
|/eventtypes/digestevents/Read|Číst výtah typů událostí správy|
|/eventtypes/Values/Read|Číst hodnoty typů událostí správy|
|ExtendedDiagnosticSettings nebo odstranění|Odstraňuje se konfigurace rozšířeného nastavení diagnostiky.|
|/ ExtendedDiagnosticSettings/čtení|Načítá se konfigurace rozšířeného nastavení diagnostiky.|
|/ExtendedDiagnosticSettings/Write|Zapisuje se konfigurace rozšířeného nastavení diagnostiky.|
|/ LogDefinitions/čtení|Číst definice protokolů|
|LogProfiles nebo odstranění|Odstraní konfiguraci profilů protokolů.|
|/ LogProfiles/čtení|Přečte profily protokolů.|
|/LogProfiles/Write|Provede zápis do konfigurace profilu protokolu.|
|/MetricAlerts/Delete|Odstranění upozornění metriky|
|/ MetricAlerts/čtení|Načtení upozornění metriky|
|/ MetricAlerts/zápis|Zápis upozornění metriky|
|/MetricDefinitions/Microsoft.Insights/Read|Číst definice metrik|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Číst definice metrik|
|/ MetricDefinitions/čtení|Číst definice metrik|
|/ Metriky nebo poskytovatelé/metriky/čtení|Čtení metrik|
|/ Metriky/čtení|Čtení metrik|
|A metriky/zápis|Zápis metriky|
|/ Operací/čtení|Načtení operací|
|/ Registrace nebo akce|Registrovat poskytovatele platformy Microsoft Insights|
|/ Klienty nebo registrace nebo akce|Inicializuje poskytovatele platformy Microsoft Insights.|
|Nebo zrušit registraci nebo akce|Registrovat poskytovatele platformy Microsoft Insights|
|Webtests nebo odstranění|Odstraní konfiguraci webového testu.|
|/ Webtests/gettoken – / čtení|Čtení token testu webu|
|/ Webtests/MetricDefinitions/čtení|Čtení definice metrik testu webu|
|/ Webtests/metriky/čtení|Čtení metriky testu webu|
|/ Webtests/čtení|Přečte konfiguraci webového testu.|
|/ Webtests/zápis|Provede zápis do konfigurace webového testu.|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operace | Popis |
|---|---|
|/checkNameAvailability/read|Zkontroluje, jestli je název trezoru klíčů platný a jestli se nepoužívá.|
|/deletedVaults/read|Zobrazí vlastnosti obnovitelně odstraněných trezorů klíčů.|
|/hsmPools/delete|Odstraní fond HSM.|
|/hsmPools/joinVault/action|Připojí trezor klíčů k fondu HSM.|
|/hsmPools/read|Zobrazí vlastnosti fondu HSM.|
|/hsmPools/write|Pokud chcete aktualizovat vlastnosti existujícího fondu HSM, vytvořte nový fond HSM.|
|/Locations/deletedVaults/PURGE/Action|Trvale odstraní trezor klíčů, který se nejdříve odstranil obnovitelně.|
|/Locations/deletedVaults/Read|Zobrazí vlastnosti obnovitelně odstraněného trezoru klíčů.|
|/locations/deleteVirtualNetworkOrSubnets/action|Upozorní službu Microsoft.KeyVault, že se odstraňuje virtuální síť nebo podsíť.|
|/Locations/operationResults/Read|Zkontroluje výsledek dlouho běžící operace.|
|/Operations/Read|Umožňuje zobrazit seznam operací dostupných v poskytovateli prostředků Microsoft.KeyVault.|
|/ registrace nebo akce|Zaregistruje předplatné.|
|nebo zrušit registraci nebo akce|Zruší registraci předplatného.|
|/vaults/accessPolicies/Write|Existující zásady přístupu můžete aktualizovat sloučením nebo nahrazením, případně můžete do trezoru přidat nové zásady přístupu.|
|/vaults/DELETE|Odstranění trezoru klíčů|
|/vaults/Deploy/Action|Umožní přístup k tajným kódům v trezoru klíčů při nasazování prostředků Azure.|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Získá nastavení diagnostiky pro tento prostředek.|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/vaults/providers/Microsoft.Insights/logDefinitions/Read|Načte dostupné protokoly pro trezor klíčů.|
|/vaults/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro trezor klíčů.|
|/vaults/Read|Zobrazí vlastnosti trezoru klíčů.|
|/vaults/secrets/Read|Zobrazí vlastnosti tajného kódu, ale ne jeho hodnotu.|
|/vaults/secrets/Write|Vytvoří nový tajný kód nebo aktualizuje hodnotu existujícího tajného kódu.|
|/ trezory a zápis|Vytvoří nový trezor klíčů nebo aktualizuje vlastnosti existujícího trezoru klíčů.|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Operace | Popis |
|---|---|
|/labAccounts/CreateLab/action|Vytvoření testovacího prostředí v účtu testovacího prostředí.|
|/labAccounts/DELETE|Odstraňte účty testovacího prostředí.|
|/labAccounts/labs/delete|Odstraňte laboratoře.|
|/labAccounts/labs/environmentSettings/delete|Odstraňte nastavení prostředí.|
|/labAccounts/labs/environmentSettings/environments/delete|Odstraňte prostředí.|
|/labAccounts/labs/environmentSettings/environments/read|Přečtěte si prostředí.|
|/labAccounts/labs/environmentSettings/environments/write|Přidat nebo změnit prostředí.|
|/labAccounts/Labs/environmentSettings/Publish/Action|Zřizuje/deprovisions požadované prostředky pro nastavení prostředí na základě aktuálního stavu nastavení testovacím prostředí.|
|/labAccounts/labs/environmentSettings/read|Nastavení prostředí pro čtení.|
|/labAccounts/labs/environmentSettings/write|Přidat nebo změnit nastavení prostředí.|
|/labAccounts/labs/read|Přečtěte si laboratoře.|
|/labAccounts/labs/users/delete|Odstraňte uživatele.|
|/labAccounts/labs/users/read|Přečtěte si uživatelé.|
|/labAccounts/Labs/Users/Write|Přidat nebo změnit uživatele.|
|/labAccounts/labs/write|Přidat nebo změnit laboratoře.|
|/labAccounts/read|Přečtěte si účty testovacího prostředí.|
|/ labAccounts/zápisu|Přidat nebo změnit účty testovacího prostředí.|
|/Locations/Operations/Read|Operace čtení.|
|/ registrace nebo akce|Zaregistruje předplatné|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Operace | Popis |
|---|---|
|/Accounts/DELETE|Odstranit umístění na základě účet služeb.|
|/accounts/listKeys/action|Seznam klíčů k účtu služeb na základě umístění|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro účty služeb na základě umístění|
|/Accounts/Read|Při získávání umístění, na základě účet služeb.|
|/accounts/regenerateKey/action|Generovat nové primární nebo sekundární klíč účtu služeb na základě umístění|
|/ účty a zápis|Vytvořit nebo aktualizovat účet služeb na základě umístění.|
|/ registrace nebo akce|Zaregistrujte zprostředkovatele|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operace | Popis |
|---|---|
|/integrationAccounts/agreements/delete|Odstraní smlouvu v integraci účtu.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Získá adresu URL zpětné volání pro obsah smlouvy v integraci účtu.|
|/integrationAccounts/agreements/read|Přečte smlouvu v integraci účtu.|
|/integrationAccounts/agreements/Write|Vytvoří nebo aktualizuje smlouvu v integraci účtu.|
|/integrationAccounts/Assemblies/DELETE|Odstraní sestavení v integraci účtu.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Získá adresu URL zpětné volání pro obsah sestavení v integraci účtu.|
|/integrationAccounts/Assemblies/Read|Načte sestavení v integraci účtu.|
|/integrationAccounts/Assemblies/Write|Vytvoří nebo aktualizuje sestavení v integraci účtu.|
|/integrationAccounts/batchConfigurations/delete|Odstraní konfiguraci batch v integraci účtu.|
|/integrationAccounts/batchConfigurations/Read|Načte konfiguraci batch v integraci účtu.|
|/integrationAccounts/batchConfigurations/write|Vytvoří nebo aktualizuje konfigurace batch v integraci účtu.|
|/integrationAccounts/Certificates/DELETE|Odstraní certifikát v integraci účtu.|
|/integrationAccounts/Certificates/Read|Načte certifikát v integraci účtu.|
|/integrationAccounts/Certificates/Write|Vytvoří nebo aktualizuje certifikátu v integraci účtu.|
|/integrationAccounts/DELETE|Odstraní účet integrace.|
|/integrationAccounts/listCallbackUrl/action|Získá adresu URL zpětné volání pro integraci účet.|
|/integrationAccounts/listKeyVaultKeys/action|Získá klíče v trezoru klíčů.|
|/integrationAccounts/logTrackingEvents/action|Protokoluje události sledování v účtu integrace.|
|/integrationAccounts/maps/delete|Odstraní mapy ve integrace účtu.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Získá adresu URL zpětné volání pro mapu obsahu v integraci účtu.|
|/integrationAccounts/maps/read|Přečte mapy ve integrace účtu.|
|/integrationAccounts/Maps/Write|Vytvoří nebo aktualizuje mapy ve integrace účtu.|
|/integrationAccounts/partners/delete|Odstraní partnera v integraci účtu.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Získá adresu URL zpětné volání pro partnera obsahu v integraci účtu.|
|/integrationAccounts/partners/read|Přečte společník v integraci účtu.|
|/integrationAccounts/partners/write|Vytvoří nebo aktualizuje partnera v integraci účtu.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Načte definice protokolů účtu pro integraci.|
|/integrationAccounts/read|Přečte integrace účtu.|
|/integrationAccounts/regenerateAccessKey/action|Obnoví tajné kódy přístupových klíčů.|
|/integrationAccounts/schemas/delete|Odstraní schéma v integraci účtu.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Získá adresu URL zpětné volání pro obsah schématu v integraci účtu.|
|/integrationAccounts/schemas/read|Přečte schéma v integraci účtu.|
|/integrationAccounts/schemas/write|Vytvoří nebo aktualizuje na schéma v integraci účtu.|
|/integrationAccounts/Sessions/DELETE|Odstraní relace v integraci účtu.|
|/integrationAccounts/Sessions/Read|Načte konfiguraci batch v integraci účtu.|
|/integrationAccounts/Sessions/Write|Vytvoří nebo aktualizuje relace v integraci účtu.|
|/ integrationAccounts/zápisu|Vytvoří nebo aktualizuje účet integrace.|
|/Locations/workflows/Validate/Action|Ověří pracovní postup.|
|/Operations/Read|Získá operaci.|
|/ registrace nebo akce|Zaregistruje pro dané předplatné poskytovatele prostředků Microsoft.Logic.|
|/workflows/accessKeys/delete|Odstraní přístupový klíč.|
|/workflows/accessKeys/list/action|Zobrazí seznam tajných kódů přístupových klíčů.|
|/workflows/accessKeys/read|Načte přístupový klíč.|
|/workflows/accessKeys/regenerate/Action|Obnoví tajné kódy přístupových klíčů.|
|/workflows/accessKeys/write|Vytvoří nebo aktualizuje přístupový klíč.|
|/workflows/DELETE|Odstraní pracovní postup.|
|/workflows/disable/Action|Zakáže pracovní postup.|
|/workflows/enable/Action|Povolí pracovní postup.|
|/workflows/listCallbackUrl/action|Načte adresu URL zpětného volání pro pracovní postup.|
|/workflows/listSwagger/action|Získá definice Swagger pracovních postupů.|
|/workflows/Move/Action|Přesune pracovní postup z existujícího ID předplatného, skupiny prostředků a/nebo názvu na jiné ID předplatného, skupinu prostředků nebo název.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Načte nastavení diagnostiky pracovního postupu.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pracovního postupu.|
|/workflows/providers/Microsoft.Insights/logDefinitions/Read|Načte definice protokolů pracovního postupu.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/Read|Načte definice metrik pracovního postupu.|
|/workflows/Read|Načte pracovní postup.|
|/workflows/regenerateAccessKey/Action|Obnoví tajné kódy přístupových klíčů.|
|/workflows/Run/Action|Spustí běh pracovního postupu.|
|/workflows/runs/actions/listExpressionTraces/action|Získá trasy výrazu akce spuštění pracovního postupu.|
|/workflows/runs/Actions/Read|Načte akci běhu pracovního postupu.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Získá trasy výrazu opakování akce spuštění pracovního postupu.|
|/workflows/runs/Actions/repetitions/Read|Načte opakování akce běhu pracovního postupu.|
|/workflows/runs/Actions/scoperepetitions/Read|Načte opakování rozsahu akce běhu pracovního postupu.|
|/workflows/runs/Cancel/Action|Zruší běh pracovního postupu.|
|/workflows/runs/Operations/Read|Načte stav operace běhu pracovního postupu.|
|/ pracovních/spouští/číst|Načte běh pracovního postupu.|
|/workflows/suspend/Action|Pozastaví pracovní postup.|
|/workflows/triggers/histories/read|Načte historie triggerů.|
|/workflows/triggers/histories/resubmit/action|Znovu spustí trigger pracovního postupu.|
|/workflows/triggers/listCallbackUrl/action|Získá adresu URL zpětného volání pro trigger.|
|/workflows/triggers/read|Načte trigger.|
|/workflows/Triggers/reset/Action|Obnoví aktivační událost.|
|/workflows/Triggers/Run/Action|Spustí trigger.|
|/workflows/Triggers/setState/Action|Nastaví stav aktivační události.|
|/workflows/Validate/Action|Ověří pracovní postup.|
|/workflows/versions/Read|Načte verzi pracovního postupu.|
|/workflows/versions/triggers/listCallbackUrl/action|Získá adresu URL zpětného volání pro trigger.|
|/ pracovní postupy a zápis|Vytvoří nebo aktualizuje pracovní postup.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operace | Popis |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Přesunout všechny strojového učení závazků přidružení plánu|
|/commitmentPlans/commitmentAssociations/read|Číst všechny strojového učení závazků přidružení plánu|
|/commitmentPlans/delete|Odstranit všechny strojového učení závazků plán|
|/commitmentPlans/JOIN/Action|Připojte všechny strojového učení závazků plán|
|/commitmentPlans/read|Číst všechny strojového učení závazků plán|
|/commitmentPlans/write|Vytvořit nebo aktualizovat každého Machine Learning závazků plánu|
|/Locations/operationresults/Read|Získání výsledku operace Machine Learning|
|/locations/operationsstatus/read|Získat stav probíhající operace Machine Learning|
|/Operations/Read|Získat strojového učení operací|
|/ registrace nebo akce|Zaregistruje předplatné pro strojové učení zprostředkovatel prostředků webové služby a povolí vytvoření webových služeb.|
|/skus/Read|Získat strojového učení závazků plán SKU|
|/webServices/action|Vytvořit místní vlastnosti webové služby pro podporované oblasti|
|/webServices/delete|Odstranit všechny webová služba Machine Learning|
|/webServices/read|Číst všechny webová služba Machine Learning|
|/webServices/write|Vytvořit nebo aktualizovat žádné webové služby Machine Learning|
|Pracovních prostorů a odstranění|Odstranit všechny strojového učení pracovního prostoru|
|/ Pracovních prostorů nebo listworkspacekeys nebo akce|Seznam klíčů pro pracovní prostor Machine Learning|
|/ Pracovních prostorů/čtení|Číst všechny strojového učení pracovního prostoru|
|/ Pracovních prostorů nebo resyncstoragekeys nebo akce|Nové synchronizace klíče účtu úložiště, které jsou nakonfigurované pro pracovní prostor Machine Learning|
|A pracovní prostory/zápis|Vytvořit nebo aktualizovat všechny strojového učení pracovního prostoru|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Operace | Popis |
|---|---|
|/operationalizationClusters/checkUpdate/action|Zkontrolujte, zda jsou k dispozici pro systémových služeb pro operationalization cluster aktualizace|
|/operationalizationClusters/delete|Odstranění všech hostitelských účtu|
|/operationalizationClusters/listKeys/action|Výpis klíčů přidruženého k operationalization clusteru|
|/operationalizationClusters/Read|Číst libovolného hostování účtu|
|/operationalizationClusters/updateSystem/action|Aktualizace systémových služeb v clusteru služby operationalization|
|/ operationalizationClusters/zápisu|Vytvořit nebo aktualizovat všechny hostování účtu|
|/ registrace nebo akce|Zaregistruje ID předplatného pro poskytovatele prostředků a povolí vytvoření strojového učení výpočetní prostředky|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Operace | Popis |
|---|---|
|/Accounts/DELETE|Odstranění všech hostitelských účtu|
|/Accounts/Read|Číst libovolného hostování účtu|
|/ účty a zápis|Vytvořit nebo aktualizovat všechny hostování účtu|
|/ registrace nebo akce|Zaregistruje ID předplatného pro poskytovatele prostředků a povolí vytvoření hostování účtu|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Operace | Popis |
|---|---|
|/userAssignedIdentities/assign/action|Akce RBAC pro přiřazení stávajícího uživatele přiřazené identity k prostředku|
|/userAssignedIdentities/delete|Odstraní stávajícího uživatele přiřazené identity|
|/userAssignedIdentities/read|Získá stávajícího uživatele přiřazené identity|
|/ userAssignedIdentities/zápisu|Vytvoří nového uživatele přiřazené identity nebo aktualizuje značkám přidruženým stávajícího uživatele přiřazené identity|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Operace | Popis |
|---|---|
|/labAccounts/CreateLab/action|Vytvoření testovacího prostředí v účtu testovacího prostředí.|
|/labAccounts/DELETE|Odstraňte účty testovacího prostředí.|
|/labAccounts/labs/delete|Odstraňte laboratoře.|
|/labAccounts/labs/environmentSettings/delete|Odstraňte nastavení prostředí.|
|/labAccounts/labs/environmentSettings/environments/delete|Odstraňte prostředí.|
|/labAccounts/labs/environmentSettings/environments/read|Přečtěte si prostředí.|
|/labAccounts/labs/environmentSettings/environments/write|Přidat nebo změnit prostředí.|
|/labAccounts/labs/environmentSettings/read|Nastavení prostředí pro čtení.|
|/labAccounts/labs/environmentSettings/write|Přidat nebo změnit nastavení prostředí.|
|/labAccounts/labs/labVms/delete|Odstranění testovacího prostředí virtuálních počítačů.|
|/labAccounts/labs/labVms/read|Přečtěte si testovacího prostředí virtuálních počítačů.|
|/labAccounts/labs/labVms/write|Přidat nebo změnit prostředí virtuálních počítačů.|
|/labAccounts/labs/read|Přečtěte si laboratoře.|
|/labAccounts/labs/write|Přidat nebo změnit laboratoře.|
|/labAccounts/read|Přečtěte si účty testovacího prostředí.|
|/ labAccounts/zápisu|Přidat nebo změnit účty testovacího prostředí.|
|/Locations/Operations/Read|Operace čtení.|
|/ registrace nebo akce|Zaregistruje předplatné|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Operace | Popis |
|---|---|
|/checkNameAvailability/action|Ověří, zda je název skupiny pro správu zadané platné a jedinečný.|
|/ getEntities nebo akce|Zobrazí seznam všech entit (skupiny pro správu, odběry atd.) pro ověřené uživatele.|
|/managementGroups/delete|Odstraňte skupinu pro správu.|
|/managementGroups/Read|Zobrazí seznam skupin pro správu pro ověřené uživatele.|
|/managementGroups/subscriptions/delete|Přidruží zrušíte předplatné ze skupiny pro správu.|
|/managementGroups/Subscriptions/Write|Partnerů existující předplatné se skupinou pro správu.|
|/managementGroups/write|Vytvořit nebo aktualizovat skupinu pro správu.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Operace | Popis |
|---|---|
|/ClassicDevServices/delete|Nepodporuje operace odstranění prostředku služby classic vývojářů.|
|/ClassicDevServices/listSecrets/action|Získá classic vývojářů služby prostředků správy klíčů.|
|/ClassicDevServices/listSingleSignOnToken/action|Získá jeden přihlašovací na URL pro službu classic vývojářů.|
|/ClassicDevServices/read|Provede operaci GET na službě classic vývojářů.|
|/ClassicDevServices/regenerateKey/action|Generuje classic vývojářů služby prostředků správy klíčů.|
|/ Operací/čtení|Čtení operací pro všechny typy prostředků.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operace | Popis |
|---|---|
|/agreements/OFFERS/plans/Cancel/Action|Zrušit smlouvu pro položku daného webu marketplace|
|/agreements/OFFERS/plans/Read|Vrátí smlouvu pro položku daného webu marketplace|
|/agreements/OFFERS/plans/Sign/Action|Podepsat smlouvu pro položku daného webu marketplace|
|/agreements/Read|Vrátí všechny smlouvy v rámci zadané předplatné|
|/offertypes/Publishers/OFFERS/plans/agreements/Read|Získat smlouvu pro položku marketplace daného virtuálního počítače|
|/offertypes/Publishers/OFFERS/plans/agreements/Write|Přihlášení nebo zrušte smlouvu pro položku marketplace daného virtuálního počítače|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operace | Popis |
|---|---|
|/ checknameavailability nebo akce|Ověří, zda je název účtu Media Services k dispozici|
|/mediaservices/DELETE|Odstranit všechny účtu Media Services|
|/mediaservices/listKeys/action|Výpis klíčů služby ACS pro účet Media Services|
|/mediaservices/Read|Číst všechny účtu Media Services|
|/mediaservices/regenerateKey/action|Znovu vygenerovat klíč média služby ACS|
|/mediaservices/syncStorageKeys/action|Synchronizovat úložiště klíče pro účet připojené úložiště Azure|
|/ mediaservices/zápisu|Vytvořit nebo aktualizovat všechny účtu Media Services|
|/Operations/Read|Číst všechny účtu Media Services|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků Media Services a povolí vytvoření účtů Media Services|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Operace | Popis |
|---|---|
|/ Operací/čtení|Načte zpřístupněné operace.|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operace | Popis |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Application Gateway Ssl předdefinovaných zásad|
|/applicationGatewayAvailableSslOptions/read|Dostupné možnosti Ssl aplikace brány|
|/applicationGatewayAvailableWafRuleSets/read|Získá aplikační bránu, kterou sad pravidel k dispozici firewall webových aplikací|
|/applicationGateways/backendAddressPools/join/action|Spojí fond adres back-end brány aplikace|
|/applicationGateways/backendhealth/action|Získá stavu back-end brány aplikaci|
|/applicationGateways/delete|Odstraní aplikační bránu.|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Získejte tabulku směrování nakonfigurované na aplikační brány|
|/applicationGateways/effectiveRouteTable/action|Získejte tabulku směrování nakonfigurované na aplikační brány|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Načte události služby Application Gateway|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro službu Application Gateway|
|/applicationGateways/read|Získá aplikační bránu.|
|/applicationGateways/setSecurityCenterConfiguration/action|Konfigurace sady Application Gateway Security Center|
|/applicationGateways/start/action|Spuštění služby application gateway|
|/applicationGateways/stop/action|Zastavení služby application gateway|
|/applicationGateways/write|Služby application gateway vytvoří nebo aktualizuje aplikační bránu.|
|/applicationSecurityGroups/delete|Odstraní skupinu zabezpečení aplikací|
|/applicationSecurityGroups/joinIpConfiguration/action|Spojí konfiguraci IP adres do skupin zabezpečení aplikace.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Spojí pravidlo zabezpečení do skupiny zabezpečení aplikací.|
|/applicationSecurityGroups/read|Získá identifikátor skupiny zabezpečení aplikací.|
|/applicationSecurityGroups/write|Vytvoří skupinu zabezpečení aplikace nebo aktualizuje existující skupiny zabezpečení aplikace.|
|/bgpServiceCommunities/read|Získat komunit protokolu Bgp služby|
|/checkTrafficManagerNameAvailability/action|Zkontroluje dostupnost názvu DNS relativní Traffic Manageru.|
|/Connections/DELETE|Deletes VirtualNetworkGatewayConnection|
|/Connections/Read|Gets VirtualNetworkGatewayConnection|
|/Connections/sharedkey/Action|Get VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/read|Gets VirtualNetworkGatewayConnection SharedKey|
|/Connections/sharedKey/Write|Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection SharedKey|
|/connections/vpndeviceconfigurationscript/read|Získá konfiguraci zařízení Vpn VirtualNetworkGatewayConnection|
|/ připojení a zápis|Vytvoří nebo aktualizuje existující VirtualNetworkGatewayConnection|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Odstraní Proxy plán ochranu DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Získá definici Proxy plán ochranu DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Vytvoří Proxy plán ochranu DDoS nebo aktualizace a stávající DDoS ochrany plánování Proxy|
|/ddosProtectionPlans/delete|Odstraní plán ochranu DDoS|
|/ddosProtectionPlans/join/action|Spojí plán ochranu DDoS|
|/ddosProtectionPlans/read|Získá plán ochranu DDoS|
|/ddosProtectionPlans/write|Vytvoří plán ochrany DDoS nebo aktualizuje plán ochranu DDoS |
|/dnsoperationresults/read|Získá výsledky operace DNS|
|/dnsoperationstatuses/read|Získá stav operace DNS |
|/dnszones/A/delete|Odebrat sadu záznamů daného názvu a typu "A" ze zóny DNS.|
|/dnszones/A/read|Načíst sadu záznamů typu "A", ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag.|
|/dnszones/A/write|Vytvořit nebo aktualizovat sadu záznamů typu "A" v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/AAAA/delete|Odebrat sadu záznamů daného názvu a typu 'AAAA, umožňuje ze zóny DNS.|
|/dnszones/AAAA/read|Načíst sadu záznamů typu 'AAAA, ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag.|
|/dnszones/AAAA/write|Vytvořit nebo aktualizovat sadu záznamů typu 'AAAA, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/all/read|Získá sady záznamů DNS mezi typy|
|/dnszones/CAA/delete|Odebrat sadu záznamů daného názvu a typu "KÁ' umožňuje ze zóny DNS.|
|/dnszones/CAA/read|Načíst sadu záznamů typu 'KÁ' ve formátu JSON. Sada záznamů obsahuje TTL, značky a etag.|
|/dnszones/CAA/write|Vytvořit nebo aktualizovat sadu záznamů typu 'KÁ' v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/CNAME/delete|Odebrat sadu záznamů daného názvu a typu, CNAME, umožňuje ze zóny DNS.|
|/dnszones/CNAME/read|Načíst sadu záznamů typu 'CNAME' ve formátu JSON. Sada záznamů obsahuje TTL, značky a etag.|
|/dnszones/CNAME/write|Vytvořit nebo aktualizovat sadu záznamů typu, CNAME, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/delete|Odstraňte zónu DNS, ve formátu JSON. Vlastnosti zóny zahrnují značky, etag, numberOfRecordSets a maxNumberOfRecordSets.|
|/dnszones/MX/delete|Odebrat sadu záznamů daného názvu a typu "MX" ze zóny DNS.|
|/dnszones/MX/read|Načíst sadu záznamů typu "MX", ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag.|
|/dnszones/MX/write|Vytvořit nebo aktualizovat sadu záznamů typu "MX" v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/NS/delete|Umožňuje odstranit sadu záznamů typu NS DNS|
|/dnszones/NS/Read|Získá DNS sadu záznamů typu NS|
|/dnszones/NS/Write|Vytvoří nebo aktualizuje DNS sadu záznamů typu NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky zóny DNS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky zóny DNS|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Získá zónu DNS definice metrik|
|/dnszones/PTR/delete|Odebrat sadu záznamů daného názvu a typu 'PTR, umožňuje ze zóny DNS.|
|/dnszones/PTR/read|Načíst sadu záznamů typu 'PTR, ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag.|
|/dnszones/PTR/write|Vytvořit nebo aktualizovat sadu záznamů typu 'PTR, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/read|Umožňuje načíst zónu DNS, ve formátu JSON. Vlastnosti zóny zahrnují značky, etag, numberOfRecordSets a maxNumberOfRecordSets. Všimněte si, že tento příkaz nenačítá sady záznamů v rámci zóny.|
|/dnszones/recordsets/read|Získá sady záznamů DNS mezi typy|
|/dnszones/SOA/read|Získá sadu záznamů DNS typu SOA|
|/dnszones/SOA/write|Vytvoří nebo aktualizuje DNS sadu záznamů typu SOA|
|/dnszones/SRV/delete|Odebrat sadu záznamů daného názvu a typu SRV "služby" ze zóny DNS.|
|/dnszones/SRV/read|Načíst sadu záznamů typu 'SRV' ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag.|
|/dnszones/SRV/write|Vytvořit nebo aktualizovat sadu záznamů typu SRV|
|/dnszones/TXT/delete|Odebrat sadu záznamů daného názvu a typu 'TXT, umožňuje ze zóny DNS.|
|/dnszones/TXT/read|Načíst sadu záznamů typu 'TXT, ve formátu JSON. Sada záznamů obsahuje seznam záznamů, jakož i TTL, značky a etag.|
|/dnszones/TXT/write|Vytvořit nebo aktualizovat sadu záznamů typu 'TXT, v rámci zóny DNS. Zadané záznamy nahradí aktuální záznamy v sadě záznamů.|
|/dnszones/write|Vytvořit nebo aktualizovat zónu DNS v rámci skupiny prostředků.  Použít k aktualizaci značek u prostředku zóny DNS. Všimněte si, že tento příkaz nelze použít k vytvoření nebo aktualizaci sad záznamů v rámci zóny.|
|/expressRouteCircuits/authorizations/delete|Odstraní ExpressRouteCircuit povolení|
|/expressRouteCircuits/authorizations/read|Získá povolení ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Vytvoří nebo aktualizuje existující povolení ExpressRouteCircuit|
|/expressRouteCircuits/delete|Odstraní ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Získá ArpTable ExpressRouteCircuit partnerského vztahu|
|/expressRouteCircuits/peerings/connections/delete|Odstraní připojení k ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/read|Získá připojení k ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/write|Vytvoří nebo aktualizuje existující prostředek připojení ExpressRouteCircuit|
|/expressRouteCircuits/peerings/delete|Odstraní partnerský vztah ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Získá partnerský vztah ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Získá RouteTable ExpressRouteCircuit partnerského vztahu|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Získá ExpressRouteCircuit partnerský vztah RouteTable souhrn|
|/expressRouteCircuits/peerings/stats/read|Získá ExpressRouteCircuit partnerský vztah Statistika|
|/expressRouteCircuits/peerings/write|Vytvoří nebo aktualizuje existující ExpressRouteCircuit partnerský vztah|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro okruhy ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro okruhy ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Získat události pro okruhy ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Získá definice metrik pro okruhy ExpressRoute|
|/expressRouteCircuits/read|Získat ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Získá ExpressRouteCircuit Stat|
|/expressRouteCircuits/write|Vytvoří nebo aktualizuje existující ExpressRouteCircuit|
|/expressRouteCrossConnections/delete|Odstranit Express Route křížová připojení|
|/expressRouteCrossConnections/join/action|Křížové spojení na Express Route připojení|
|/expressRouteCrossConnections/peerings/arpTables/action|Získá trasu Express křížové připojení partnerský vztah na základě tabulky Arp|
|/expressRouteCrossConnections/peerings/delete|Odstraní Express směrování mezi partnerský vztah připojení|
|/expressRouteCrossConnections/peerings/Read|Získá trasu Express křížové připojení partnerského vztahu|
|/expressRouteCrossConnections/peerings/routeTables/action|Získá trasu Express křížové připojení partnerský vztah směrovací tabulku|
|/expressRouteCrossConnections/peerings/routeTableSummary/Action|Získá trasu Express křížové připojení partnerský vztah trasy tabulky Souhrn|
|/expressRouteCrossConnections/peerings/stats/Read|Získá trasu Express křížové připojení partnerský vztah Statistika|
|/expressRouteCrossConnections/peerings/write|Vztahu Express Route křížová připojení vytvoří nebo aktualizuje existující Express trasy mezi připojení partnerský vztah|
|/expressRouteCrossConnections/read|Získat Express Route křížová připojení|
|/expressRouteCrossConnections/write|Vytvořit nebo aktualizovat Express Route křížová připojení|
|/expressRouteServiceProviders/read|Získá poskytovatele služeb Expressroute|
|/loadBalancers/backendAddressPools/join/action|Spojí fond back-end adresy Vyrovnávání zatížení.|
|/loadBalancers/backendAddressPools/read|Získá definici fondu adres back-end zatížení nástroje pro vyrovnávání|
|/loadBalancers/DELETE|Odstraní nástroj pro vyrovnávání zatížení|
|/loadBalancers/frontendIPConfigurations/read|Získá definice konfigurace IP front-endu nástroje pro vyrovnávání zatížení|
|/loadBalancers/inboundNatPools/join/action|Spojí Vyrovnávání zatížení fond příchozích pravidel nat|
|/loadBalancers/inboundNatPools/read|Získá Vyrovnávání zatížení příchozí nat definici fondu|
|/loadBalancers/inboundNatRules/delete|Odstraní pravidlo příchozí nat nástroje pro vyrovnávání zatížení.|
|/loadBalancers/inboundNatRules/join/action|Spojí pravidlo příchozí nat nástroje pro vyrovnávání zatížení.|
|/loadBalancers/inboundNatRules/read|Nástroj pro vyrovnávání zatížení získá definici příchozího pravidla nat|
|/loadBalancers/inboundNatRules/write|Vytvoří pravidlo příchozí nat nástroje pro vyrovnávání zatížení nebo aktualizuje existující pravidlo příchozí nat nástroje pro vyrovnávání zatížení|
|/loadBalancers/loadBalancingRules/read|Získá zatížení nástroje pro vyrovnávání zatížení vyrovnávání definici pravidla|
|/loadBalancers/networkInterfaces/read|Získá odkazy na rozhraní sítě pod nástrojem pro vyrovnávání zatížení|
|/loadBalancers/outboundNatRules/read|Získá definici pravidla odchozí nat nástroje pro vyrovnávání zatížení|
|/loadBalancers/probes/JOIN/Action|Umožňuje použití sondy nástroje pro vyrovnávání zatížení. S touto vlastností healthProbe oprávnění měřítka virtuálních počítačů sady můžete odkazovat, sonda.|
|/loadBalancers/probes/Read|Získá sondu nástroje pro vyrovnávání zatížení|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro vyrovnávání zatížení|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro vyrovnávání zatížení|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Získá události pro nástroj pro vyrovnávání zatížení|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro vyrovnávání zatížení|
|/loadBalancers/Read|Získá definici nástroje pro vyrovnávání zatížení.|
|/loadBalancers/virtualMachines/read|Získá odkazy na všechny virtuální počítače pod nástrojem pro vyrovnávání zatížení|
|/ loadBalancers/zápisu|Vytvoří nástroj pro vyrovnávání zatížení nebo aktualizuje existující pro vyrovnávání zatížení|
|/localnetworkgateways/DELETE|Deletes LocalNetworkGateway|
|/localnetworkgateways/Read|Gets LocalNetworkGateway|
|/ localnetworkgateways/zápisu|Vytvoří nebo aktualizuje existující LocalNetworkGateway|
|/locations/checkDnsNameAvailability/read|Zkontroluje, jestli je popisek dns k dispozici v zadaném umístění|
|/Locations/operationResults/Read|Získá výsledek operace asynchronní POST nebo operace odstranění|
|/Locations/Operations/Read|Získá prostředek operace, který reprezentuje stav asynchronní operace|
|/Locations/usages/Read|Získá metriky využití prostředků|
|/locations/virtualNetworkAvailableEndpointServices/read|Získá seznam dostupných virtuální síťové koncový bod služby|
|/networkInterfaces/delete|Odstraní rozhraní sítě|
|/networkInterfaces/diagnosticIdentity/read|Získá diagnostiky Identity prostředku|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Získat skupiny zabezpečení sítě nakonfigurované na síťové rozhraní z virtuálního počítače|
|/networkInterfaces/effectiveRouteTable/action|Získejte tabulku směrování nakonfigurovaný v síťovém rozhraní virtuálního počítače|
|/networkInterfaces/ipconfigurations/read|Získá definici konfigurace ip rozhraní sítě. |
|/networkInterfaces/join/action|Virtuální počítač připojí k síťovému rozhraní|
|/networkInterfaces/loadBalancers/read|Získá všech služeb Vyrovnávání zatížení, které je součástí rozhraní sítě|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro síťové rozhraní|
|/networkInterfaces/read|Získá definici rozhraní sítě. |
|/networkInterfaces/write|Vytvoří rozhraní sítě nebo aktualizuje existující rozhraní sítě. |
|/networkSecurityGroups/defaultSecurityRules/read|Získá definici pravidla zabezpečení výchozí|
|/networkSecurityGroups/delete|Odstraní skupinu zabezpečení sítě|
|/networkSecurityGroups/join/action|Spojí skupinu zabezpečení sítě|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky skupin zabezpečení sítě|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky skupin zabezpečení sítě, tato operace je doplněná poskytovatelem prostředků přehledů.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/Read|Získá události pro skupinu zabezpečení sítě|
|/networkSecurityGroups/read|Získá definici skupiny zabezpečení sítě|
|/networkSecurityGroups/securityRules/delete|Odstraní pravidlo zabezpečení|
|/networkSecurityGroups/securityRules/read|Získá definici pravidla zabezpečení|
|/networkSecurityGroups/securityRules/write|Vytvoří pravidlo zabezpečení nebo aktualizuje existující pravidlo zabezpečení.|
|/networkSecurityGroups/write|Vytvoří skupinu zabezpečení sítě nebo aktualizuje existující skupinu zabezpečení sítě|
|/networkWatchers/availableProvidersList/action|Vrátí všechny dostupné internet poskytovatelé služeb pro zadané oblasti Azure.|
|/networkWatchers/azureReachabilityReport/action|Vrátí relativní latence skóre pro internet poskytovatelé služeb z určitého umístění k oblastem Azure.|
|/networkWatchers/configureFlowLog/action|Nakonfiguruje toku protokolování pro cílový prostředek.|
|/networkWatchers/connectionMonitors/delete|Odstraní monitorování připojení|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|Získat nastavení diagnostiky monitorování připojení|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings a zápis|Vytvoří nebo aktualizuje nastavení diagnostiky monitorování připojení|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/čtení|Načte dostupné metriky pro monitorování připojení|
|/networkWatchers/connectionMonitors/query/action|Dotaz na monitorování připojení mezi zadané koncové body|
|/networkWatchers/connectionMonitors/read|Získat podrobnosti o monitorování připojení|
|/networkWatchers/connectionMonitors/Start/Action|Spuštění monitorování připojení mezi zadané koncové body|
|/networkWatchers/connectionMonitors/stop/action|Zastavení nebo pozastavení monitorování připojení mezi zadané koncové body|
|/networkWatchers/connectionMonitors/write|Vytvoří připojení monitorování|
|/networkWatchers/connectivityCheck/Action|Ověří možnost pro přímé připojení TCP z virtuálního počítače na danou koncový bod, včetně jiným virtuálním Počítačem nebo libovolný vzdálený server.|
|/networkWatchers/delete|Odstraní sledovací proces sítě|
|/networkWatchers/ipFlowVerify/action|Vrátí, jestli je paket povolený nebo zakázaný do nebo z konkrétní cíl.|
|/networkWatchers/lenses/delete|Odstraní přehledu|
|/networkWatchers/lenses/query/action|Dotaz na monitorování síťových přenosů na zadaný koncový bod|
|/networkWatchers/lenses/read|Získat podrobnosti o přehledu|
|/networkWatchers/lenses/Start/Action|Spuštění monitorování síťových přenosů na zadaný koncový bod|
|/networkWatchers/lenses/stop/action|Zastavení nebo pozastavení monitorování síťových přenosů na zadaný koncový bod|
|/networkWatchers/lenses/Write|Vytvoří přehledu|
|/networkWatchers/nextHop/action|U zadaného cíle a cílové IP adresy vrátí typ dalšího směrování a další naděje IP adresu.|
|/networkWatchers/packetCaptures/delete|Odstraní zachytávání paketů|
|/networkWatchers/packetCaptures/queryStatus/action|Získá informace o vlastnostech a stav prostředku zachytávání paketů.|
|/networkWatchers/packetCaptures/read|Získat definici zachytávání paketů|
|/networkWatchers/packetCaptures/stop/action|Zastavte spuštěné relaci zachytávání paketů.|
|/networkWatchers/packetCaptures/write|Vytvoří zachytávání paketů|
|/networkWatchers/queryFlowLogStatus/action|Získá stav toku protokolování na prostředku.|
|/networkWatchers/queryTroubleshootResult/action|Získá řešení potíží výsledek z dříve spuštění nebo aktuálně spuštěna operace odstraňování potíží.|
|/networkWatchers/read|Získat definici sledovací proces sítě|
|/networkWatchers/securityGroupView/action|Zobrazení pravidel skupiny zabezpečení sítě nakonfigurované a efektivní použita na virtuálním počítači.|
|/networkWatchers/Topology/Action|Získá úrovně zobrazení síťových prostředků a jejich vztahů ve skupině prostředků.|
|/networkWatchers/troubleshoot/Action|Spustí se Poradce při potížích s na prostředku sítě v Azure.|
|/ networkWatchers/zápisu|Sledovací proces sítě vytvoří nebo aktualizuje existující sledovací proces sítě|
|/Operations/Read|Získat dostupné operace|
|/publicIPAddresses/delete|Odstraní veřejnou Ip adresu.|
|/publicIPAddresses/join/action|Spojí veřejnou ip adresu.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Získat nastavení diagnostiky veřejné IP adresy|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Vytvořit nebo aktualizovat nastavení diagnostiky veřejné IP adresy|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Získat definice protokolu veřejné IP adresy|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Získat definice metrik veřejné IP adresy|
|/publicIPAddresses/read|Získá definici veřejné ip adresy.|
|/publicIPAddresses/write|Vytvoří veřejnou Ip adresu nebo aktualizuje existující veřejnou Ip adresu. |
|/ registrace nebo akce|Zaregistruje předplatné|
|/routeFilters/DELETE|Odstraní definici směrovací filtru|
|/routeFilters/JOIN/Action|Spojí filtr trasy|
|/routeFilters/Read|Získá definici filtru trasy|
|/routeFilters/routeFilterRules/delete|Odstraní definici směrovací filtr pravidlo|
|/routeFilters/routeFilterRules/read|Získá definici pravidla filtru trasy|
|/routeFilters/routeFilterRules/write|Vytvoří pravidlo filtru trasu nebo aktualizuje existující pravidlo filtru trasy|
|/ routeFilters/zápisu|Vytvoří filtr trasu nebo aktualizuje existující směrovací filtr|
|/routeTables/delete|Odstraní definici směrovací tabulky|
|/routeTables/JOIN/Action|Spojí směrovací tabulku|
|/routeTables/Read|Získá definici směrovací tabulky|
|/routeTables/routes/delete|Odstraní definici směrovací|
|/routeTables/Routes/Read|Získá definici trasy|
|/routeTables/Routes/Write|Vytvoří trasu nebo aktualizuje existující trasu.|
|/ routeTables/zápisu|Vytvoří směrovací tabulku nebo aktualizuje existující směrovací tabulku.|
|/securegateways/applicationRuleCollections/delete|Odstraní kolekci aplikace pravidel pro zabezpečené brány|
|/securegateways/applicationRuleCollections/read|Načtení kolekci pravidel aplikace pro danou bránu zabezpečení|
|/securegateways/applicationRuleCollections/write|Vytvoří nebo aktualizuje kolekci pravidel aplikace pro bránu zabezpečení|
|/securegateways/DELETE|Odstranit bránu zabezpečení|
|/securegateways/networkRuleCollections/delete|Odstraní kolekci pravidel sítě pro zabezpečené brány|
|/securegateways/networkRuleCollections/read|Načtení sítě kolekci pravidel pro danou bránu zabezpečení|
|/securegateways/networkRuleCollections/write|Vytvoří nebo aktualizuje kolekci pravidel sítě pro bránu zabezpečení|
|/securegateways/Read|Zjištění zabezpečené brány|
|/ securegateways/zápisu|Vytvoří nebo aktualizuje bránu zabezpečení|
|/serviceEndpointPolicies/delete|Odstraní zásadu koncový bod služby|
|/serviceEndpointPolicies/JOIN/Action|Spojí zásadu koncový bod služby|
|/serviceEndpointPolicies/joinSubnet/Action|Spojí podsíť zásad koncový bod služby|
|/serviceEndpointPolicies/Read|Získá popis zásad koncový bod služby|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/DELETE|Odstraní definici zásady koncový bod služby|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Read|Získá dešifrování definice zásady koncový bod služby|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write|Vytvoří definici zásady koncový bod služby nebo aktualizuje existující definice zásady koncový bod služby|
|/ serviceEndpointPolicies/zápisu|Vytvoří zásadu koncový bod služby nebo aktualizuje existující zásady koncový bod služby|
|/trafficManagerGeographicHierarchies/read|Získá obsahující oblastí, které se dají použít s metodu směrování provozu geografické hierarchii Geographic Traffic Manager|
|/trafficManagerProfiles/azureEndpoints/delete|Odstraní koncový bod Azure z existujícího profilu Správce provozu. Správce provozu se zastaví směrování provozu odstraněné koncový bod Azure.|
|/trafficManagerProfiles/azureEndpoints/read|Získá koncový bod Azure, který patří k profilu Traffic Manageru, včetně všechny vlastnosti tohoto koncového bodu Azure.|
|/trafficManagerProfiles/azureEndpoints/write|Přidat nový koncový bod Azure ve stávající profil správce provozu nebo aktualizujte vlastnosti existující koncový bod Azure v tomto profilu služby Traffic Manager.|
|/trafficManagerProfiles/delete|Odstraňte profil služby Traffic Manager. Všechna nastavení spojená s profilem správce provozu se ztratí a profil slouží už pro směrování provozu.|
|/trafficManagerProfiles/externalEndpoints/delete|Externí koncový bod se odstraní z existujícího profilu Správce provozu. Správce provozu se zastaví směrování provozu odstraněné externí koncový bod.|
|/trafficManagerProfiles/externalEndpoints/read|Získá externí koncový bod, který patří k profilu Traffic Manageru, včetně všechny vlastnosti tohoto externího koncového bodu.|
|/trafficManagerProfiles/externalEndpoints/write|Přidat nový externí koncový bod v existující profil Traffic Manageru nebo aktualizujte vlastnosti existující externí koncový bod v tomto profilu služby Traffic Manager.|
|/trafficManagerProfiles/heatMaps/read|Získá Heat mapa Traffic Manager pro daný profil Traffic Manageru, která obsahuje dotaz počty a latence data podle umístění a zdroj IP.|
|/trafficManagerProfiles/nestedEndpoints/delete|Odstraní koncový bod vnořené z existujícího profilu Správce provozu. Správce provozu se zastaví směrování provozu odstraněné vnořené koncový bod služby.|
|/trafficManagerProfiles/nestedEndpoints/read|Získá koncový vnořené, který patří k profilu Traffic Manageru, včetně všechny vlastnosti tohoto koncového bodu vnořený.|
|/trafficManagerProfiles/nestedEndpoints/write|Přidat nový koncový bod vnořené v existující profil Traffic Manageru nebo aktualizujte vlastnosti existující vnořené koncový bod v tomto profilu služby Traffic Manager.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky Traffic Manageru|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky Traffic Manager. Tato operace je doplněná poskytovatelem prostředků statistiky.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Získá události pro správce provozu|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro správce provozu.|
|/trafficManagerProfiles/read|Získání konfigurace profilu Správce provozu. To zahrnuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncového bodu a seznam koncových bodů směrovaných tímto profilem Traffic Manager.|
|/trafficManagerProfiles/write|Vytvoření profilu Správce provozu nebo úprava konfigurace existujícího profilu Správce provozu. To zahrnuje zapnutí nebo vypnutí profilu a změna nastavení DNS, nastavení směrování provozu nebo nastavení monitorování koncového bodu. Koncových bodů směrovaných tímto profilem správce provozu můžete přidat, odebrat, povolit nebo zakázat.|
|/trafficManagerUserMetricsKeys/delete|Odstraní úrovni předplatného je klíč používaný k shromažďování metrik uživatele v reálném čase.|
|/trafficManagerUserMetricsKeys/read|Získá úrovni předplatného klíč používaný pro shromažďování metrik uživatele v reálném čase.|
|/trafficManagerUserMetricsKeys/write|Vytvoří nový klíč úrovni předplatného, který se má použít pro shromažďování metrik uživatele v reálném čase.|
|nebo zrušit registraci nebo akce|Zruší registraci předplatného|
|/virtualHubs/delete|Odstraní virtuální rozbočovače|
|/virtualHubs/hubVirtualNetworkConnections/delete|Odstraní HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|Get a HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/write|Vytvořit nebo aktualizovat HubVirtualNetworkConnection|
|/virtualHubs/Read|Získat virtuální rozbočovače|
|/virtualHubs/write|Vytvořit nebo aktualizovat virtuální rozbočovače|
|/virtualnetworkgateways/Connections/Read|Get VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|Odstraní virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|Generovat balíček VpnClient pro virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnprofile/Action|Generovat VpnProfile balíček pro VirtualNetworkGateway|
|/virtualnetworkgateways/getadvertisedroutes/Action|Získá virtualNetworkGateway Inzerovat trasy|
|/virtualnetworkgateways/getbgppeerstatus/Action|Získá stav partnerského protokolu bgp virtualNetworkGateway|
|/virtualnetworkgateways/getlearnedroutes/Action|Získá virtualnetworkgateway naučili trasy|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Získávání parametrů Vpnclient Ipsec pro VirtualNetworkGateway P2S klienta.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Získá adresu URL profilu balíčku klienta vpn předem vygenerovaná|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení pro diagnostiku brány virtuální sítě|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky brány virtuální sítě, tato operace je doplněná poskytovatelem prostředků statistiky.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Získá události pro bránu virtuální sítě|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro bránu virtuální sítě|
|/virtualNetworkGateways/read|Získá VirtualNetworkGateway|
|/virtualnetworkgateways/reset/Action|Obnoví virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Nastavte parametry pro klienta VirtualNetworkGateway P2S Vpnclient Ipsec.|
|/virtualnetworkgateways/supportedvpndevices/action|Seznamy podporovaná zařízení Vpn|
|/virtualNetworkGateways/write|Vytvoří nebo aktualizuje VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|Zkontrolujte, jestli je k dispozici v zadané virtuální síti Ip adresy|
|/virtualNetworks/customViews/get/action|Získat vlastní zobrazení obsahu virtuální sítě|
|/virtualNetworks/customViews/read|Načtení definice zobrazení vlastní virtuální sítě|
|/virtualNetworks/delete|Odstraní virtuální sítě|
|/virtualNetworks/peer/action|Partnerský vztah virtuální síť s jinou virtuální sítí|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Získat nastavení diagnostiky virtuální sítě|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Vytvořit nebo aktualizovat nastavení diagnostiky virtuální sítě|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Získat definice protokolu virtuální sítě|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Získat definice metrik virtuální sítě|
|/virtualNetworks/read|Získat definici virtuální sítě|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Odstraní virtuální sítě partnerského vztahu proxy server|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Získá virtuální síť partnerský vztah definici proxy|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Proxy server partnerského vztahu virtuální sítě vytvoří nebo aktualizuje stávající virtuální síť partnerského vztahu proxy|
|/virtualNetworks/subnets/delete|Odstraní podsíť virtuální sítě|
|/virtualNetworks/subnets/join/action|Připojí virtuální sítě|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Spojí prostředků, jako je například účet úložiště nebo SQL database k podsíti.|
|/virtualNetworks/subnets/read|Získá definici podsítě virtuální sítě|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Odstraní prostředek navigační odkaz|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Načtení definice prostředku navigační odkaz|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Vytvoří navigační odkaz na prostředek nebo aktualizuje existující prostředek navigační odkaz|
|/virtualNetworks/subnets/virtualMachines/read|Získá odkazy na všechny virtuální počítače v podsíti virtuální sítě|
|/virtualNetworks/subnets/write|Vytvoří podsíť virtuální sítě nebo aktualizuje existující podsíť virtuální sítě|
|/virtualNetworks/taggedTrafficConsumers/delete|Odstraní s příznakem provoz příjemce|
|/virtualNetworks/taggedTrafficConsumers/read|Získat definici označené provoz příjemce|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Ověří s příznakem provoz příjemce|
|/virtualNetworks/taggedTrafficConsumers/write|Provoz příjemce označené vytvoří nebo aktualizuje existující příjemce provoz s příznakem|
|/virtualNetworks/usages/read|Získat IP použití pro každou podsíť virtuální sítě|
|/virtualNetworks/virtualMachines/read|Získá odkazy na všechny virtuální počítače ve virtuální síti|
|/virtualNetworks/virtualNetworkPeerings/delete|Odstraní virtuální sítě partnerského vztahu|
|/virtualNetworks/virtualNetworkPeerings/read|Získá definici partnerského vztahu virtuální sítě|
|/virtualNetworks/virtualNetworkPeerings/write|Vytvoří partnerský vztah virtuální sítě nebo aktualizuje existující virtuální síť partnerský vztah|
|/virtualNetworks/write|Vytvoří virtuální síť nebo aktualizuje existující virtuální síť|
|/virtualNetworkTaps/delete|Odstranit klepněte na virtuální síť|
|/virtualNetworkTaps/join/action|Spojí klepněte na virtuální síť|
|/virtualNetworkTaps/read|Získat klepněte na virtuální síť|
|/virtualNetworkTaps/write|Vytvořit nebo aktualizovat klepněte na virtuální síť|
|/virtualwans/delete|Odstranění a virtuální sítě Wan|
|/virtualwans/Read|GET a virtuální sítě Wan|
|/virtualWans/virtualHubProxies/delete|Odstraní proxy server virtuální rozbočovače|
|/virtualWans/virtualHubProxies/read|Získá definici proxy virtuální rozbočovače|
|/virtualWans/virtualHubProxies/write|Proxy server virtuální rozbočovače vytvoří nebo aktualizuje proxy server virtuální rozbočovače|
|/virtualwans/virtualHubs/Read|Získá všechny virtuální rozbočovače, které jsou přidružené k virtuální síti, která se Wan.|
|/virtualwans/vpnconfiguration/read|Získá konfiguraci sítě Vpn|
|/virtualWans/vpnSiteProxies/delete|Odstraní proxy serveru sítě Vpn|
|/virtualWans/vpnSiteProxies/read|Získá definici proxy serveru sítě Vpn|
|/virtualWans/vpnSiteProxies/write|Vytvoří proxy serveru sítě Vpn nebo aktualizuje proxy serveru sítě Vpn|
|/virtualwans/vpnSites/read|Získá všechny servery VPN, které jsou přidružené k virtuální síti, která se Wan.|
|/ virtualwans/zápisu|Vytvořit nebo aktualizovat virtuální Wan|
|/vpnGateways/read|Získá Brána VPN.|
|/vpnGateways/vpnConnections/read|Získá VpnConnection.|
|/vpnGateways/vpnConnections/write|Vloží VpnConnection.|
|/vpnGateways/write|Vloží Brána VPN.|
|/vpnsites/delete|Odstraní prostředek Vpn Site.|
|/vpnsites/read|Získá zdroj Vpn webu.|
|/vpnsites/write|Vytvoří nebo aktualizuje prostředek Vpn Site.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operace | Popis |
|---|---|
|/CheckNamespaceAvailability/action|Zkontroluje, jestli je ve službě NotificationHub k dispozici název prostředku oboru názvů, nebo ne.|
|/ Obory názvů nebo authorizationRules nebo akce|Získá seznam popisů autorizačních pravidel oboru názvů.|
|/Namespaces/authorizationRules/delete|Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit. |
|/Namespaces/authorizationRules/listkeys/action|Získat připojovací řetězec k oboru názvů|
|/ Obory názvů nebo authorizationRules/čtení|Získá seznam popisů autorizačních pravidel oboru názvů.|
|/Namespaces/authorizationRules/regenerateKeys/action|Autorizační pravidlo oboru názvů – opětovné vygenerování primárního nebo sekundárního klíče. Zadejte klíč, který je nutné znovu vygenerovat.|
|/Namespaces/authorizationRules/write|Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat.|
|/Namespaces/CheckNotificationHubAvailability/action|Zkontroluje, jestli je v oboru názvů k dispozici název NotificationHub, nebo ne.|
|/Namespaces/Delete|Odstraní prostředek oboru názvů.|
|/Namespaces/NotificationHubs/authorizationRules/action|Získá seznam autorizačních pravidel Centra oznámení.|
|/Namespaces/NotificationHubs/authorizationRules/delete|Odstranit autorizační pravidla Centra oznámení|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Získá připojovací řetězec k Centru oznámení.|
|/Namespaces/NotificationHubs/authorizationRules/read|Získá seznam autorizačních pravidel Centra oznámení.|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Autorizační pravidlo Centra oznámení – opětovné vygenerování primárního nebo sekundárního klíče. Zadejte klíč, který je nutné znovu vygenerovat.|
|/Namespaces/NotificationHubs/authorizationRules/write|Vytvoření pravidla autorizace centra oznámení a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat.|
|/ Obory názvů nebo NotificationHubs/debugSend nebo akce|Poslat testovací nabízené oznámení|
|/Namespaces/NotificationHubs/Delete|Odstraní prostředek Centra oznámení.|
|/ Obory názvů nebo NotificationHubs/metricDefinitions/čtení|Získání seznamu Namespace metrik popisy prostředků|
|/Namespaces/NotificationHubs/pnsCredentials/action|Získáte všechna pověření systému PNS centra oznámení. To zahrnuje, přihlašovací údaje WNS, MPNS, APNS, GCM a Baidu|
|/Namespaces/NotificationHubs/read|Získá seznam popisů prostředků Centra oznámení.|
|/Namespaces/NotificationHubs/write|Vytvoření centra oznámení a aktualizujte jeho vlastnosti. Jeho vlastnosti především zahrnovat přihlašovací údaje systému PNS. Autorizační pravidla a hodnota TTL|
|/Namespaces/read|Získá seznam popisů prostředku oboru názvů.|
|/Namespaces/write|Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat.|
|/ registrace nebo akce|Zaregistruje předplatné u poskytovatele prostředků NotificationHubs a povolí vytváření oborů názvů a NotificationHubs.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operace | Popis |
|---|---|
|/linkTargets/read|Zobrazí seznam stávajících účtů, které nejsou přidružené předplatné Azure. Toto předplatné odkaz na pracovním prostoru, použijte id zákazníka vrácená touto operací ve vlastnosti id zákazníka operace vytvořit pracovní prostor.|
|/ registrace nebo akce|Registrujte předplatné zprostředkovatele prostředků.|
|/workspaces/analytics/query/action|Vyhledávání pomocí nový modul.|
|/workspaces/analytics/query/schema/read|Načíst schéma vyhledávání V2.|
|/workspaces/api/query/action|Vyhledávání pomocí nový modul.|
|/workspaces/api/query/schema/read|Načíst schéma vyhledávání V2.|
|/workspaces/configurationScopes/DELETE|Odstranit konfigurace oboru|
|/workspaces/configurationScopes/Read|Získání konfigurace oboru|
|/workspaces/configurationScopes/Write|Nastavení konfigurace oboru|
|/workspaces/Datasources/DELETE|Odstraňte zdrojů dat v pracovním prostoru.|
|/workspaces/Datasources/Read|Získáte zdrojů dat v pracovním prostoru.|
|/workspaces/Datasources/Write|Vytvořit nebo aktualizovat zdroje dat v pracovním prostoru.|
|/workspaces/DELETE|Odstraní pracovního prostoru. Pokud byl pracovním prostoru propojený k existujícímu pracovnímu prostoru v okamžiku vytvoření není odstranit pracovní prostor, který byl propojen s.|
|/workspaces/generateregistrationcertificate/Action|Generuje registrační certifikát pro pracovní prostor. Tento certifikát se používá k připojení do pracovního prostoru Microsoft System Center Operation Manager.|
|/workspaces/intelligencepacks/disable/action|Zakáže intelligence pack pro daný pracovní prostor.|
|/workspaces/intelligencepacks/enable/action|Umožňuje intelligence pack pro daný pracovní prostor.|
|/workspaces/intelligencepacks/read|Obsahuje seznam všech intelligence Pack, které jsou viditelné pro danou worksapce a také uvádí, jestli je sada povolit nebo zakázat pracovního prostoru.|
|/workspaces/linkedServices/delete|Odstranění propojené služby v rámci daného pracovního prostoru.|
|/workspaces/linkedServices/read|Získat propojené služby v rámci zadané pracovního prostoru.|
|/workspaces/linkedServices/write|Vytvořit nebo aktualizovat propojené služby v rámci daného pracovního prostoru.|
|/workspaces/listKeys/action|Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru.|
|/workspaces/listKeys/read|Načte seznam klíčů pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru.|
|/workspaces/managementGroups/Read|Získá názvy a metadat pro připojení k tomuto pracovnímu prostoru skupiny pro správu System Center Operations Manager.|
|/workspaces/metricDefinitions/Read|Získat definice metrika v části pracovní prostor|
|/workspaces/notificationSettings/delete|Odstraňte uživatele nastavení oznámení pro pracovní prostor.|
|/workspaces/notificationSettings/read|Získáte uživatelské nastavení oznámení pro pracovní prostor.|
|/workspaces/notificationSettings/write|Nastavte uživatele nastavení oznámení pro pracovní prostor.|
|/workspaces/PURGE/Action|Odstranit zadaná data z pracovního prostoru|
|/workspaces/Read|Získá existujícímu pracovnímu prostoru|
|/workspaces/savedSearches/delete|Odstraní uložené vyhledávací dotaz.|
|/workspaces/savedSearches/Read|Získá uložené vyhledávací dotaz.|
|/workspaces/savedSearches/write|Vytvoří uložené vyhledávací dotaz.|
|/workspaces/schema/read|Získá schéma vyhledávání pro pracovní prostor.  Hledání schématu zahrnuje zveřejněné pole a jejich typy.|
|/workspaces/Search/Action|Provede vyhledávací dotaz.|
|/workspaces/sharedKeys/action|Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru.|
|/workspaces/sharedKeys/read|Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru.|
|/workspaces/storageinsightconfigs/delete|Odstraní konfigurace úložiště. To se zastaví statistice provozu Microsoft z čtení dat z účtu úložiště.|
|/workspaces/storageinsightconfigs/read|Získá konfiguraci úložiště.|
|/workspaces/storageinsightconfigs/write|Vytvoří novou konfiguraci úložiště. Tyto konfigurace se používají k načítání dat z umístění, do stávající účet úložiště.|
|/workspaces/usages/Read|Získá data o využití pro pracovní prostor, včetně množství dat číst v pracovním prostoru.|
|/ pracovních prostorů a zápis|Vytvoří nový pracovní prostor nebo odkazy k existujícímu pracovnímu prostoru tím, že poskytuje id zákazníka z existující pracovního prostoru.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operace | Popis |
|---|---|
|/managementAssociations/delete|Odstraňte existující přidružení sady Management|
|/managementAssociations/read|Získat existující přidružení sady Management|
|/managementAssociations/write|Vytvoření nového přidružení správy|
|/managementConfigurations/delete|Odstraňte existující Configuratin správy|
|/managementConfigurations/Read|Získat existující konfigurace správy|
|/ managementConfigurations/zápisu|Vytvořit novou konfiguraci správy|
|/ registrace nebo akce|Registrujte předplatné zprostředkovatele prostředků.|
|/Solutions/DELETE|Odstraňte existující řešení OMS|
|/Solutions/Read|Získat ukončení OMS řešení|
|/ řešení a zápis|Vytvořte nové řešení OMS|

## <a name="microsoftportal"></a>Microsoft.Portal

| Operace | Popis |
|---|---|
|/Dashboards/DELETE|Odebere z předplatného řídicí panel.|
|/Dashboards/Read|Čte řídicí panely pro předplatné.|
|/ řídicí panely a zápis|Umožňuje přidat nebo upravit řídicí panely u předplatného.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Operace | Popis |
|---|---|
|/capacities/checkNameAvailability/action|Kontroluje, zda daný kapacity vyhrazené Power BI je platný název a ne v používání.|
|/capacities/DELETE|Odstranění Power BI vyhrazené kapacity.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro Power BI vyhrazené kapacity.|
|/capacities/Read|Načte informace o zadané Power BI vyhrazené kapacity.|
|/ kapacity a zápis|Vytvoří nebo aktualizuje zadaný Power BI vyhrazené kapacitu.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operace | Popis |
|---|---|
|/Locations/allocatedStamp/Read|GetAllocatedStamp je interní operace, kterou používá služba|
|/Locations/allocateStamp/Action|AllocateStamp je interní operace, které používá služba|
|/Locations/backupPreValidateProtection/Action||
|/Locations/backupStatus/Action|Zkontrolujte stav zálohování trezory služeb zotavení|
|/locations/backupValidateFeatures/action|Ověřit funkce|
|/Operations/Read|Operace vrátí seznam operací pro poskytovatele prostředků|
|/ registrace nebo akce|Zaregistruje předplatné pro danou poskytovatele prostředků|
|/Vaults/backupconfig/read|Vrátí konfiguraci pro obnovení služeb trezoru.|
|/Vaults/backupconfig/write|Konfigurace aktualizací pro obnovení služeb trezoru.|
|/ Trezory/backupEngines/čtení|Vrátí všechny servery správy zálohování zaregistrované s trezorem.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Získat všechny položky v kontejneru|
|/Vaults/backupFabrics/backupProtectionIntent/write|Vytvoření zálohy záměr ochrany|
|/Vaults/backupFabrics/operationResults/read|Vrátí stav operace|
|/Vaults/backupFabrics/protectableContainers/read|Získání všech chránitelné kontejnery|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Proveďte dotaz pro úlohy v rámci kontejneru|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Načte výsledky operace provedené na kontejneru ochrany.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Provede zálohování chráněné položky.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Odstranění chráněné položky|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Načte výsledky operace provedené na chráněných položkách.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Načte stav operace provedené na chráněných položkách.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Vrátí podrobnosti o objektu chráněné položky|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Zřízení rychlých položky obnovení pro chráněné položky|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Načíst body obnovení pro chráněné položky|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Obnoví body obnovení pro chráněné položky|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Odvolat rychlých položky obnovení pro chráněné položky|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Vytvoření zálohy chráněné položky|
|/Vaults/backupFabrics/protectionContainers/read|Vrátí všechny registrované kontejnery|
|/Vaults/backupFabrics/protectionContainers/write|Vytvoří kontejner registrovaných|
|/Vaults/backupFabrics/refreshContainers/action|Aktualizuje seznam kontejneru|
|/ Trezory/backupJobs nebo zrušit nebo akce|Zrušení úlohy|
|/Vaults/backupJobs/operationResults/read|Vrátí výsledek operace úlohy.|
|/ Trezory/backupJobs/čtení|Vrátí všechny objekty úlohy|
|/Vaults/backupJobsExport/action|Export úloh|
|/Vaults/backupJobsExport/operationResults/read|Vrátí výsledek operace úlohy exportu.|
|/Vaults/backupManagementMetaData/read|Vrátí metadata správy zálohování trezoru Recovery Services.|
|/Vaults/backupOperationResults/read|Vrátí výsledek operace zálohování trezoru Recovery Services.|
|/Vaults/backupOperations/read|Operace zálohování vrátí stav pro obnovení služeb trezoru.|
|/Vaults/backupPolicies/delete|Odstranění zásady ochrany|
|/Vaults/backupPolicies/operationResults/read|Načte výsledky operace zásad.|
|/Vaults/backupPolicies/operations/read|Načíst stav operace zásad.|
|/Vaults/backupPolicies/read|Vrátí všechny zásady ochrany|
|/Vaults/backupPolicies/write|Vytvoří zásadu ochrany|
|/Vaults/backupProtectableItems/read|Vrátí seznam chránitelných položek.|
|/Vaults/backupProtectedItems/read|Vrátí seznam všech chráněných položek.|
|/Vaults/backupProtectionContainers/read|Vrátí všechny kontejnery, které patří k předplatnému|
|/Vaults/backupSecurityPIN/action|Vrátí zabezpečení PIN kódu informace pro obnovení služeb trezoru.|
|/ Trezory/backupstorageconfig/čtení|Vrátí konfiguraci úložiště pro obnovení služeb trezoru.|
|A trezory/backupstorageconfig/zápis|Aktualizace konfiguraci úložiště pro obnovení služeb trezoru.|
|/Vaults/backupUsageSummaries/read|Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení.|
|A trezory/certifikátů/zápis|Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů.|
|Trezory nebo odstranění|Operace odstranění trezoru Odstraní zadaný prostředek Azure typu 'trezoru.|
|/Vaults/extendedInformation/delete|Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor.|
|/Vaults/extendedInformation/read|Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor.|
|/Vaults/extendedInformation/write|Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor.|
|/Vaults/monitoringAlerts/read|Získá výstrahy pro trezor služeb zotavení.|
|/Vaults/monitoringAlerts/write|Vyřeší výstrahy.|
|/ Trezory/monitoringConfigurations/čtení|Získá konfigurace oznámení trezoru služby pro obnovení.|
|A trezory/monitoringConfigurations/zápis|Nakonfiguruje e-mailová oznámení do trezoru služeb zotavení.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Zálohování Azure Diagnostics|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Zálohování Azure Diagnostics|
|/Vaults/providers/Microsoft.Insights/logDefinitions/Read|Azure protokoly zálohování|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure Backup metriky|
|/ Trezory/čtení|Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru.|
|/Vaults/registeredIdentities/delete|Zrušit registraci kontejneru operaci lze použít se zrušit registraci kontejneru.|
|/Vaults/registeredIdentities/operationResults/read|Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci|
|/Vaults/registeredIdentities/read|Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek.|
|/Vaults/registeredIdentities/write|Operaci zaregistrovat kontejneru služby lze použít k registraci kontejner službou obnovení.|
|/vaults/replicationAlertSettings/read|Číst všechny nastavení výstrah|
|/vaults/replicationAlertSettings/write|Vytvořit nebo aktualizovat nastavení výstrah|
|/vaults/replicationEvents/Read|Číst všechny události|
|/vaults/replicationFabrics/checkConsistency/action|Zkontroluje konzistenci prostředí infrastruktury.|
|/vaults/replicationFabrics/delete|Odstranit všechny prostředky infrastruktury|
|/vaults/replicationFabrics/deployProcessServerImage/action|Nasadit Image procesového serveru|
|/vaults/replicationFabrics/read|Číst všechny prostředky infrastruktury|
|/vaults/replicationFabrics/reassociateGateway/action|Přidružení brány|
|/vaults/replicationFabrics/remove/action|Odebrání infrastruktury|
|/vaults/replicationFabrics/renewcertificate/action|Obnovit certifikát pro prostředky infrastruktury|
|/vaults/replicationFabrics/replicationNetworks/read|Přečtěte si žádné sítě.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Odstranit všechny mapování sítě|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Číst veškerá jeho mapování sítě|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Vytvořit nebo aktualizovat veškerá jeho mapování sítě|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Zjistit Chránitelná položka|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Číst všechny kontejnery ochrany|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Kontejner ochrany odebrat.|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/čtení|Číst všechny položky, které jsou předmětem ochrany|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint nebo akce|Použít bod obnovení|
|/ trezory, replicationFabrics/replicationProtectionContainers, replicationProtectedItems nebo odstranění|Odstranit všechny chráněné položky|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit nebo akce|Potvrzení převzetí služeb při selhání|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover nebo akce|Plánované převzetí služeb při selhání|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/čtení|Číst všechny chráněné položky|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/čtení|Číst všechny body obnovení replikace|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems, odebrat nebo akce|Odebrat chráněné položky|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication nebo akce|Oprava replikace|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems nebo opětovné ochrany nebo akce|Znovu aktivujte ochranu chráněné položky|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover nebo akce|Testovací převzetí služeb při selhání|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup nebo akce|Vyčistit testovací převzetí služeb při selhání|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover nebo akce|Převzetí služeb při selhání|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Aktualizace služby Mobility|
|/ trezory a replicationFabrics/replicationProtectionContainers/replicationProtectedItems/zápis|Vytvořit nebo aktualizovat všechny chráněné položky|
|/ trezory, replicationFabrics/replicationProtectionContainers, replicationProtectionContainerMappings nebo odstranění|Odstranit veškerá jeho mapování kontejnerů ochrany|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/čtení|Číst veškerá jeho mapování kontejnerů ochrany|
|/ trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings, odebrat nebo akce|Odebrat mapování kontejnerů ochrany|
|/ trezory a replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/zápis|Vytvořit nebo aktualizovat veškerá jeho mapování kontejnerů ochrany|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Kontejner ochrany přepínače|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Vytvořit nebo aktualizovat všechny kontejnery ochrany|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Odstranit zprostředkovatelů služby obnovení|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Přečtěte si zprostředkovatelů služby obnovení|
|/ trezory/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider nebo akce|Aktualizujte zprostředkovatele|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Odebrat zprostředkovatele služeb zotavení|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Vytvořit nebo aktualizovat zprostředkovatelů služby obnovení|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Číst všechny klasifikace úložiště|
|/ trezory, replicationFabrics/replicationStorageClassifications, replicationStorageClassificationMappings nebo odstranění|Odstranit veškerá jeho mapování klasifikace úložiště|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Číst veškerá jeho mapování klasifikace úložiště|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Vytvořit nebo aktualizovat veškerá jeho mapování klasifikace úložiště|
|/vaults/replicationFabrics/replicationvCenters/delete|Odstranit všechny úlohy|
|/vaults/replicationFabrics/replicationvCenters/read|Číst všechny úlohy|
|/vaults/replicationFabrics/replicationvCenters/write|Vytvořit nebo aktualizovat všechny úlohy|
|/vaults/replicationFabrics/write|Vytvořit nebo aktualizovat všechny prostředky infrastruktury|
|/vaults/replicationJobs/Cancel/Action|Zrušení úlohy|
|/vaults/replicationJobs/Read|Číst všechny úlohy|
|/vaults/replicationJobs/restart/Action|Restartujte úlohu|
|/vaults/replicationJobs/Resume/Action|Obnovení úlohy|
|/vaults/replicationPolicies/delete|Odstranit všechny zásady|
|/vaults/replicationPolicies/Read|Číst všechny zásady|
|/vaults/replicationPolicies/write|Vytvořit nebo aktualizovat všechny zásady|
|/vaults/replicationRecoveryPlans/delete|Odstranit všechny plány obnovení|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plán obnovení potvrzení převzetí služeb při selhání|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plán obnovení plánované převzetí služeb při selhání|
|/vaults/replicationRecoveryPlans/read|Číst všechny plány obnovení|
|/vaults/replicationRecoveryPlans/reProtect/action|Znovu nastavte ochranu plánu obnovení|
|/vaults/replicationRecoveryPlans/testFailover/action|Testovací převzetí služeb při selhání obnovení plán|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Plán obnovení testovací převzetí služeb při selhání čištění|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plán obnovení převzetí služeb při selhání|
|/vaults/replicationRecoveryPlans/write|Vytvořit nebo aktualizovat všechny plány obnovení|
|/Vaults/tokenInfo/read|Vrátí token informace o trezoru služeb zotavení.|
|/vaults/usages/Read|Číst všechny použití trezoru|
|/ Trezory nebo použití/čtení|Vrátí podrobnosti využití trezoru Recovery Services.|
|/Vaults/vaultTokens/read|Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end.|
|A trezory/zápis|Operace Vytvořit trezor vytvoří prostředek Azure typu trezor.|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operace | Popis |
|---|---|
|/checkNameAvailability/action|Zkontroluje dostupnost oboru názvů v daném předplatném.|
|/checkNamespaceAvailability/action|Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. místo toho použijte CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/Action|Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je depricated. Se aktualizovat autorizační pravidlo Namespace místo toho použijte volání PUT... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/authorizationRules/delete|Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit. |
|/namespaces/authorizationRules/listkeys/action|Získat připojovací řetězec k oboru názvů|
|/namespaces/authorizationRules/read|Získá seznam popisů autorizačních pravidel oboru názvů.|
|/namespaces/authorizationRules/regenerateKeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/authorizationRules/Write|Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat.|
|/namespaces/Delete|Odstraní prostředek oboru názvů.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Získá povolení pravidla klíče pro zotavení po havárii primární obor názvů|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Získání po havárii obnovení primární Namespace pro autorizační pravidla|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Zkontroluje dostupnost alias oboru názvů v rámci daného předplatného.|
|/namespaces/disasterRecoveryConfigs/delete|Odstraní konfigurace zotavení po havárii přidružené k oboru názvů. Tato operace může volat jenom přes primární obor názvů.|
|/namespaces/disasterRecoveryConfigs/failover/action|Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor.|
|/namespaces/disasterRecoveryConfigs/read|Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů.|
|/namespaces/disasterRecoveryConfigs/write|Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů.|
|/namespaces/HybridConnections/authorizationRules/Action|Operace k aktualizaci HybridConnection. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo.|
|/namespaces/HybridConnections/authorizationRules/delete|Operace odstranění HybridConnection autorizační pravidla|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Získat připojovací řetězec k HybridConnection|
|/namespaces/HybridConnections/authorizationRules/Read| Získání seznamu HybridConnection autorizační pravidla|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/Action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/HybridConnections/authorizationRules/Write|Vytvořte HybridConnection autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat.|
|/namespaces/HybridConnections/DELETE|Operace odstranění HybridConnection prostředků|
|/namespaces/HybridConnections/Read|Získání seznamu popisů HybridConnection prostředků|
|/namespaces/HybridConnections/Write|Vytvoření nebo aktualizace HybridConnection vlastnosti.|
|/namespaces/messagingPlan/Read|Získá plánování zasílání zpráv pro obor názvů. Toto rozhraní API je zastaralá. Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/messagingPlan/Write|Aktualizace, zasílání zpráv plánování pro obor názvů. Toto rozhraní API je zastaralá. Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/operationresults/Read|Získá stav operace Namespace.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Získání seznamu Namespace metrik popisy prostředků|
|/namespaces/read|Získá seznam popisů prostředku oboru názvů.|
|/namespaces/WcfRelays/authorizationRules/action|Operace k aktualizaci WcfRelay. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo.|
|/namespaces/WcfRelays/authorizationRules/delete|Operace odstranění WcfRelay autorizační pravidla|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Získat připojovací řetězec k WcfRelay|
|/namespaces/WcfRelays/authorizationRules/read| Získání seznamu WcfRelay autorizační pravidla|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/WcfRelays/authorizationRules/Write|Vytvořte WcfRelay autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat.|
|/namespaces/WcfRelays/Delete|Operace odstranění WcfRelay prostředků|
|/namespaces/WcfRelays/read|Získání seznamu popisů WcfRelay prostředků|
|/namespaces/WcfRelays/write|Vytvoření nebo aktualizace WcfRelay vlastnosti.|
|/ obory názvů a zápis|Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat.|
|/Operations/Read|Získejte operace|
|/ registrace nebo akce|Zaregistruje předplatné u poskytovatele prostředků Relay a povolí vytváření prostředků Relay.|
|nebo zrušit registraci nebo akce|Zaregistruje předplatné u poskytovatele prostředků Relay a povolí vytváření prostředků Relay.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operace | Popis |
|---|---|
|/ AvailabilityStatuses/aktuální/čtení|Získá stav dostupnosti pro zadaný prostředek.|
|/ AvailabilityStatuses/čtení|Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu.|
|/ healthevent nebo akce|Označuje změnu ve stavu zadaného prostředku.|
|/healthevent/activated/Action|Označuje změnu ve stavu zadaného prostředku.|
|/healthevent/InProgress/Action|Označuje změnu ve stavu zadaného prostředku.|
|/healthevent/Pending/Action|Označuje změnu ve stavu zadaného prostředku.|
|/healthevent/Resolved/Action|Označuje změnu ve stavu zadaného prostředku.|
|/healthevent/Updated/Action|Označuje změnu ve stavu zadaného prostředku.|
|/ registrace nebo akce|Zaregistruje předplatné do služby Microsoft Resource Health.|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operace | Popis |
|---|---|
|/checkResourceName/action|Zkontroluje správnost názvu prostředku.|
|/Deployments/Cancel/Action|Zruší nasazení.|
|/Deployments/DELETE|Odstraní nasazení.|
|/Deployments/Operations/Read|Načte nebo vypíše operace nasazení.|
|/Deployments/Read|Načte nebo vypíše nasazení.|
|/Deployments/Validate/Action|Ověří nasazení.|
|/ nasazení a zápis|Vytvoří nebo aktualizuje nasazení.|
|/links/delete|Odstraní odkaz na prostředek.|
|/Links/Read|Načte nebo vypíše odkazy na prostředek.|
|/ odkazy a zápis|Vytvoří nebo aktualizuje odkaz na prostředek.|
|/Marketplace/Purchase/Action|Umožňuje koupit prostředek z Marketplace.|
|/providers/Read|Načte seznam zprostředkovatelů.|
|/Resources/Read|Načte seznam prostředků na základě filtrů.|
|/subscriptions/Locations/Read|Získá výpis podporovaných umístění.|
|/subscriptions/operationresults/Read|Načte výsledky operací s předplatným.|
|/subscriptions/providers/Read|Načte nebo vypíše zprostředkovatele prostředků.|
|/subscriptions/Read|Získá výpis předplatných.|
|/subscriptions/resourceGroups/delete|Odstraní skupinu prostředků a všechny její prostředky.|
|/subscriptions/resourcegroups/Deployments/Operations/Read|Načte nebo vypíše operace nasazení.|
|/subscriptions/resourcegroups/Deployments/operationstatuses/Read|Získá nebo vypíše stavy operace nasazení.|
|/subscriptions/resourcegroups/Deployments/Read|Načte nebo vypíše nasazení.|
|/subscriptions/resourcegroups/Deployments/Write|Vytvoří nebo aktualizuje nasazení.|
|/subscriptions/resourceGroups/moveResources/action|Přesune prostředky z jedné skupiny prostředků do jiné.|
|/subscriptions/resourceGroups/read|Načte nebo vypíše skupinu prostředků.|
|/subscriptions/resourcegroups/Resources/Read|Načte prostředky pro skupinu prostředků.|
|/subscriptions/resourceGroups/validateMoveResources/action|Ověří přesun prostředků z jedné skupiny prostředků do jiné.|
|/subscriptions/resourceGroups/write|Vytvoří nebo aktualizuje skupinu prostředků.|
|/subscriptions/Resources/Read|Získá prostředky předplatného.|
|/subscriptions/tagNames/delete|Odstraní značku předplatného.|
|/subscriptions/tagNames/Read|Načte nebo vypíše značky předplatného.|
|/subscriptions/tagNames/tagValues/DELETE|Odstraní hodnotu značky předplatného.|
|/subscriptions/tagNames/tagValues/Read|Načte nebo vypíše hodnoty značky předplatného.|
|/subscriptions/tagNames/tagValues/Write|Přidá hodnotu značky předplatného.|
|/subscriptions/tagNames/write|Přidá značku předplatného.|
|/tenants/Read|Získá výpis klientů.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operace | Popis |
|---|---|
|/jobcollections/DELETE|Odstraní kolekci úloh.|
|/jobcollections/disable/Action|Vypne kolekci úloh.|
|/jobcollections/enable/Action|Zapne kolekci úloh.|
|/jobcollections/Jobs/DELETE|Odstraní úlohu.|
|/jobcollections/Jobs/generateLogicAppDefinition/Action|Vygeneruje definici Aplikace logiky na základě úlohy Scheduleru.|
|/jobcollections/Jobs/jobhistories/Read|Načte historii úlohy.|
|/jobcollections/Jobs/Read|Načte úlohu.|
|/jobcollections/Jobs/Run/Action|Spustí úlohu.|
|/jobcollections/Jobs/Write|Vytvoří nebo aktualizuje úlohu.|
|/jobcollections/Read|Získat kolekci úloh|
|/ kolekce a zápis|Vytvoří nebo aktualizuje kolekci úloh.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operace | Popis |
|---|---|
|/checkNameAvailability/action|Zkontroluje dostupnost názvu služby.|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele vyhledávání prostředků a umožňuje vytvoření služby vyhledávání.|
|/searchServices/createQueryKey/action|Vytvoří klíč dotazu.|
|/searchServices/delete|Odstraní službu vyhledávání.|
|/searchServices/diagnosticSettings/read|Získá nastavení pro čtení pro prostředek diganostic|
|/searchServices/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diganostic pro prostředek.|
|/searchServices/listAdminKeys/action|Načte klíče správce.|
|/searchServices/logDefinitions/Read|Získá dostupné protokoly pro službu vyhledávání.|
|/searchServices/metricDefinitions/read|Načte dostupné metriky pro službu vyhledávání.|
|/searchServices/queryKey/delete|Odstraní klíč dotazu.|
|/searchServices/queryKey/read|Načte klíče dotazu.|
|/searchServices/read|Načte službu vyhledávání.|
|/searchServices/regenerateAdminKey/action|Znovu vygeneruje klíč správce.|
|/searchServices/start/action|Spustí službu vyhledávání.|
|/searchServices/stop/action|Zastaví službu vyhledávání.|
|/searchServices/write|Vytvoří nebo aktualizuje službu vyhledávání.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operace | Popis |
|---|---|
|/alerts/read|Získá všechny výstrahy zabezpečení k dispozici|
|/applicationWhitelistings/read|Získá whitelistings aplikace|
|/applicationWhitelistings/write|Vytvoří nové vytvoření seznamu povolených aplikací nebo aktualizuje existující|
|/complianceResults/Read|Získá výsledky dodržování shody pro prostředek|
|/Locations/Alerts/Activate/Action|Aktivovat výstrahy zabezpečení|
|/Locations/Alerts/dismiss/Action|Zamítnutí výstrahy zabezpečení|
|/Locations/Alerts/Read|Získá všechny výstrahy zabezpečení k dispozici|
|/locations/jitNetworkAccessPolicies/initiate/action|Inicializuje zásady přístupu k síti za běhu|
|/locations/jitNetworkAccessPolicies/read|Získá zásad přístupu k síti za běhu|
|/locations/jitNetworkAccessPolicies/write|Vytvoří nové zásady přístupu k síti za běhu nebo aktualizuje existující|
|/Locations/Read|Získá umístění zabezpečení dat|
|/Locations/Tasks/Activate/Action|Aktivovat doporučení zabezpečení|
|/locations/tasks/dismiss/action|Zavření doporučení zabezpečení|
|/Locations/Tasks/Read|Získá všechny dostupné zabezpečení doporučení|
|/Locations/Tasks/Resolve/Action|Vyřešte doporučení zabezpečení|
|/Locations/Tasks/Start/Action|Spustit doporučení zabezpečení|
|/Policies/Read|Získá zásady zabezpečení|
|/ Zásady/zápisu|Aktualizace nastavení zásad zabezpečení|
|/pricings/DELETE|Odstraní cenovou nastavení oboru|
|/pricings/Read|Získá cenovou nastavení oboru|
|/ cen daných předmětů a zápis|Aktualizace nastavení cenové pro obor|
|/ registrace nebo akce|Zaregistruje předplatné pro Azure Security Center|
|/securityContacts/DELETE|Odstraní zabezpečení kontakt|
|/securityContacts/Read|Získá kontakt zabezpečení|
|/ securityContacts/zápisu|Aktualizace zabezpečení kontakt|
|/securitySolutions/DELETE|Odstraní zabezpečení řešení|
|/securitySolutions/Read|Získá řešení zabezpečení|
|/ securitySolutions/zápisu|Vytvoří nové řešení zabezpečení nebo aktualizuje existující|
|/securitySolutionsReferenceData/read|Získá referenční data řešení zabezpečení|
|/securityStatuses/Read|Získá stavy stavu zabezpečení prostředků Azure|
|/securityStatusesSummaries/Read|Získá zabezpečení souhrny stavů pro obor|
|/Tasks/Read|Získá všechny dostupné zabezpečení doporučení|
|/webApplicationFirewalls/delete|Odstraní brány firewall webových aplikací|
|/webApplicationFirewalls/read|Získá webové aplikace brány firewall|
|/webApplicationFirewalls/write|Vytvoří nové brány firewall webových aplikací nebo aktualizuje existující|
|/workspaceSettings/Connect/Action|Změňte nastavení obnovení nastavení pracovního prostoru|
|/workspaceSettings/DELETE|Odstraní nastavení pracovního prostoru|
|/workspaceSettings/read|Získá nastavení pracovního prostoru|
|/workspaceSettings/write|Aktualizace nastavení pracovního prostoru|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operace | Popis |
|---|---|
|/checkNameAvailability/action|Zkontroluje dostupnost oboru názvů v daném předplatném.|
|/checkNamespaceAvailability/action|Zkontroluje dostupnost oboru názvů v daném předplatném. Toto rozhraní API je zastaralé. místo toho použijte CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/Action|Aktualizace Namespace autorizační pravidlo. Toto rozhraní API je depricated. Se aktualizovat autorizační pravidlo Namespace místo toho použijte volání PUT... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/authorizationRules/delete|Odstraňte Namespace autorizační pravidlo. Výchozí Namespace autorizační pravidlo nelze odstranit. |
|/namespaces/authorizationRules/listkeys/action|Získat připojovací řetězec k oboru názvů|
|/namespaces/authorizationRules/read|Získá seznam popisů autorizačních pravidel oboru názvů.|
|/namespaces/authorizationRules/regenerateKeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/authorizationRules/Write|Autorizační pravidla úrovni Namespace vytvořit a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva, primární a sekundární klíče lze aktualizovat.|
|/namespaces/Delete|Odstraní prostředek oboru názvů.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Získá povolení pravidla klíče pro zotavení po havárii primární obor názvů|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Získání po havárii obnovení primární Namespace pro autorizační pravidla|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Zakáže Zotavení po havárii a zastaví replikaci změn z primárních oborů názvů do sekundárních.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Zkontroluje dostupnost alias oboru názvů v rámci daného předplatného.|
|/namespaces/disasterRecoveryConfigs/delete|Odstraní konfigurace zotavení po havárii přidružené k oboru názvů. Tato operace může volat jenom přes primární obor názvů.|
|/namespaces/disasterRecoveryConfigs/failover/action|Vyvolá převzetí služeb při selhání GEO DR a změní konfiguraci aliasu oboru názvů tak, aby vedl na sekundární obor.|
|/namespaces/disasterRecoveryConfigs/read|Získá konfiguraci Zotavení po havárii přidruženou k danému oboru názvů.|
|/namespaces/disasterRecoveryConfigs/write|Vytvoří nebo aktualizuje konfiguraci Zotavení po havárii přidruženou k danému oboru názvů.|
|/namespaces/eventGridFilters/delete|Odstraní filtr událostí mřížky přidružený k oboru názvů.|
|/namespaces/eventGridFilters/read|Získá filtr událostí mřížky přidružený k oboru názvů.|
|/namespaces/eventGridFilters/write|Vytvoří nebo aktualizuje filtr událostí mřížky přidružený k oboru názvů.|
|/namespaces/eventhubs/Read|Získání seznamu popisů EventHub prostředků|
|/namespaces/messagingPlan/Read|Získá plánování zasílání zpráv pro obor názvů. Toto rozhraní API je zastaralá. Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/messagingPlan/Write|Aktualizace, zasílání zpráv plánování pro obor názvů. Toto rozhraní API je zastaralá. Vlastnosti, které jsou zveřejňovány prostřednictvím prostředků MessagingPlan přesunou do (nadřazené) Namespace prostředků v novějších verzích rozhraní API... Tato operace není podporována na volání rozhraní API verze 2017-04-01.|
|/namespaces/Migrate/Action|Operace migrace oboru názvů|
|/namespaces/operationresults/Read|Získá stav operace Namespace.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Získání seznamu popisů Namespace nastavení pro diagnostiku prostředků|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Získání seznamu protokolů Namespace popisy prostředků|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Získání seznamu Namespace metrik popisy prostředků|
|/namespaces/queues/authorizationRules/action|Operace k aktualizaci fronty. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo.|
|/namespaces/queues/authorizationRules/delete|Operace odstranění fronty autorizační pravidla|
|/namespaces/queues/authorizationRules/listkeys/action|Získat připojovací řetězec do fronty|
|/namespaces/queues/authorizationRules/read| Získání seznamu fronty autorizační pravidla|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/queues/authorizationRules/write|Vytvoření fronty autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat.|
|/namespaces/queues/Delete|Operace odstranění fronty prostředků|
|/namespaces/queues/read|Získání seznamu popisů fronty prostředků|
|/namespaces/queues/write|Vytvoření nebo aktualizace fronty vlastnosti.|
|/namespaces/read|Získá seznam popisů prostředku oboru názvů.|
|/namespaces/topics/authorizationRules/Action|Operace k aktualizaci tématu. Tato operace není podporována na volání rozhraní API verze 2017-04-01. Autorizační pravidla. Použijte volání PUT se aktualizovat autorizační pravidlo.|
|/namespaces/topics/authorizationRules/delete|Operace odstranění tématu autorizační pravidla|
|/namespaces/topics/authorizationRules/listkeys/action|Získat připojovací řetězec k tématu|
|/namespaces/topics/authorizationRules/Read| Získání seznamu tématu autorizační pravidla|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Znovu vygeneruje primární nebo sekundární klíč k prostředku.|
|/namespaces/topics/authorizationRules/Write|Vytvoření tématu autorizační pravidla a aktualizujte jeho vlastnosti. Autorizační pravidla přístupová práva lze aktualizovat.|
|/namespaces/topics/DELETE|Operace odstranění prostředků tématu|
|/namespaces/topics/Read|Získání seznamu popisů tématu prostředků|
|/namespaces/topics/Subscriptions/DELETE|Operace odstranění TopicSubscription prostředků|
|/namespaces/topics/Subscriptions/Read|Získání seznamu popisů TopicSubscription prostředků|
|/namespaces/topics/Subscriptions/Rules/DELETE|Operace se odstranit pravidlo prostředků|
|/namespaces/topics/Subscriptions/Rules/Read|Získání seznamu popisů pravidlo prostředků|
|/namespaces/topics/Subscriptions/Rules/Write|Vytvořit nebo aktualizovat pravidlo vlastnosti.|
|/namespaces/topics/Subscriptions/Write|Vytvoření nebo aktualizace TopicSubscription vlastnosti.|
|/namespaces/topics/Write|Vytvoření nebo aktualizace tématu vlastnosti.|
|/ obory názvů a zápis|Vytvořte prostředek Namespace a aktualizujte jeho vlastnosti. Značky a kapacitu Namespace jsou vlastnosti, které lze aktualizovat.|
|/Operations/Read|Získejte operace|
|/ registrace nebo akce|Zaregistruje předplatné u poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus.|
|/sku/read|Získání seznamu popisů Sku prostředků|
|/SKU/Regions/Read|Získání seznamu popisů SkuRegions prostředků|
|nebo zrušit registraci nebo akce|Zaregistruje předplatné u poskytovatele prostředků ServiceBus a povolí vytváření prostředků ServiceBus.|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Operace | Popis |
|---|---|
|/Clusters/Applications/DELETE|Umožňuje odstranit libovolnou aplikaci.|
|/Clusters/Applications/Read|Umožňuje číst libovolnou aplikaci|
|/clusters/applications/services/delete|Umožňuje odstranit libovolnou službu.|
|/clusters/applications/services/partitions/read|Umožňuje číst libovolný oddíl.|
|/Clusters/Applications/Services/Partitions/Replicas/Read|Umožňuje číst libovolnou repliku.|
|/Clusters/Applications/Services/Read|Umožňuje číst libovolnou službu.|
|/Clusters/Applications/Services/Statuses/Read|Umožňuje načíst stav libovolné služby.|
|/Clusters/Applications/Services/Write|Umožňuje vytvořit nebo aktualizovat libovolnou službu.|
|/Clusters/Applications/Write|Umožňuje vytvořit nebo aktualizovat libovolnou aplikaci.|
|/Clusters/applicationTypes/DELETE|Umožňuje odstranit libovolný typ aplikace.|
|/Clusters/applicationTypes/Read|Umožňuje přečíst libovolný typ aplikace.|
|/Clusters/applicationTypes/versions/DELETE|Umožňuje odstranit libovolnou verzi typu aplikace.|
|/Clusters/applicationTypes/versions/Read|Umožňuje přečíst libovolnou verzi typu aplikace.|
|/Clusters/applicationTypes/versions/Write|Umožňuje vytvořit nebo aktualizovat libovolnou verzi typu aplikace.|
|/Clusters/applicationTypes/Write|Umožňuje vytvořit nebo aktualizovat libovolný typ aplikace.|
|/Clusters/DELETE|Umožňuje odstranit libovolný cluster.|
|/Clusters/Nodes/Read|Umožňuje číst libovolný uzel.|
|/Clusters/Read|Umožňuje číst libovolný cluster.|
|/Clusters/Statuses/Read|Umožňuje načíst stav libovolného clusteru.|
|/ clustery a zápis|Umožňuje vytvořit nebo aktualizovat libovolný cluster.|
|/Locations/clusterVersions/Read|Načíst libovolnou verzi clusteru|
|/Locations/Environments/clusterVersions/Read|Umožňuje přečíst libovolnou verzi clusteru pro konkrétní prostředí.|
|/Locations/operationresults/Read|Umožňuje přečíst libovolné výsledky operace.|
|/Locations/Operations/Read|Umožňuje přečíst libovolnou operaci podle umístění.|
|/Operations/Read|Umožňuje přečíst libovolnou dostupnou operaci.|
|/ registrace nebo akce|Umožňuje zaregistrovat libovolnou akci.|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Operace | Popis |
|---|---|
|/applicationDefinitions/DELETE|Odebere definici aplikace.|
|/applicationDefinitions/Read|Načte seznam definic aplikací.|
|/ applicationDefinitions/zápisu|Přidá nebo upraví definici aplikace.|
|/Applications/DELETE|Odebere aplikaci.|
|/Applications/Read|Načte seznam aplikací.|
|/ aplikace/zápisu|Vytvoří aplikaci.|
|/Locations/operationStatuses/Read|Umožňuje načíst stav operace pro prostředek.|
|/ registrace nebo akce|Provede registraci do Solutions.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operace | Popis |
|---|---|
|/checkNameAvailability/action|Ověřte, zda zadaný server je k dispozici pro zřizování po celém světě pro dané předplatné název.|
|/locations/auditingSettingsAzureAsyncOperation/read|Načíst výsledek operace nastavení zásad auditování objektu blob rozšířený server|
|/locations/auditingSettingsOperationResults/read|Načíst výsledek operace nastavení zásad auditování objektu blob serveru|
|/Locations/Capabilities/Read|Získá možnosti pro toto předplatné v daném umístění|
|/locations/databaseAzureAsyncOperation/read|Získá stav operace databáze.|
|/locations/databaseOperationResults/read|Získá stav operace databáze.|
|/locations/deletedServerAsyncOperation/read|Získá v průběhu operace na odstraněné serveru|
|/locations/deletedServerOperationResults/read|Získá v průběhu operace na odstraněné serveru|
|/locations/deletedServers/read|Vrátí seznam odstraněných servery nebo získá vlastnosti pro zadaný server odstraněné.|
|/Locations/deletedServers/Recover/Action|Obnovení odstraněného serveru|
|/locations/deleteVirtualNetworkOrSubnets/action|Odstraní pravidla virtuální sítě přidružený k virtuální síť nebo podsíť|
|/locations/elasticPoolAzureAsyncOperation/read|Získá azure asynchronní operace pro elastický fond asynchronní operace|
|/Locations/elasticPoolOperationResults/Read|Získá výsledek operace elastického fondu.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Načíst výsledek operace nastavení zásad auditování objektu blob rozšířený server|
|/Locations/extendedAuditingSettingsOperationResults/Read|Načíst výsledek operace nastavení zásad auditování objektu blob rozšířený server|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Dokončení operace obnovení spravované databáze|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Získá v průběhu operací na spravované databáze transparentní šifrování dat|
|/locations/managedTransparentDataEncryptionOperationResults/read|Získá v průběhu operací na spravované databáze transparentní šifrování dat|
|/Locations/Read|Získá umístění dostupné pro dané předplatné|
|/locations/syncAgentOperationResults/read|Načíst výsledek operace synchronizace agenta prostředků|
|/locations/syncDatabaseIds/read|Načtení ID databáze synchronizace pro konkrétní oblasti a předplatného|
|/locations/syncGroupOperationResults/read|Načíst výsledek operace synchronizace skupiny prostředků|
|/locations/syncMemberOperationResults/read|Načíst výsledek operace synchronizace člen prostředků|
|/Locations/usages/Read|Získá kolekci metriky využití pro toto předplatné v umístění|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Vrátí podrobnosti pravidel zadané virtuální síti azure asynchronní operace |
|/locations/virtualNetworkRulesOperationResults/read|Vrací podrobnosti operace pravidla zadané virtuální síti |
|/managedInstances/administrators/delete|Odstraní stávající správce spravované instance.|
|/managedInstances/administrators/read|Získá seznam správců spravované instance.|
|/managedInstances/administrators/write|Vytvoří nebo aktualizuje spravované instance správce se zadanými parametry.|
|/managedInstances/databases/delete|Odstraní stávající spravované databáze|
|/managedInstances/databases/read|Získá existující spravované databáze|
|/managedInstances/databases/securityAlertPolicies/read|Načtení podrobností zásady detekce hrozeb databáze nakonfigurované na danou databázi spravované|
|/managedInstances/databases/securityAlertPolicies/write|Změna zásad detekce hrozeb databáze pro danou databázi spravované|
|/managedInstances/databases/securityEvents/read|Načte události zabezpečení spravované databáze|
|/managedInstances/databases/transparentDataEncryption/read|Načíst podrobnosti o databáze transparentní šifrování dat na danou databázi spravované|
|/managedInstances/databases/transparentDataEncryption/write|Změnit databázi transparentní šifrování dat pro danou databázi spravované|
|/managedInstances/databases/write|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|/managedInstances/delete|Odstraní existující spravované instance.|
|/managedInstances/metricDefinitions/read|Získat definice metrik spravované instance|
|/managedInstances/metrics/read|Získat metriky spravované instance|
|/managedInstances/read|Vrátí seznam spravované instance nebo získá vlastnosti pro zadané spravované instance.|
|/managedInstances/securityAlertPolicies/read|Načíst podrobnosti o zásadě detekce hrozeb spravovaný server nakonfigurovaný na daném spravovaného serveru|
|/managedInstances/securityAlertPolicies/write|Změna zásad detekce hrozeb spravovaného serveru pro danou spravovaný server|
|/managedInstances/write|Vytvoří instanci spravovaného se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadané spravované instance.|
|/Operations/Read|Získá dostupné operace REST|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků Microsoft SQL Database a umožňuje vytvoření databáze Microsoft SQL.|
|/servers/administratorOperationResults/read|Získá v průběhu operace ve Správci serveru|
|/servers/administrators/delete|Odstranit správce serveru|
|/servers/administrators/read|Načíst podrobnosti o správce serveru|
|/servers/administrators/write|Vytvořit nebo aktualizovat Správce serveru|
|/servers/advisors/read|Vrátí seznam poradci, které jsou k dispozici pro server|
|/Servers/advisors/recommendedActions/Read|Vrátí seznam doporučených akcí zadaný advisor pro server|
|/Servers/advisors/recommendedActions/Write|Použití doporučenou akci na server|
|/servers/advisors/write|Aktualizace automatického – spuštění stav advisor na úrovni serveru.|
|/Servers/auditingPolicies/Read|Načíst podrobnosti o serveru tabulky výchozí zásady na daném serveru nakonfigurován auditování|
|/Servers/auditingPolicies/Write|Změňte výchozí server tabulky auditování pro daný server|
|/servers/auditingSettings/operationResults/read|Načíst výsledek operace nastavení zásad auditování objektu blob serveru|
|/servers/auditingSettings/read|Načíst podrobnosti o zásadě auditování objektů blob serveru na daném serveru nakonfigurován|
|/servers/auditingSettings/write|Změnit nastavení auditování serveru objekt blob pro daný server|
|/servers/automaticTuning/read|Vrátí automatické ladění nastavení pro server|
|/servers/automaticTuning/write|Automatické ladění nastavení pro server aktualizací a vrátí aktualizovaným nastavením|
|/servers/backupLongTermRetentionVaults/delete|Odstraní existující trezor záloh archivaci.|
|/servers/backupLongTermRetentionVaults/read|Tato operace se používá k získání trezoru zálohování dlouhodobé uchovávání. Vrátí informace o úložišti registrované k tomuto serveru|
|/servers/backupLongTermRetentionVaults/write|Tato operace se používá k registraci zálohování dlouhodobé uchovávání trezoru k serveru|
|/servers/communicationLinks/delete|Odstraní existující odkaz komunikace serveru.|
|/servers/communicationLinks/read|Vrátí seznam komunikační propojení zadaný server.|
|/servers/communicationLinks/write|Vytvořit nebo aktualizovat datový spoj server.|
|/servers/connectionPolicies/read|Vrátí seznam zásady připojení serveru zadaného serveru.|
|/servers/connectionPolicies/write|Vytvořit nebo aktualizovat zásady připojení serveru.|
|/servers/databases/advisors/read|Vrátí seznam poradci, které jsou k dispozici pro databázi|
|/Servers/Databases/advisors/recommendedActions/Read|Vrátí seznam doporučené akce zadané pro databázi Poradce pro|
|/servers/databases/advisors/recommendedActions/write|Použít akci doporučenou v databázi|
|/servers/databases/advisors/write|Aktualizace automatického – spuštění stav advisor na úrovni databáze.|
|/servers/databases/auditingPolicies/read|Načtení podrobností tabulky auditování zásady, které jsou nakonfigurované na danou databázi|
|/servers/databases/auditingPolicies/write|Změna zásad auditování tabulka pro danou databázi|
|/servers/databases/auditingSettings/read|Načíst podrobnosti o objektu blob auditování zásady, které jsou nakonfigurované na danou databázi|
|/servers/databases/auditingSettings/write|Změna zásad auditování objektů blob pro danou databázi|
|/servers/databases/auditRecords/read|Načtení záznamů auditu databáze objektů blob|
|/servers/databases/automaticTuning/read|Vrátí nastavení automatického ladění pro databázi|
|/servers/databases/automaticTuning/write|Aktualizace nastavení automatického ladění pro databázi a vrátí aktualizovaným nastavením|
|/servers/databases/azureAsyncOperation/read|Získá stav operace databáze.|
|/servers/databases/backupLongTermRetentionPolicies/read|Vrátí seznam archivace zásady zálohování zadané databáze.|
|/servers/databases/backupLongTermRetentionPolicies/write|Vytvořit nebo aktualizovat zásady zálohování archivace databáze.|
|/servers/databases/connectionPolicies/read|Načíst podrobnosti o zásadě připojení nakonfigurovaná na danou databázi|
|/servers/databases/connectionPolicies/write|Změnit zásady připojení pro danou databázi|
|/servers/databases/dataMaskingPolicies/read|Vrátí seznam maskování zásady dat databáze.|
|/servers/databases/dataMaskingPolicies/rules/delete|Odstranit pravidlo zásad pro danou databázi maskování dat.|
|/servers/databases/dataMaskingPolicies/rules/read|Načtení podrobností pravidla zásad, které jsou nakonfigurované na danou databázi maskování dat|
|/servers/databases/dataMaskingPolicies/rules/write|Změňte pravidla zásad pro danou databázi maskování dat.|
|/servers/databases/dataMaskingPolicies/write|Změnit zásady pro danou databázi maskování dat.|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Vrátí informace o kroku distribuovaného dotazu dotazu datový sklad pro vybraný krok ID|
|/servers/databases/dataWarehouseQueries/read|Vrátí distribuční dotazu informace o datovém skladu pro vybraný dotaz ID|
|/servers/databases/dataWarehouseUserActivities/read|Načte aktivit uživatelů instance SQL Data Warehouse, která obsahuje dotazy, spuštěné a pozastavený|
|/servers/databases/delete|Odstraní existující databázi.|
|/Servers/Databases/export/Action|Export databáze Azure SQL|
|/servers/databases/extendedAuditingSettings/read|Načtení podrobností rozšířený objekt blob auditování zásady, které jsou nakonfigurované na danou databázi|
|/servers/databases/extendedAuditingSettings/write|Změna zásad auditování rozšířený objekt blob pro danou databázi|
|/servers/databases/extensions/read|Získá kolekci rozšíření pro databázi.|
|/servers/databases/extensions/write|Změňte příponu pro danou databázi|
|/servers/databases/geoBackupPolicies/read|Načtení geograficky zásady zálohování pro danou databázi|
|/servers/databases/geoBackupPolicies/write|Vytvořit nebo aktualizovat zásadu geobackup databáze|
|/servers/databases/importExportOperationResults/read|Získá probíhající operace importu a exportu|
|/Servers/Databases/metricDefinitions/Read|Návratové typy metriky, které jsou k dispozici pro databáze|
|/Servers/Databases/Metrics/Read|Vrátí metriky pro databáze|
|/Servers/Databases/Move/Action|Rename Azure SQL Database|
|/servers/databases/operationResults/read|Získá stav operace databáze.|
|/Servers/Databases/Operations/Cancel/Action|Zruší Azure SQL Database čekající asynchronní operaci, která ještě není dokončen.|
|/Servers/Databases/Operations/Read|Vrátí seznam operací provést v databázi|
|/servers/databases/pause/action|Pozastavení databáze datového skladu Azure SQL|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Získá dostupné protokoly pro databáze|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Návratové typy metriky, které jsou k dispozici pro databáze|
|/servers/databases/queryStore/queryTexts/read|Vrátí kolekci texty dotazu, které odpovídají na zadané parametry.|
|/servers/databases/queryStore/read|Vrátí aktuální hodnoty nastavení úložiště dotazů pro databázi.|
|/servers/databases/queryStore/write|Aktualizace nastavení úložiště dotazů pro databázi|
|/servers/databases/read|Vrátí seznam databází nebo získá vlastnosti pro určenou databázi.|
|/servers/databases/replicationLinks/delete|Ukončení relace replikace vynuceně a potenciální ztráty dat.|
|/servers/databases/replicationLinks/failover/action|Převzetí služeb při selhání po synchronizaci všech změní z primární, provedení této databáze do replikace relationship\u0027s primární a provádění vzdálených primární do sekundárního|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Převzetí služeb při selhání okamžitě potenciální ztrátě dat, provedení této databáze do replikace relationship\u0027s primární a provádění vzdálených primární do sekundárního|
|/servers/databases/replicationLinks/read|Návratový podrobnosti o propojení replikace navázána pro konkrétní databázi|
|/servers/databases/replicationLinks/unlink/action|Ukončení relace replikace vynuceně nebo po synchronizaci s partnera|
|/servers/databases/replicationLinks/updateReplicationMode/action|Režim aktualizace replikace pro odkaz na synchronní nebo asynchronní režim|
|/servers/databases/restorePoints/action|Vytvoří nový bod obnovení|
|/servers/databases/restorePoints/read|Vrátí obnovit body pro databázi.|
|/Servers/Databases/Resume/Action|Obnovení databáze datového skladu Azure SQL|
|/servers/databases/schemas/read|Načíst seznam schémat databáze|
|/servers/databases/schemas/tables/columns/read|Načíst seznam sloupců tabulky.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Odstranit štítek utajení daného sloupce|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Získat štítek utajení daného sloupce|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Vytvořit nebo aktualizovat štítek utajení daného sloupce|
|/servers/databases/schemas/tables/read|Načtení seznamu tabulek databáze|
|/servers/databases/schemas/tables/recommendedIndexes/read|Načíst seznam doporučení indexu v databázi|
|/servers/databases/schemas/tables/recommendedIndexes/write|Použít doporučení indexu|
|/servers/databases/securityAlertPolicies/read|Načíst podrobnosti o zásadě detekce hrozeb nakonfigurované na danou databázi|
|/servers/databases/securityAlertPolicies/write|Změna zásad detekce hrozeb pro danou databázi|
|/servers/databases/securityMetrics/read|Získá kolekci metriky zabezpečení databáze|
|/servers/databases/sensitivityLabels/read|Seznam popisky velkých a malých písmen na danou databázi|
|/servers/databases/serviceTierAdvisors/read|Vrátí návrhu o škálování databáze nahoru nebo dolů na základě statistik provádění dotazu na výkon nebo snížit náklady na|
|/servers/databases/syncGroups/cancelSync/action|Zrušit synchronizaci skupiny synchronizace|
|/servers/databases/syncGroups/delete|Odstraní existující synchronizace skupiny.|
|/servers/databases/syncGroups/hubSchemas/read|Vrátí seznam synchronizace databáze schémata rozbočovače|
|/servers/databases/syncGroups/logs/read|Vrátí seznam protokolů synchronizace skupiny|
|/servers/databases/syncGroups/read|Vrátí seznam synchronizace skupiny nebo načte vlastnosti zadaného synchronizace skupiny.|
|/servers/databases/syncGroups/refreshHubSchema/action|Aktualizace schématu databáze Centrum synchronizace|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Načíst výsledek operace aktualizace schématu Centrum synchronizace|
|/servers/databases/syncGroups/syncMembers/delete|Odstraní existujícího člena synchronizace.|
|/servers/databases/syncGroups/syncMembers/read|Vrátí seznam členů synchronizace nebo získá vlastnosti pro člena zadané synchronizace.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Aktualizovat schéma člen synchronizace|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Načíst výsledek operace aktualizace schématu člen synchronizace|
|/servers/databases/syncGroups/syncMembers/schemas/read|Vrátí seznam synchronizace schémat databáze člena|
|/servers/databases/syncGroups/syncMembers/write|Vytvoří členem synchronizace se zadanými parametry nebo aktualizovat vlastnosti pro člena zadané synchronizace.|
|/servers/databases/syncGroups/triggerSync/action|Aktivace synchronizace skupiny synchronizace|
|/servers/databases/syncGroups/write|Vytvoří skupinu synchronizace se zadanými parametry nebo aktualizovat vlastnosti pro zadaný synchronizace skupiny.|
|/servers/databases/topQueries/queryText/action|Vrátí text Transact-SQL pro ID vybraný dotaz|
|/servers/databases/topQueries/read|Vrátí statistiku modulu runtime pro vybraný dotaz agregovat do vybrané časové období|
|/servers/databases/topQueries/statistics/read|Vrátí statistiku modulu runtime pro vybraný dotaz agregovat do vybrané časové období|
|/servers/databases/transparentDataEncryption/operationResults/read|Získá v průběhu operací na transparentní šifrování dat|
|/servers/databases/transparentDataEncryption/read|Načíst stav a podrobnosti o funkce zabezpečení dat transparentní šifrování pro danou databázi|
|/servers/databases/transparentDataEncryption/write|Změnit stav šifrování transparentní dat|
|/servers/databases/upgradeDataWarehouse/action|Upgrade databáze datového skladu Azure SQL|
|/Servers/Databases/usages/Read|Získá informace o použití Azure SQL Database|
|/servers/databases/vulnerabilityAssessments/delete|Odebrat vyhodnocení ohrožení zabezpečení pro danou databázi|
|/servers/databases/vulnerabilityAssessments/read|Načíst podrobnosti o vyhodnocení ohrožení zabezpečení, které jsou nakonfigurované na danou databázi|
|/Servers/Databases/vulnerabilityAssessments/Rules/baselines/DELETE|Odeberte směrný plán vyhodnocení pravidlo ohrožení zabezpečení pro danou databázi|
|/Servers/Databases/vulnerabilityAssessments/Rules/baselines/Read|Získat základní pravidlo assessment ohrožení zabezpečení pro danou databázi|
|/Servers/Databases/vulnerabilityAssessments/Rules/baselines/Write|Změňte základní pravidlo assessment ohrožení zabezpečení pro danou databázi|
|/servers/databases/vulnerabilityAssessments/scans/action|Spusťte kontrolu databáze assessment ohrožení zabezpečení.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Převeďte stávající výsledek kontroly na lidské čitelném formátu. Pokud už nic se stane|
|/servers/databases/vulnerabilityAssessments/scans/read|Vrátí seznam ohrožení zabezpečení databáze záznamy kontrolu vyhodnocení nebo získat kontrolu záznam pro ID zadaný kontroly.|
|/servers/databases/vulnerabilityAssessments/write|Změnit vyhodnocení ohrožení zabezpečení pro danou databázi|
|/servers/databases/vulnerabilityAssessmentScans/action|Spusťte kontrolu databáze assessment ohrožení zabezpečení.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Načíst výsledky prohledávání vyhodnocení ohrožení zabezpečení databáze operaci zpracování|
|/servers/databases/vulnerabilityAssessmentSettings/read|Načíst podrobnosti o vyhodnocení ohrožení zabezpečení, které jsou nakonfigurované na danou databázi|
|/servers/databases/vulnerabilityAssessmentSettings/write|Změnit vyhodnocení ohrožení zabezpečení pro danou databázi|
|/Servers/Databases/Write|Vytvoří databázi se zadanými parametry nebo aktualizovat vlastnosti a značky pro určenou databázi.|
|/Servers/DELETE|Odstraní existující server.|
|/servers/disasterRecoveryConfiguration/delete|Odstraní stávající konfigurace zotavení po havárii pro daný server|
|/servers/disasterRecoveryConfiguration/failover/action|Převzetí služeb při selhání DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Vynutit převzetí služeb při selhání DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/read|Získá kolekci po havárii obnovení konfigurace, které zahrnují tento server|
|/servers/disasterRecoveryConfiguration/write|Konfigurace obnovení po havárii serveru změn|
|/servers/elasticPoolEstimates/read|Vrátí seznam odhad elastického fondu vytvořeny již pro tento server|
|/servers/elasticPoolEstimates/write|Vytvoří nový odhad elastického fondu pro seznam databází poskytuje|
|/servers/elasticPools/advisors/read|Vrátí seznam poradci pro elastický fond k dispozici|
|/servers/elasticPools/advisors/recommendedActions/read|Vrátí seznam doporučených akcí zadaný advisor pro elastický fond|
|/servers/elasticPools/advisors/recommendedActions/write|Použít akci doporučenou v elastického fondu|
|/servers/elasticPools/advisors/write|Aktualizace automatického – spuštění stav advisor na úrovni elastického fondu.|
|/servers/elasticPools/databases/read|Získá seznam databází pro elastický fond|
|/servers/elasticPools/delete|Odstranění existujícího elastického fondu|
|/servers/elasticPools/elasticPoolActivity/read|Načtení činnosti a údaje na danou databázi elastického fondu|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Načtení činnosti a údaje na danou databázi, která je součástí fondu elastické databáze|
|/servers/elasticPools/metricDefinitions/read|Návratové typy metriky, které jsou k dispozici pro fondy elastické databáze|
|/servers/elasticPools/metrics/read|Vrátí metriky pro fondy elastické databáze|
|/servers/elasticPools/operations/cancel/action|Zruší elastický fond Azure SQL čekající asynchronní operaci, která ještě není dokončen.|
|/servers/elasticPools/operations/read|Vrátí seznam operací týkajících se elastický fond|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Návratové typy metriky, které jsou k dispozici pro fondy elastické databáze|
|/servers/elasticPools/read|Načíst podrobnosti o elastického fondu na daném serveru|
|/servers/elasticPools/skus/read|Získá kolekci SKU, které jsou k dispozici pro tento elastický fond|
|/servers/elasticPools/write|Vytvořit novou nebo změnit vlastnosti existujícího elastického fondu|
|/Servers/encryptionProtector/Read|Vrátí seznam ochrany pomocí šifrování serveru nebo získá vlastnosti pro zadaný server ochranu pomocí šifrování.|
|/Servers/encryptionProtector/Write|Aktualizujte vlastnosti pro zadaná šifrování Ochrana serveru.|
|/servers/extendedAuditingSettings/read|Načíst podrobnosti o objektu blob rozšířený server auditování zásady nakonfigurované na daném serveru|
|/Servers/extendedAuditingSettings/Write|Změnit server rozšířených auditování objektů blob pro daný server|
|/servers/failoverGroups/delete|Odstraní existující skupinu pro převzetí služeb při selhání.|
|/servers/failoverGroups/failover/action|Provede plánované převzetí služeb při selhání v existující skupině převzetí služeb při selhání.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Provede Vynucené převzetí služeb při selhání v existující skupině převzetí služeb při selhání.|
|/servers/failoverGroups/read|Vrátí seznam převzetí služeb při selhání skupiny nebo načte vlastnosti zadaného převzetí služeb při selhání skupiny.|
|/servers/failoverGroups/write|Vytvoří skupinu převzetí služeb při selhání se zadanými parametry nebo aktualizuje vlastnosti a značky pro skupinu zadaný převzetí služeb při selhání.|
|/servers/firewallRules/delete|Odstraní existující pravidlo brány firewall serveru.|
|/servers/firewallRules/read|Vrátí seznam serverová brána firewall, pravidla nebo získá vlastnosti pro zadaný server pravidlo brány firewall.|
|/servers/firewallRules/write|Vytvoří pravidlo brány firewall serveru se zadanými parametry, aktualizovat vlastnosti pro zadané pravidlo nebo přepsat všechny existující pravidla nové pravidel brány firewall serveru.|
|/servers/import/action|Vytvoření nové databáze na serveru a nasadit schéma a data z balíčku DacPac|
|/servers/importExportOperationResults/read|Získá probíhající operace importu a exportu|
|/Servers/Keys/DELETE|Odstraní existující klíč serveru.|
|/Servers/Keys/Read|Vrátí seznam serveru klíče nebo získá vlastnosti pro klíč zadaný server.|
|/Servers/Keys/Write|Vytvoří klíč se zadanými parametry nebo aktualizovat vlastnosti a značky pro klíč zadaný server.|
|/Servers/operationResults/Read|Získá serveru v průběhu operace|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Návratové typy metriky, které jsou k dispozici pro servery|
|/Servers/Read|Vrátí seznam serverů nebo získá vlastnosti pro zadaný server.|
|/servers/recommendedElasticPools/databases/read|Načtení metriky pro fondy doporučené elastické databáze pro daný server|
|/servers/recommendedElasticPools/read|Načtení doporučení pro fondy elastické databáze pro snížení nákladů na nebo vylepšení výkonu na základě využití prostředků historica|
|/Servers/recoverableDatabases/Read|Tato operace se používá pro zotavení po havárii živé databáze k obnovení databáze pro poslední známá funkční bodu zálohy. Vrací informace o poslední správné zálohy ale doesn\u0027t ve skutečnosti obnovit databázi.|
|/Servers/restorableDroppedDatabases/Read|Získáte seznam databází, které byly vyřazeny na daném serveru, které jsou stále v rámci zásady uchovávání informací.|
|/servers/securityAlertPolicies/operationResults/read|Načíst výsledky operace zápisu zásady detekce hrozeb serveru|
|/servers/securityAlertPolicies/read|Načíst podrobnosti o zásadě detekce hrozeb server nakonfigurovaný na daném serveru|
|/servers/securityAlertPolicies/write|Změnit zásady detekce hrozeb serveru pro daný server|
|/servers/serviceObjectives/read|Načtení seznamu cílů na úrovni služby (také označované jako úrovně výkonu) k dispozici na daném serveru|
|/servers/syncAgents/delete|Odstraní stávající agenta synchronizace.|
|/servers/syncAgents/generateKey/action|Vygenerování klíče registrace agenta synchronizace|
|/servers/syncAgents/linkedDatabases/read|Vrátí seznam agenta propojené databáze synchronizace|
|/servers/syncAgents/read|Vrátí seznam agentů synchronizace nebo získá vlastnosti pro zadaný synchronizace agenta.|
|/servers/syncAgents/write|Vytvoří agenta synchronizace se zadanými parametry nebo aktualizovat vlastnosti pro zadaný synchronizace agenta.|
|/Servers/usages/Read|Vrátí kvóty DTU serveru a aktuální consuption DTU všemi databázemi v rámci serveru|
|/servers/virtualNetworkRules/delete|Odstraní existující pravidlo virtuální sítě|
|/servers/virtualNetworkRules/read|Vrátí seznam virtuální sítě pravidla nebo získá vlastnosti pro pravidlo zadané virtuální síti.|
|/servers/virtualNetworkRules/write|Vytvoří pravidlo virtuální sítě se zadanými parametry nebo aktualizovat vlastnosti a značky pro pravidlo zadané virtuální síti.|
|/ servery a zápis|Vytvoří server, se zadanými parametry nebo aktualizovat vlastnosti a značky pro zadaný server.|
|nebo zrušit registraci nebo akce|Zrušení registrace předplatného pro poskytovatele prostředků Microsoft SQL Database a umožňuje vytvoření databáze Microsoft SQL.|
|/virtualClusters/Read|Vrátí seznam virtuálních clusterů nebo získá vlastnosti pro zadanou virtuální cluster.|
|/ virtualClusters/zápisu|Aktualizace virtuálního clusteru značky.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operace | Popis |
|---|---|
|/checknameavailability/Read|Zkontroluje, že název účtu je platný a nepoužívá se.|
|/locations/deleteVirtualNetworkOrSubnets/action|Upozorní službu Microsoft.Storage, že se odstraňuje virtuální síť nebo podsítě.|
|/Operations/Read|Zadá dotaz na stav asynchronní operace.|
|/ registrace nebo akce|Zaregistruje předplatné pro zprostředkovatele prostředku úložiště a povolí vytvoření účtů úložiště.|
|/skus/Read|Vypíše SKU podporovaná v Microsoft.Storage.|
|/storageAccounts/blobServices/Containers/clearLegalHold/Action|Vymazat blokování kontejneru objektů blob z právních důvodů|
|/storageAccounts/blobServices/Containers/DELETE|Vrátí výsledek odstraňování kontejneru.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Odstranit zásady neměnnosti kontejneru objektů blob|
|/storageAccounts/blobServices/Containers/immutabilityPolicies/extend/Action|Rozšířit zásady neměnnosti kontejneru objektů blob|
|/storageAccounts/blobServices/Containers/immutabilityPolicies/Lock/Action|Zamknout zásady neměnnosti kontejneru objektů blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Získat zásady neměnnosti kontejneru objektů blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Vložit zásady neměnnosti kontejneru objektů blob|
|/storageAccounts/blobServices/Containers/Read|Vrátí kontejner nebo seznam kontejnerů.|
|/storageAccounts/blobServices/Containers/setLegalHold/Action|Nastavit blokování kontejneru objektů blob z právních důvodů|
|/storageAccounts/blobServices/Containers/Write|Vrátí výsledek vložení nebo vypůjčení kontejneru objektů blob.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky prostředku.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky prostředku.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Získá seznam definic Metrik úložišť Microsoft.|
|/storageAccounts/blobServices/Read|Vrátí vlastnosti nebo statistiky služby Blob.|
|/storageAccounts/blobServices/Write|Vrátí výsledek vložení vlastností služby Blob.|
|/storageAccounts/DELETE|Odstraní existující účet úložiště.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky prostředku.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky prostředku.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Získá seznam definic Metrik úložišť Microsoft.|
|/storageAccounts/listAccountSas/action|Vrátí token SAS určeného účtu úložiště.|
|/storageAccounts/listkeys/action|Vrátí přístupové klíče pro zadaný účet úložiště.|
|/storageAccounts/listServiceSas/action|Vrátí token SAS služby pro určený účet úložiště.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky prostředku.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky prostředku.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Získá seznam definic Metrik úložišť Microsoft.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky prostředku.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Získá seznam definic Metrik úložišť Microsoft.|
|/storageAccounts/queueServices/queues/delete|Vrátí výsledek odstranění fronty.|
|/storageAccounts/queueServices/queues/read|Vrátí frontu nebo seznam front.|
|/storageAccounts/queueServices/queues/write|Vrátí výsledek zápisu fronty.|
|/storageAccounts/queueServices/read|Vrátí vlastnosti nebo statistiky služby Queue.|
|/storageAccounts/queueServices/write|Vrátí výsledky nastavení vlastností služby Queue.|
|/storageAccounts/Read|Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu.|
|/storageAccounts/regeneratekey/Action|Obnoví přístupové klíče pro zadaný účet úložiště.|
|/storageAccounts/services/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky účtu úložiště.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky prostředku.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky prostředku.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky prostředku.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Získá seznam definic Metrik úložišť Microsoft.|
|/ storageAccounts/zápisu|Vytvoří účet úložiště se zadanými parametry, aktualizuje vlastnosti a značky nebo přidá vlastní doménu pro zadaný účet úložiště.|
|/usages/Read|Vrací limit a aktuální počet použití pro zdroje v zadaném odběru|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Operace | Popis |
|---|---|
|/storageSyncServices/delete|Odstranit všechny synchronizační služby úložiště|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro úložiště synchronizační služby|
|/storageSyncServices/read|Číst všechny synchronizační služby úložiště|
|/storageSyncServices/registeredServers/delete|Odstranit libovolný zaregistrovaný Server|
|/storageSyncServices/registeredServers/read|Číst všechny zaregistrovaný Server|
|/storageSyncServices/registeredServers/write|Vytvořit nebo aktualizovat všechny zaregistrovaný Server|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Odstranit žádné koncové body cloudu|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|Umístění rozhraní api pro asynchronní volání zálohování|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Volat tuto akci po dokončení zálohování|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Volat tuto akci po obnovení|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Volat tuto akci před zálohováním|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Tato akce před obnovení volání|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Číst všechny koncové body cloudu|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Obnovení prezenčního signálu|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Vytvořit nebo aktualizovat žádné koncové body cloudu|
|/storageSyncServices/syncGroups/delete|Odstranit všechny skupiny pro synchronizaci|
|/storageSyncServices/syncGroups/read|Číst všechny skupiny pro synchronizaci|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Odstranit žádné koncové body serveru|
|/storageSyncServices/syncGroups/serverEndpoints/read|Číst všechny koncové body serveru|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Volat tuto akci vrátit soubory na server|
|/storageSyncServices/syncGroups/serverEndpoints/write|Vytvořit nebo aktualizovat žádné koncové body serveru|
|/storageSyncServices/syncGroups/write|Vytvořit nebo aktualizovat všechny skupiny pro synchronizaci|
|/storageSyncServices/write|Vytvořit nebo aktualizovat všechny synchronizační služby úložiště|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operace | Popis |
|---|---|
|/managers/accessControlRecords/delete|Odstraní záznamy řízení přístupu|
|/managers/accessControlRecords/read|Uvádí nebo získá záznamy řízení přístupu|
|/managers/accessControlRecords/write|Vytvořit nebo aktualizovat záznamy řízení přístupu|
|/Managers/Alerts/Read|Uvádí nebo získá výstrahy|
|/managers/bandwidthSettings/delete|Odstraní existující nastavení šířky pásma (pouze řady 8000)|
|/Managers/bandwidthSettings/Read|Seznam nastavení šířky pásma (pouze 8000 řada)|
|/managers/bandwidthSettings/write|Vytvoří novou nebo aktualizuje nastavení šířky pásma (pouze řady 8000)|
|A správci/certifikátů/zápis|Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů.|
|/managers/clearAlerts/action|Vymažte všechny výstrahy spojené se Správce zařízení.|
|/Managers/cloudApplianceConfigurations/Read|Seznam zařízení cloudu podporované konfigurace|
|/managers/configureDevice/action|Konfiguruje zařízení|
|/Managers/DELETE|Odstraní Správci zařízení|
|Správci nebo odstranění|Operace odstranění trezoru Odstraní zadaný prostředek Azure typu 'trezoru.|
|/managers/devices/alertSettings/read|Uvádí nebo získá nastavení výstrah|
|/managers/devices/alertSettings/write|Vytvořit nebo aktualizovat nastavení výstrah|
|/managers/devices/backupPolicies/backup/action|Proveďte zálohu ručně vytvořit na vyžádání zálohování všech svazků chráněnými zásadami.|
|/managers/devices/backupPolicies/delete|Odstraní existující zásady zálohování (pouze řady 8000)|
|/managers/devices/backupPolicies/read|Seznam zálohování zásady (pouze 8000 řada)|
|/managers/devices/backupPolicies/schedules/delete|Odstraní existující plán|
|/managers/devices/backupPolicies/schedules/read|Seznam plány|
|/managers/devices/backupPolicies/schedules/write|Vytvoří novou nebo aktualizovat plány|
|/managers/devices/backupPolicies/write|Vytvoří novou nebo aktualizovat zásady zálohování (pouze řady 8000)|
|/Managers/Devices/backups/DELETE|Odstraní zálohovacího skladu|
|/Managers/Devices/backups/Elements/clone/Action|Klonování sdílené složky nebo svazku pomocí zálohování elementu.|
|/Managers/Devices/backups/Read|Uvádí nebo získá zálohovacího skladu|
|/managers/devices/backups/restore/action|Všechny svazky obnovte ze zálohovacího skladu.|
|/managers/devices/backupScheduleGroups/delete|Odstraní plán zálohování skupiny|
|/managers/devices/backupScheduleGroups/read|Uvádí nebo získá skupiny plánu zálohování|
|/managers/devices/backupScheduleGroups/write|Vytvořit nebo aktualizovat skupiny plánu zálohování|
|/managers/devices/chapSettings/delete|Odstraní nastavení Chap|
|/managers/devices/chapSettings/read|Uvádí nebo získá nastavení Chap|
|/managers/devices/chapSettings/write|Vytvořit nebo aktualizovat nastavení Chap|
|/Managers/Devices/Deactivate/Action|Deaktivuje zařízení.|
|/Managers/Devices/DELETE|Odstraní zařízení|
|/Managers/Devices/download/Action|Stažení aktualizací pro zařízení.|
|/Managers/Devices/Failover/Action|Převzetí služeb při selhání zařízení.|
|/managers/devices/fileservers/backup/action|Proveďte zálohování souborového serveru.|
|/managers/devices/fileservers/delete|Odstraní souborových serverů|
|/managers/devices/fileservers/metrics/read|Uvádí nebo získá metriky|
|/managers/devices/fileservers/metricsDefinitions/read|Uvádí nebo získá definice metrik|
|/managers/devices/fileservers/read|Uvádí nebo získá souborových serverů|
|/managers/devices/fileservers/shares/delete|Odstraní sdílené složky|
|/managers/devices/fileservers/shares/metrics/read|Uvádí nebo získá metriky|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Uvádí nebo získá definice metrik|
|/managers/devices/fileservers/shares/read|Uvádí nebo získá sdílené složky|
|/managers/devices/fileservers/shares/write|Vytvořit nebo aktualizovat sdílené složky|
|/managers/devices/fileservers/write|Vytvořit nebo aktualizovat souborových serverů|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Změnit stav napájení řadiče hardwaru skupiny součástí|
|/managers/devices/hardwareComponentGroups/read|Seznam skupin součástí hardwaru|
|/managers/devices/install/action|Instalaci aktualizací na zařízení.|
|/managers/devices/installUpdates/action|Nainstaluje aktualizace zařízení|
|/managers/devices/iscsiservers/backup/action|Proveďte zálohování serveru iSCSI.|
|/managers/devices/iscsiservers/delete|Odstraní iSCSI servery|
|/managers/devices/iscsiservers/disks/delete|Odstraní disky|
|/managers/devices/iscsiservers/disks/metrics/read|Uvádí nebo získá metriky|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Uvádí nebo získá definice metrik|
|/managers/devices/iscsiservers/disks/read|Uvádí nebo získá disky|
|/managers/devices/iscsiservers/disks/write|Vytvořit nebo aktualizovat disky|
|/managers/devices/iscsiservers/metrics/read|Uvádí nebo získá metriky|
|/managers/devices/iscsiservers/metricsDefinitions/read|Uvádí nebo získá definice metrik|
|/managers/devices/iscsiservers/read|Uvádí nebo získá iSCSI servery|
|/Managers/Devices/iscsiservers/Write|Vytvořit nebo aktualizovat iSCSI servery|
|/Managers/Devices/Jobs/Cancel/Action|Zrušení spuštěné úlohy|
|/Managers/Devices/Jobs/Read|Uvádí nebo získá úlohy|
|/managers/devices/listFailoverSets/action|Zobrazí seznam sady převzetí služeb při selhání ze stávajících zařízení.|
|/managers/devices/listFailoverTargets/action|Seznam cílů převzetí služeb při selhání zařízení|
|/Managers/Devices/Metrics/Read|Uvádí nebo získá metriky|
|/managers/devices/metricsDefinitions/read|Uvádí nebo získá definice metrik|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Potvrzuje úspěšné migrace a potvrďte ho.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Načtěte stav potvrdit migrace.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Načtěte stav odhad úlohy migrace.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Načtěte stav migrace.|
|/managers/devices/migrationSourceConfigurations/import/action|Import konfigurace zdroje pro migraci.|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Spustit migraci pomocí zdroj konfigurace|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Spustíte úlohu k odhad doby trvání procesu migrace.|
|/managers/devices/networkSettings/read|Uvádí nebo získá nastavení sítě|
|/managers/devices/networkSettings/write|Vytvoří novou nebo aktualizovat nastavení sítě|
|/managers/devices/publicEncryptionKey/action|Seznam veřejný šifrovací klíč Správce zařízení|
|/managers/devices/publishSupportPackage/action|Publikujte balíček pro podporu zařízení pro řešení potíží s Microsoft Support.|
|/Managers/Devices/Read|Uvádí nebo získá zařízení|
|/managers/devices/scanForUpdates/action|Vyhledávání aktualizací v zařízení.|
|/Managers/Devices/securitySettings/Read|Seznam nastavení zabezpečení|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Synchronizujte certifikát vzdálené správy pro zařízení.|
|/Managers/Devices/securitySettings/Update/Action|Aktualizujte nastavení zabezpečení.|
|/Managers/Devices/securitySettings/Write|Vytvoří novou nebo aktualizuje nastavení zabezpečení|
|/managers/devices/sendTestAlertEmail/action|Odeslat testovací výstrahy e-mailu k příjemce nakonfigurované e-mailů.|
|/managers/devices/timeSettings/read|Uvádí nebo získá nastavení času|
|/managers/devices/timeSettings/write|Vytvoří novou nebo aktualizuje nastavení času|
|/Managers/Devices/updateSummary/Read|Uvádí nebo získá souhrn aktualizace|
|/Managers/Devices/volumeContainers/DELETE|Odstraní existující kontejnery svazků (pouze řady 8000)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Seznam šifrování klíče kontejnery svazků|
|/Managers/Devices/volumeContainers/Metrics/Read|Seznam metriky|
|/Managers/Devices/volumeContainers/metricsDefinitions/Read|Seznam definice metrik|
|/Managers/Devices/volumeContainers/Read|Seznam kontejnery svazků (pouze 8000 řada)|
|/Managers/Devices/volumeContainers/rolloverEncryptionKey/Action|Šifrování klíče výměny kontejnery svazků|
|/Managers/Devices/volumeContainers/Volumes/DELETE|Odstraní existující svazky|
|/Managers/Devices/volumeContainers/Volumes/Metrics/Read|Seznam metriky|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Seznam definice metrik|
|/Managers/Devices/volumeContainers/Volumes/Read|Seznam svazků|
|/Managers/Devices/volumeContainers/Volumes/Write|Vytvoří novou nebo aktualizuje svazky|
|/Managers/Devices/volumeContainers/Write|Vytvoří novou nebo aktualizuje kontejnery svazků (pouze řady 8000)|
|/Managers/Devices/Write|Vytvořit nebo aktualizovat zařízení|
|/Managers/encryptionSettings/Read|Uvádí nebo získá nastavení šifrování|
|/Managers/extendedInformation/delete|Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor.|
|/Managers/extendedInformation/read|Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor.|
|/Managers/extendedInformation/write|Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor.|
|/managers/getActivationKey/action|Získáte aktivační klíč pro správce zařízení.|
|/managers/getEncryptionKey/action|Získání šifrovacího klíče pro správce zařízení.|
|/managers/listActivationKey/action|Získá aktivační klíč Správce zařízení StorSimple.|
|/managers/listPrivateEncryptionKey/action|Získá privátní šifrovací klíč pro správce zařízení StorSimple.|
|/managers/listPublicEncryptionKey/action|Seznam veřejný šifrovací klíče nástroje Správce zařízení StorSimple.|
|/Managers/Metrics/Read|Uvádí nebo získá metriky|
|/Managers/metricsDefinitions/Read|Uvádí nebo získá definice metrik|
|/Managers/provisionCloudAppliance/Action|Vytvořte nové zařízení cloudu.|
|/Managers/Read|Uvádí nebo získá správce zařízení|
|/ Správci/čtení|Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru.|
|/managers/regenarateRegistationCertificate/action|Znovu vygenerujte registrační certifikát pro správce zařízení.|
|/managers/regenerateActivationKey/action|Znovu vygenerujte aktivační klíč pro správce zařízení.|
|/Managers/storageAccountCredentials/DELETE|Odstraní údaje k účtu úložiště|
|/managers/storageAccountCredentials/listAccessKey/action|Seznam přístupových klíčů přihlašovacích údajů účtu úložiště|
|/Managers/storageAccountCredentials/Read|Uvádí nebo získá přihlašovací údaje účtu úložiště|
|/Managers/storageAccountCredentials/Write|Vytvořit nebo aktualizovat údaje k účtu úložiště|
|/managers/storageDomains/delete|Odstraní domén úložiště|
|/Managers/storageDomains/Read|Uvádí nebo získá domén úložiště|
|/managers/storageDomains/write|Vytvořit nebo aktualizovat domén úložiště|
|/ Správci a zápis|Vytvořit nebo aktualizovat Správci zařízení|
|A správci/zápis|Operace Vytvořit trezor vytvoří prostředek Azure typu trezor.|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operace | Popis |
|---|---|
|/locations/quotas/Read|Kvóty předplatného analýzy datového proudu pro čtení|
|/ operací/čtení|Read Stream Analytics Operations|
|/ Registrace nebo akce|Registrovat předplatné u poskytovatele prostředků Stream Analytics|
|/ streamingjobs/odstranit|Odstranit úlohy Stream Analytics|
|/streamingjobs/Functions/DELETE|Odstranit funkci úlohy Stream Analytics|
|/streamingjobs/Functions/operationresults/Read|Přečtěte si výsledky operace pro funkci úlohy Stream Analytics|
|/streamingjobs/Functions/Read|Číst Stream Analytics úlohy funkce|
|/streamingjobs/Functions/RetrieveDefaultDefinition/Action|Načíst výchozí definici funkce úlohy Stream Analytics|
|/streamingjobs/Functions/test/Action|Test Stream Analytics úlohy funkce|
|/streamingjobs/Functions/Write|Write – funkce úlohy Stream Analytics|
|/streamingjobs/inputs/DELETE|Odstranit vstupu úlohy Stream Analytics|
|/streamingjobs/inputs/operationresults/Read|Výsledky operace čtení pro vstup úlohy Stream Analytics|
|/streamingjobs/inputs/Read|Číst Stream Analytics úlohy vstup|
|/streamingjobs/inputs/Sample/Action|Ukázka Stream Analytics úlohy vstup|
|/streamingjobs/inputs/test/Action|Test Stream Analytics úlohy vstup|
|/streamingjobs/inputs/Write|Zápis vstupu úlohy Stream Analytics|
|/streamingjobs/metricdefinitions/Read|Číst definice metrik|
|/streamingjobs/operationresults/Read|Přečtěte si výsledky operace úlohy Stream Analytics|
|/streamingjobs/Outputs/DELETE|Odstranit výstup úlohy Stream Analytics|
|/streamingjobs/Outputs/operationresults/Read|Výsledky operace čtení pro výstup úlohy Stream Analytics|
|/streamingjobs/Outputs/Read|Výstup úlohy analýzy datového proudu pro čtení|
|/streamingjobs/Outputs/test/Action|Výstup úlohy analýzy datového proudu testu|
|/streamingjobs/Outputs/Write|Zapisovat výstup úlohy Stream Analytics|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Nastavení diagnostiky pro čtení.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Zápis nastavení diagnostiky.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/Read|Získá dostupné protokoly pro streamingjobs|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro streamingjobs|
|/ streamingjobs/čtení|Read Stream Analytics Job|
|/streamingjobs/Start/Action|Start Stream Analytics Job|
|/streamingjobs/stop/Action|Stop Stream Analytics Job|
|/streamingjobs/Transformations/DELETE|Odstranit transformaci úlohy Stream Analytics|
|/streamingjobs/Transformations/Read|Číst Stream Analytics úlohy transformace|
|/streamingjobs/Transformations/Write|Zápis transformace úlohy Stream Analytics|
|/ streamingjobs/zápisu|Write Stream Analytics Job|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Operace | Popis |
|---|---|
|/ SubscriptionDefinitions/čtení|Získáte definici předplatného Azure v rámci skupiny pro správu.|
|/ SubscriptionDefinitions/zápis|Vytvořit definici předplatného Azure|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operace | Popis |
|---|---|
|/ registrace nebo akce|Registruje zprostředkovatele prostředků podpory.|
|/supportTickets/Read|Umožňuje získat podrobnosti lístku podpory (včetně stavu, závažnosti, podrobností kontaktu a komunikací) nebo seznam lístků podpory pro všechna předplatná.|
|/supportTickets/write|Vytvoří nebo aktualizuje lístek podpory. Můžete vytvořit lístek podpory pro Technical, fakturace, kvóty nebo správy předplatného související problémy. Můžete aktualizovat závažnost, podrobnosti kontaktu a komunikace pro existující lístky žádostí o podporu.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Operace | Popis |
|---|---|
|/environments/accesspolicies/delete|Odstraní zásady přístupu.|
|/Environments/accesspolicies/Read|Umožňuje získáte vlastnosti zásady přístupu.|
|/Environments/accesspolicies/Write|Vytvoří nové zásady přístupu pro prostředí nebo aktualizuje existující zásady přístupu.|
|/Environments/DELETE|Odstraní prostředí.|
|/Environments/eventsources/DELETE|Odstraní zdroj události.|
|/Environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings a zápis|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro eventsources|
|/Environments/eventsources/Read|Umožňuje získáte vlastnosti zdroje událostí.|
|/Environments/eventsources/Write|Vytvoří nový zdroj událostí pro prostředí nebo aktualizuje existující zdroje událostí.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Načte dostupné metriky pro prostředí|
|/Environments/Read|Umožňuje získáte vlastnosti prostředí.|
|/environments/referencedatasets/delete|Odstraní odkaz na datovou sadu.|
|/Environments/referencedatasets/Read|Umožňuje získáte vlastnosti referenční datové sady.|
|/Environments/referencedatasets/Write|Vytvoří nový odkaz datové sady pro prostředí nebo aktualizuje existující sadu dat odkaz.|
|/Environments/status/Read|Načíst stav prostředí, stav jeho přidružené operací, jako je příjem příchozích dat.|
|/ prostředí a zápis|Vytvoří nového prostředí nebo aktualizuje stávající prostředí.|
|/ registrace nebo akce|Zaregistruje předplatné pro poskytovatele prostředků časové řady přehledy a povolí vytvoření prostředí Statistika časové řady.|

## <a name="microsoftweb"></a>microsoft.web

| Operace | Popis |
|---|---|
|/apimanagementaccounts/apiacls/read|Získáte rozhraní Api správy účtů Apiacls.|
|/apimanagementaccounts/apis/apiacls/delete|Odstraňte rozhraní Api správy účtů rozhraní API Apiacls.|
|/apimanagementaccounts/apis/apiacls/read|Získáte rozhraní Api správy účtů rozhraní API Apiacls.|
|/apimanagementaccounts/apis/apiacls/write|Aktualizujte rozhraní Api správy účtů rozhraní API Apiacls.|
|/apimanagementaccounts/APIs/connectionacls/Read|Získáte rozhraní Api správy účtů rozhraní API Connectionacls.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Potvrďte souhlas kódu rozhraní Api správy účtů rozhraní API pro připojení.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Odstraňte rozhraní Api správy účtů rozhraní API připojení Connectionacls.|
|/apimanagementaccounts/APIs/Connections/connectionacls/Read|Získáte rozhraní Api správy účtů rozhraní API připojení Connectionacls.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Aktualizujte rozhraní Api správy účtů rozhraní API připojení Connectionacls.|
|/apimanagementaccounts/APIs/Connections/DELETE|Odstraňte rozhraní Api správy účtů rozhraní API pro připojení.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Získáte odkazy souhlasu pro rozhraní Api správy účtů rozhraní API pro připojení.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Seznam připojení klíče rozhraní Api správy účtů rozhraní API pro připojení.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Seznam tajné klíče rozhraní Api správy účtů rozhraní API pro připojení.|
|/apimanagementaccounts/APIs/Connections/Read|Získáte rozhraní Api správy účtů rozhraní API pro připojení.|
|/apimanagementaccounts/APIs/Connections/Write|Aktualizujte připojení rozhraní Api správy účtů rozhraní API.|
|/apimanagementaccounts/apis/delete|Odstraňte rozhraní API účty pro správu rozhraní Api.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Odstranit rozhraní Api správy rozhraní API pro účty lokalizované definice.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Získat rozhraní Api správy rozhraní API pro účty lokalizované definice.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Rozhraní API účty pro správu rozhraní Api aktualizace lokalizované definice.|
|/apimanagementaccounts/APIs/Read|Získáte rozhraní API účty pro správu rozhraní Api.|
|/apimanagementaccounts/apis/write|Aktualizujte rozhraní API účty pro správu rozhraní Api.|
|/apimanagementaccounts/connectionacls/read|Získáte rozhraní Api správy účtů Connectionacls.|
|/availablestacks/Read|Získáte zásobníky k dispozici.|
|/billingmeters/Read|Získejte seznam fakturace měřidla.|
|/ certifikátů/odstranit|Odstraňte existující certifikát.|
|/ certifikátů/čtení|Získáte seznam certifikátů.|
|/ certifikátů/zápisu|Přidat nový certifikát nebo aktualizovat existující.|
|/checknameavailability/Read|Zkontrolujte, zda název prostředku je k dispozici.|
|/classicmobileservices/read|Získáte Classic mobilních služeb.|
|/ connectionGateways/odstranit|Odstraní připojení brány.|
|/connectionGateways/JOIN/Action|Spojí připojení brány.|
|/connectiongateways/liststatus/Action|Seznam stav připojení brány.|
|/connectionGateways/ListStatus/Action|Zobrazí stav připojení brány.|
|/connectionGateways/Move/Action|Přesune připojení brány.|
|/connectionGateways/Read|Získejte seznam připojení brány.|
|/ connectionGateways/zápisu|Vytvoří nebo aktualizuje připojení brány.|
|/connections/confirmconsentcode/action|Potvrďte souhlas kód připojení.|
|/ připojení/odstranit|Odstraní připojení.|
|/Connections/JOIN/Action|Spojí připojení.|
|/Connections/listconsentlinks/Action|Seznam odkazů souhlasu pro připojení.|
|/Connections/Move/Action|Přesune připojení.|
|/ připojení/čtení|Získejte seznam připojení.|
|/ připojení/zápisu|Vytvoří nebo aktualizuje připojení.|
|/ customApis/odstranit|Odstraní vlastní rozhraní API.|
|/customApis/extractApiDefinitionFromWsdl/Action|Definice rozhraní API extrahuje z WSDL.|
|/customApis/JOIN/Action|Spojí vlastní rozhraní API.|
|/customApis/listWsdlInterfaces/Action|Seznam WSDL rozhraní pro vlastní API.|
|/customApis/Move/Action|Přesune vlastní rozhraní API.|
|/ customApis/čtení|Získejte seznam vlastní rozhraní API.|
|/ customApis/zápisu|Vytvoří nebo aktualizuje vlastní API.|
|/deploymentlocations/Read|Získáte umístění nasazení.|
|/ geoRegions/čtení|Získejte seznam geografických oblastech.|
|/hostingenvironments/capacities/read|Získat hostování prostředí kapacity.|
|/hostingEnvironments/Delete|Odstranění služby App Service Environment|
|/hostingenvironments/diagnostics/read|Získat hostování prostředí diagnostiky.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Získáte koncových bodů sítě všechny příchozí závislostí.|
|/hostingenvironments/metricdefinitions/read|Získat hostování prostředí definice metrik.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Získat hostování definice metrik u fondy prostředí.|
|/hostingenvironments/multirolepools/metrics/read|Get Hosting Environments MultiRole Pools Metrics.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Načte dostupné metriky pro MultiRole prostředí App Service|
|/hostingEnvironments/multiRolePools/Read|Umožňuje získat vlastnosti fondu front-endu ve službě App Service Environment|
|/hostingenvironments/multirolepools/skus/read|Získat hostování prostředí u fondy SKU.|
|/hostingenvironments/multirolepools/usages/read|Získat hostování prostředí u fondy použití.|
|/hostingEnvironments/multiRolePools/Write|Vytvořit nový fond front-endové služby App Service Environment, nebo aktualizaci existující|
|/hostingenvironments/operations/read|Získat hostování prostředí operace.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/Read|Získáte koncových bodů sítě všechny odchozí závislostí.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/hostingEnvironments/Read|Umožňuje získat vlastnosti služby App Service Environment|
|/hostingEnvironments/reboot/Action|Restartujte všechny počítače ve službě App Service Environment|
|/hostingenvironments/resume/action|Obnovte hostitelská prostředí.|
|/hostingenvironments/serverfarms/read|Get Hosting Environments App Service Plans.|
|/hostingenvironments/sites/read|Get Hosting Environments Web Apps.|
|/hostingenvironments/suspend/Action|Suspend Hosting Environments.|
|/hostingenvironments/usages/read|Získat hostování použití prostředí.|
|/hostingenvironments/workerpools/metricdefinitions/Read|Získat hostování prostředí Workerpools metrika definice.|
|/hostingenvironments/workerpools/Metrics/Read|Získat hostování prostředí Workerpools metriky.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro WorkerPool prostředí App Service|
|/hostingEnvironments/workerPools/Read|Umožňuje získat vlastnosti fondu pracovních procesů ve službě App Service Environment|
|/hostingenvironments/workerpools/skus/read|Získat hostování prostředí Workerpools SKU.|
|/hostingenvironments/workerpools/usages/read|Získat hostování prostředí Workerpools použití.|
|/hostingEnvironments/workerPools/Write|Vytvořit nový fond pracovních procesů ve službě App Service Environment nebo aktualizovat stávající|
|/hostingEnvironments/Write|Vytvoření nové služby App Service Environment nebo aktualizovat existující|
|/ishostingenvironmentnameavailable/read|Získáte, pokud je k dispozici hostování název prostředí.|
|/ishostnameavailable/read|Zkontrolujte, zda název hostitele je k dispozici.|
|/isusernameavailable/Read|Zkontrolujte, zda uživatelské jméno je k dispozici.|
|/listSitesAssignedToHostName/Read|Získáte názvy lokalit, které jsou přiřazeny k hostitele.|
|/Locations/apioperations/Read|Získejte operace umístění rozhraní API.|
|/Locations/connectiongatewayinstallations/Read|Získáte umístění připojení brány instalace.|
|/locations/extractapidefinitionfromwsdl/action|Extrahuje definice rozhraní Api z WSDL pro umístění.|
|/Locations/listwsdlinterfaces/Action|Seznam WSDL rozhraní pro umístění.|
|/Locations/managedapis/apioperations/Read|Získáte operace umístění spravované rozhraní API.|
|/Locations/managedapis/JOIN/Action|Spojí spravované rozhraní API.|
|/Locations/managedapis/Read|Získáte umístění spravovaná rozhraní API.|
|/Operations/Read|Získejte operace.|
|/publishingusers/Read|Uživatelé získat publikování.|
|/ publishingusers/zápisu|Aktualizace publikování uživatelů.|
|/ doporučení/čtení|Získejte seznam doporučení pro předplatné.|
|/ registrace nebo akce|Registrace poskytovatele prostředků Microsoft.Web pro předplatné.|
|/resourcehealthmetadata/Read|Získáte Metadata stavu prostředků.|
|/serverfarms/capabilities/read|Získáte možnosti plány služby App Service.|
|/serverfarms/Delete|Odstranit existující plán služby App Service|
|/serverfarms/firstpartyapps/settings/delete|Odstraňte první strany aplikace nastavení plánů služby App Service.|
|/serverfarms/firstpartyapps/settings/read|Získáte nastavení první strany aplikací plány služby App Service.|
|/serverfarms/firstpartyapps/settings/write|Aktualizujte nastavení první strany aplikací plány služby App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Odstranění služby App Service plány hybridní připojení předávací službu obory názvů.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Získáte služby App Service plány hybridní připojení předávací službu obory názvů.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Získáte plány služby App Service hybridní připojení obory názvů předávací webové aplikace.|
|/serverfarms/hybridconnectionplanlimits/read|Získáte limity plán služby App Service plány hybridní připojení.|
|/serverfarms/hybridconnectionrelays/read|Získáte předávací připojení hybridní plány služby App Service.|
|/serverfarms/metricdefinitions/Read|Získáte definice metrik plány služby App Service.|
|/serverfarms/Metrics/Read|Získáte metriky plány služby App Service.|
|/serverfarms/operationresults/Read|Získáte výsledky operace plány služby App Service.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro plán služby App Service|
|/ serverfarms/čtení|Umožňuje získat vlastnosti na plán služby App Service|
|/serverfarms/restartSites/Action|Restartujte všechny webové aplikace v plán služby App Service|
|/serverfarms/sites/read|Get App Service Plans Web Apps.|
|/serverfarms/skus/read|Získáte SKU plány služby App Service.|
|/serverfarms/usages/Read|Získáte použití plány služby App Service.|
|/serverfarms/virtualnetworkconnections/gateways/write|Aktualizace brány připojení virtuální sítě plány služby App Service.|
|/serverfarms/virtualnetworkconnections/read|Získání připojení virtuální sítě plány služby App Service.|
|/serverfarms/virtualnetworkconnections/routes/delete|Odstraňte směrování připojení virtuální sítě plány služby App Service.|
|/serverfarms/virtualnetworkconnections/Routes/Read|Získáte trasy připojení virtuální sítě plány služby App Service.|
|/serverfarms/virtualnetworkconnections/Routes/Write|Aktualizace tras připojení virtuální sítě plány služby App Service.|
|/serverfarms/workers/reboot/Action|Restartování pracovních procesů plány služby App Service.|
|/ serverfarms/zápisu|Nový plán služby App Service umožňuje vytvořit nebo aktualizovat stávající|
|/sites/analyzecustomhostname/read|Analýza vlastní název hostitele.|
|/Sites/applySlotConfig/Action|Použít konfiguraci webové aplikace slotu z cílového slotu na aktuální webové aplikace|
|/Sites/Backup/Action|Vytvoří novou zálohu webové aplikace|
|/ weby/zálohování/čtení|Získáte zálohování webové aplikace.|
|/sites/backup/write|Aktualizace webové aplikace zálohování.|
|/Sites/backups/DELETE|Odstraňte webových aplikací zálohování.|
|/sites/backups/list/action|Seznam webových aplikací zálohování.|
|/Sites/backups/Read|Umožňuje získat vlastnosti zálohování webové aplikace|
|/sites/backups/restore/action|Obnovte zálohy webové aplikace.|
|/sites/config/delete|Odstraňte konfigurační webové aplikace.|
|/sites/config/list/Action|Seznam webové aplikace citlivé nastavení zabezpečení, jako je například publikování přihlašovací údaje, nastavení aplikace a připojovacích řetězců|
|/sites/config/Read|Získat nastavení konfigurace webové aplikace|
|/sites/config/Write|Aktualizovat nastavení konfigurace webové aplikace|
|/sites/continuouswebjobs/delete|Odstranění webové aplikace nepřetržité webové úlohy.|
|/Sites/continuouswebjobs/Read|Získáte webové aplikace nepřetržité webové úlohy.|
|/Sites/continuouswebjobs/Start/Action|Spuštění webové aplikace nepřetržité webové úlohy.|
|/sites/continuouswebjobs/stop/action|Zastavení webové aplikace nepřetržité webové úlohy.|
|/ weby/odstranit|Odstranit stávající webovou aplikaci|
|/Sites/Deployments/DELETE|Odstraňte nasazení webové aplikace.|
|/Sites/Deployments/log/Read|Získání protokolu nasazení webové aplikace.|
|/Sites/Deployments/Read|Získá nasazení webové aplikace.|
|/Sites/Deployments/Write|Aktualizace nasazení webové aplikace.|
|/Sites/Diagnostics/analyses/Execute/Action|Run Web Apps Diagnostics Analysis.|
|/Sites/Diagnostics/analyses/Read|Získáte webové analýzy diagnostiky aplikace.|
|/sites/diagnostics/aspnetcore/read|Získání diagnostiky webové aplikace pro aplikaci ASP.NET Core.|
|/sites/diagnostics/autoheal/read|Get Web Apps Diagnostics Autoheal.|
|/Sites/Diagnostics/Deployment/Read|Získání diagnostiky aplikací nasazení webu.|
|/Sites/Diagnostics/Deployments/Read|Získá nasazení webové aplikace diagnostiky.|
|/sites/diagnostics/detectors/execute/Action|Spuštění webové aplikace diagnostiky detektor.|
|/Sites/Diagnostics/detectors/Read|Získáte detektor webové aplikace diagnostiky.|
|/sites/diagnostics/failedrequestsperuri/read|Získáte webové aplikace diagnostiky neúspěšných požadavků na identifikátor Uri.|
|/sites/diagnostics/frebanalysis/read|Získáte webové analýzy FREB diagnostiky aplikace.|
|/Sites/Diagnostics/loganalyzer/Read|Získáte webové aplikace diagnostiky protokolu analyzátoru.|
|/sites/diagnostics/read|Get Web Apps Diagnostics Categories.|
|/sites/diagnostics/runtimeavailability/read|Získáte dostupnosti webové aplikace diagnostiky modulu Runtime.|
|/sites/diagnostics/servicehealth/read|Get Web Apps Diagnostics Service Health.|
|/sites/diagnostics/sitecpuanalysis/read|Získáte analýza procesoru webu webové aplikace diagnostiky.|
|/Sites/Diagnostics/sitecrashes/Read|Získáte webové aplikace diagnostiky webu dojde k chybě.|
|/sites/diagnostics/sitelatency/read|Získáte webové aplikace diagnostiky lokality latence.|
|/sites/diagnostics/sitememoryanalysis/read|Získáte analýza paměti webu webové aplikace diagnostiky.|
|/sites/diagnostics/siterestartsettingupdate/read|Získáte aktualizace nastavení webové aplikace diagnostiky lokality restartování.|
|/Sites/Diagnostics/siterestartuserinitiated/Read|Získáte webové aplikace diagnostiky lokality restartování uživatelem iniciované.|
|/sites/diagnostics/siteswap/read|Získáte prohození webové aplikace diagnostiky lokality.|
|/sites/diagnostics/threadcount/read|Získáte počet vláken webové aplikace diagnostiky.|
|/Sites/Diagnostics/workeravailability/Read|Získáte Workeravailability webové aplikace diagnostiky.|
|/Sites/Diagnostics/workerprocessrecycle/Read|Získáte recyklace procesu Worker webové aplikace diagnostiky.|
|/Sites/domainownershipidentifiers/Read|Získání identifikátorů vlastnictví webové aplikace domény.|
|/Sites/domainownershipidentifiers/Write|Aktualizace webové aplikace domény vlastnictví identifikátory.|
|/Sites/Functions/Action|Funkce Web Apps.|
|/Sites/Functions/DELETE|Odstraňte funkce webové aplikace.|
|/Sites/Functions/listsecrets/Action|Funkce, webové aplikace seznamu tajných klíčů.|
|/Sites/Functions/masterkey/Read|Získáte Masterkey funkce webové aplikace.|
|/Sites/Functions/Read|Získáte funkce Web Apps.|
|/Sites/Functions/token/Read|Get webové aplikace funkce Token.|
|/Sites/Functions/Write|Aktualizace funkcí webové aplikace.|
|/sites/hostnamebindings/delete|Odstranění webové aplikace Hostname vazby.|
|/sites/hostnamebindings/read|Získáte webové aplikace Hostname vazby.|
|/Sites/hostnamebindings/Write|Update Web Apps Hostname Bindings.|
|/sites/hybridconnection/delete|Odstranění webové aplikace hybridní připojení.|
|/sites/hybridconnection/read|Získáte webové aplikace hybridní připojení.|
|/sites/hybridconnection/write|Aktualizace webové aplikace hybridní připojení.|
|/sites/hybridconnectionnamespaces/relays/delete|Odstranění webové aplikace hybridní připojení obory názvů předávání.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Seznam klíčů webové aplikace hybridní připojení obory názvů předávání.|
|/sites/hybridconnectionnamespaces/relays/read|Získáte předávací službu obory názvů webové aplikace hybridní připojení.|
|/sites/hybridconnectionnamespaces/relays/write|Aktualizace webové aplikace hybridní připojení obory názvů předávání.|
|/sites/hybridconnectionrelays/read|Získáte webové aplikace hybridní připojení předávání.|
|/Sites/instances/Deployments/DELETE|Odstraňte nasazení instance webové aplikace.|
|/Sites/instances/Deployments/Read|Získá nasazení instance webové aplikace.|
|/sites/instances/extensions/log/read|Získáte webové aplikace instance rozšíření protokolu.|
|/sites/instances/extensions/read|Získáte rozšíření instance webových aplikací.|
|/sites/instances/processes/delete|Odstraňte procesy instance webové aplikace.|
|/sites/instances/processes/read|Získáte procesy instance webové aplikace.|
|/sites/instances/read|Získá instance webové aplikace.|
|/Sites/listsyncfunctiontriggerstatus/Action|Seznam synchronizace funkce aktivační událost stavu webových aplikací.|
|/Sites/metricdefinitions/Read|Získáte webové aplikace metrika definice.|
|/Sites/Metrics/Read|Získáte metriky webové aplikace.|
|/Sites/metricsdefinitions/Read|Získáte definice webové aplikace metriky.|
|/sites/migratemysql/action|Migrate MySql Web Apps.|
|/sites/migratemysql/read|Get Web Apps Migrate MySql.|
|/Sites/networktrace/Action|Webové aplikace, síťové trasování.|
|/Sites/newpassword/Action|Nové_heslo webové aplikace.|
|/Sites/operationresults/Read|Získáte výsledky operace webové aplikace.|
|/Sites/Operations/Read|Získejte operace webové aplikace.|
|/Sites/perfcounters/Read|Získáte čítače výkonu webové aplikace.|
|/sites/premieraddons/delete|Odstraňte rozšíření Premier webové aplikace.|
|/sites/premieraddons/read|Získáte rozšíření Premier webové aplikace.|
|/Sites/premieraddons/Write|Aktualizace doplňků Premier webové aplikace.|
|/Sites/Processes/Read|Získáte procesy webové aplikace.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro webovou aplikaci|
|/Sites/publiccertificates/DELETE|Odstranění webové aplikace veřejné certifikáty.|
|/Sites/publiccertificates/Read|Získáte webové aplikace veřejné certifikáty.|
|/Sites/publiccertificates/Write|Aktualizace webové aplikace veřejné certifikáty.|
|/Sites/Publish/Action|Publikování webové aplikace|
|/sites/publishxml/Action|Získat publikování profil xml pro webovou aplikaci|
|/sites/publishxml/read|Získat publikování webové aplikace XML.|
|/ weby/čtení|Umožňuje získat vlastnosti webové aplikace|
|/Sites/recommendationhistory/Read|Zobrazit historii doporučení webové aplikace.|
|/Sites/Recommendations/disable/Action|Zakážete doporučení webové aplikace.|
|/Sites/Recommendations/Read|Získejte seznam doporučení pro webovou aplikaci.|
|/Sites/Recover/Action|Recover Web Apps.|
|/Sites/resetSlotConfig/Action|Resetovat konfiguraci webové aplikace|
|/Sites/resourcehealthmetadata/Read|Získáte Metadata stavu webových aplikací prostředků.|
|/Sites/restart/Action|Restartování webové aplikace|
|/Sites/Restore/Read|Získáte obnovení webové aplikace.|
|/Sites/Restore/Write|Obnovení webové aplikace.|
|/Sites/siteextensions/DELETE|Odstraňte rozšíření webové aplikace webů.|
|/Sites/siteextensions/Read|Získáte rozšíření webové aplikace webů.|
|/Sites/siteextensions/Write|Aktualizujte rozšíření webové aplikace webů.|
|/sites/slots/analyzecustomhostname/read|Získat webové aplikace sloty analyzovat vlastní název hostitele.|
|/sites/slots/applySlotConfig/Action|Použít konfiguraci webové aplikace slotu z cílového slotu na aktuální pozici.|
|/sites/slots/backup/Action|Vytvoří novou zálohu Slot webové aplikace.|
|/sites/slots/backup/read|Získáte zálohování sloty webové aplikace.|
|/sites/slots/backup/write|Aktualizujte zálohování sloty webové aplikace.|
|/sites/slots/backups/delete|Odstraňte zálohy sloty webové aplikace.|
|/sites/slots/backups/list/action|Seznam webových aplikací sloty zálohování.|
|/sites/slots/backups/Read|Umožňuje získat vlastnosti zálohování se sloty webové aplikace|
|/sites/slots/backups/restore/action|Obnovte zálohy sloty webové aplikace.|
|/sites/slots/config/delete|Odstraňte konfigurační sloty webové aplikace.|
|/sites/slots/config/list/Action|Seznam Slot webové aplikace citlivé nastavení zabezpečení, jako je například publikování přihlašovací údaje, nastavení aplikace a připojovacích řetězců|
|/sites/slots/config/Read|Získat nastavení konfigurace Slot webové aplikace|
|/sites/slots/config/Write|Aktualizovat nastavení konfigurace Slot webové aplikace|
|/sites/slots/continuouswebjobs/delete|Odstranění webové aplikace sloty nepřetržité webové úlohy.|
|/Sites/slots/continuouswebjobs/Read|Získáte webové aplikace sloty nepřetržité webové úlohy.|
|/Sites/slots/continuouswebjobs/Start/Action|Spuštění webové aplikace sloty nepřetržité webové úlohy.|
|/sites/slots/continuouswebjobs/stop/action|Zastavení webové aplikace sloty nepřetržité webové úlohy.|
|/Sites/slots/DELETE|Odstraňte existující Slot webové aplikace|
|/Sites/slots/Deployments/DELETE|Odstraňte nasazení sloty webové aplikace.|
|/Sites/slots/Deployments/log/Read|Získáte webové aplikace sloty nasazení protokolu.|
|/Sites/slots/Deployments/Read|Získá nasazení sloty webové aplikace.|
|/Sites/slots/Deployments/Write|Aktualizace nasazení sloty webové aplikace.|
|/Sites/slots/Diagnostics/analyses/Execute/Action|Spuštění webové aplikace sloty diagnostiky analýzy.|
|/Sites/slots/Diagnostics/analyses/Read|Získáte webové aplikace sloty diagnostiky analýzy.|
|/sites/slots/diagnostics/aspnetcore/read|Získání diagnostiky sloty webové aplikace pro aplikaci ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Získáte funkce Autoheal diagnostiky webové aplikace sloty.|
|/Sites/slots/Diagnostics/Deployment/Read|Získáte nasazení diagnostiky sloty webové aplikace.|
|/Sites/slots/Diagnostics/Deployments/Read|Získá webové aplikace sloty diagnostiky nasazení.|
|/sites/slots/diagnostics/detectors/execute/Action|Spuštění webové aplikace sloty diagnostiky detektor.|
|/Sites/slots/Diagnostics/detectors/Read|Získáte webové aplikace sloty diagnostiky detektor.|
|/Sites/slots/Diagnostics/frebanalysis/Read|Získáte webové aplikace sloty diagnostiky FREB analýzy.|
|/Sites/slots/Diagnostics/loganalyzer/Read|Získáte webové aplikace sloty diagnostiky protokolu analyzátoru.|
|/sites/slots/diagnostics/read|Získání diagnostiky sloty webové aplikace.|
|/sites/slots/diagnostics/runtimeavailability/read|Získáte webové aplikace sloty diagnostiky běhovou dostupnost.|
|/sites/slots/diagnostics/servicehealth/read|Get Web Apps Slots Diagnostics Service Health.|
|/Sites/slots/Diagnostics/sitecpuanalysis/Read|Získáte procesoru analýza diagnostiky webu webové aplikace sloty.|
|/Sites/slots/Diagnostics/sitecrashes/Read|Získáte webové aplikace sloty diagnostiky webu dojde k chybě.|
|/sites/slots/diagnostics/sitelatency/read|Získáte webové aplikace sloty diagnostiky lokality latence.|
|/Sites/slots/Diagnostics/sitememoryanalysis/Read|Získáte analýza lokality paměti sloty diagnostiky webové aplikace.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Získáte aktualizace nastavení restartování diagnostiky webové aplikace sloty lokality.|
|/Sites/slots/Diagnostics/siterestartuserinitiated/Read|Získáte webové aplikace sloty diagnostiky lokality restartování inicializované uživatelem.|
|/sites/slots/diagnostics/siteswap/read|Získáte prohození lokality webové aplikace sloty diagnostiky.|
|/sites/slots/diagnostics/threadcount/read|Získáte počet vláken webové aplikace sloty diagnostiky.|
|/sites/slots/diagnostics/workeravailability/read|Získáte webové aplikace sloty diagnostiky Workeravailability.|
|/Sites/slots/Diagnostics/workerprocessrecycle/Read|Získáte recyklace pracovní procesu sloty diagnostiky webové aplikace.|
|/Sites/slots/domainownershipidentifiers/Read|Získání webové aplikace sloty domény vlastnictví identifikátorů.|
|/sites/slots/hostnamebindings/delete|Odstraňte vazby názvů hostitelů sloty webové aplikace.|
|/sites/slots/hostnamebindings/read|Získáte vazby názvů hostitelů sloty webové aplikace.|
|/sites/slots/hostnamebindings/write|Aktualizujte vazby názvů hostitelů sloty webové aplikace.|
|/sites/slots/hybridconnection/delete|Odstranění webové aplikace sloty hybridní připojení.|
|/sites/slots/hybridconnection/read|Získáte webové aplikace sloty hybridní připojení.|
|/sites/slots/hybridconnection/write|Aktualizace webové aplikace sloty hybridní připojení.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Odstranění webové aplikace sloty hybridní připojení předávací službu obory názvů.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Aktualizace webové aplikace sloty hybridní připojení předávací službu obory názvů.|
|/sites/slots/hybridconnectionrelays/read|Získáte předávací službu webové aplikace sloty hybridní připojení.|
|/sites/slots/instances/deployments/read|Získá webové aplikace sloty instancí nasazení.|
|/sites/slots/instances/processes/delete|Odstranění webové aplikace sloty instancí procesy.|
|/sites/slots/instances/processes/read|Získáte webové aplikace sloty instancí procesy.|
|/sites/slots/instances/read|Získá instance sloty webové aplikace.|
|/Sites/slots/metricdefinitions/Read|Získáte definice metrik sloty webové aplikace.|
|/Sites/slots/Metrics/Read|Získáte metriky sloty webové aplikace.|
|/sites/slots/migratemysql/read|Získat webové aplikace sloty migraci databáze MySql.|
|/Sites/slots/networktrace/Action|Sloty síťové trasování webové aplikace.|
|/Sites/slots/newpassword/Action|Sloty nové_heslo webové aplikace.|
|/Sites/slots/operationresults/Read|Získáte výsledky operace sloty webové aplikace.|
|/Sites/slots/Operations/Read|Získáte operace pro sloty webové aplikace.|
|/Sites/slots/perfcounters/Read|Získáte čítače výkonu sloty webové aplikace.|
|/sites/slots/phplogging/read|Získáte Phplogging sloty webové aplikace.|
|/sites/slots/premieraddons/delete|Odstranění webové aplikace sloty Premier rozšíření.|
|/sites/slots/premieraddons/read|Získáte webové aplikace sloty Premier rozšíření.|
|/sites/slots/premieraddons/write|Aktualizace webové aplikace sloty Premier doplňků.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Získá nastavení diagnostiky pro tento prostředek.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Vytvoří nebo aktualizuje nastavení diagnostiky pro tento prostředek.|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Načte dostupné metriky pro Slot webové aplikace|
|/Sites/slots/publiccertificates/Read|Získání certifikátů veřejných sloty webové aplikace.|
|/Sites/slots/publiccertificates/Write|Vytvořit nebo aktualizovat veřejné certifikáty sloty webové aplikace.|
|/Sites/slots/Publish/Action|Publikování Slot webové aplikace|
|/sites/slots/publishxml/Action|Získat publikování profil xml pro Slot webové aplikace|
|/Sites/slots/Read|Umožňuje získat vlastnosti nasazovací slot webové aplikace|
|/sites/slots/resetSlotConfig/Action|Resetovat konfiguraci slot webové aplikace|
|/Sites/slots/resourcehealthmetadata/Read|Získáte Metadata stavu Web Apps sloty prostředků.|
|/Sites/slots/restart/Action|Restartujte Slot webové aplikace|
|/sites/slots/restore/read|Získáte obnovení sloty webové aplikace.|
|/Sites/slots/Restore/Write|Obnovte sloty webové aplikace.|
|/Sites/slots/siteextensions/DELETE|Odstraňte rozšíření lokality sloty webové aplikace.|
|/Sites/slots/siteextensions/Read|Získáte rozšíření lokality sloty webové aplikace.|
|/Sites/slots/siteextensions/Write|Aktualizujte rozšíření lokality sloty webové aplikace.|
|/Sites/slots/slotsdiffs/Action|Získat rozdíly v konfiguraci mezi sloty a webové aplikace|
|/sites/slots/slotsswap/Action|Prohodit sloty nasazení webové aplikace|
|/Sites/slots/snapshots/Read|Získáte snímky sloty webové aplikace.|
|/Sites/slots/sourcecontrols/DELETE|Odstranění nastavení konfigurace správy zdrojů Slot webové aplikace|
|/Sites/slots/sourcecontrols/Read|Získat nastavení konfigurace Slot webové aplikace zdrojového kódu|
|/Sites/slots/sourcecontrols/Write|Aktualizovat nastavení konfigurace správy zdrojů Slot webové aplikace|
|/Sites/slots/Start/Action|Spustit Slot webové aplikace|
|/sites/slots/stop/Action|Zastavit Slot webové aplikace|
|/Sites/slots/Sync/Action|Sloty webové aplikace synchronizace.|
|/sites/slots/triggeredwebjobs/delete|Odstraňte spouštěná WebJobs sloty webové aplikace.|
|/sites/slots/triggeredwebjobs/read|Získáte spouštěná WebJobs sloty webové aplikace.|
|/sites/slots/triggeredwebjobs/run/action|Sloty spuštění webové aplikace aktivované webové úlohy.|
|/Sites/slots/usages/Read|Získáte použití sloty webové aplikace.|
|/sites/slots/virtualnetworkconnections/delete|Odstraňte připojení virtuální sítě sloty webové aplikace.|
|/Sites/slots/virtualnetworkconnections/gateways/Write|Aktualizace webové aplikace sloty virtuální síť připojení brány.|
|/Sites/slots/virtualnetworkconnections/Read|Získání připojení virtuální sítě sloty webové aplikace.|
|/Sites/slots/virtualnetworkconnections/Write|Aktualizujte připojení virtuální sítě sloty webové aplikace.|
|/Sites/slots/webjobs/Read|Načtení úloh Webjob sloty webové aplikace.|
|/Sites/slots/Write|Vytvořit nový Slot webové aplikace nebo aktualizovat stávající|
|/Sites/slotsdiffs/Action|Získat rozdíly v konfiguraci mezi sloty a webové aplikace|
|/sites/slotsswap/Action|Prohodit sloty nasazení webové aplikace|
|/Sites/snapshots/Read|Get Web Apps Snapshots.|
|/Sites/sourcecontrols/DELETE|Odstranění nastavení konfigurace správy zdrojů webové aplikace|
|/Sites/sourcecontrols/Read|Získat nastavení konfigurace zdrojového kódu webové aplikace|
|/Sites/sourcecontrols/Write|Aktualizovat nastavení konfigurace správy zdrojů webové aplikace|
|/Sites/Start/Action|Spustí webovou aplikaci|
|/sites/stop/Action|Zastavení webové aplikace|
|/sites/sync/action|Sync Web Apps.|
|/Sites/syncfunctiontriggers/Action|Synchronizace aktivačních událostí funkce pro webové aplikace.|
|/sites/triggeredwebjobs/delete|Odstraňte spouštěná WebJobs webové aplikace.|
|/sites/triggeredwebjobs/history/read|Získáte webová aplikace aktivované webové úlohy historie.|
|/sites/triggeredwebjobs/read|Získáte spouštěná WebJobs webové aplikace.|
|/sites/triggeredwebjobs/run/action|Spuštění webové aplikace aktivované webové úlohy.|
|/Sites/usages/Read|Získáte použití webové aplikace.|
|/Sites/virtualnetworkconnections/DELETE|Odstraňte připojení virtuální sítě webové aplikace.|
|/Sites/virtualnetworkconnections/gateways/Read|Získáte brány připojení virtuální sítě webové aplikace.|
|/Sites/virtualnetworkconnections/gateways/Write|Aktualizace brány připojení virtuální sítě webové aplikace.|
|/Sites/virtualnetworkconnections/Read|Získání připojení virtuální sítě webové aplikace.|
|/Sites/virtualnetworkconnections/Write|Aktualizujte připojení virtuální sítě webové aplikace.|
|/Sites/webjobs/Read|Načtení úloh Webjob webové aplikace.|
|/ weby/zápisu|Vytvořit novou webovou aplikaci nebo aktualizovat stávající|
|/skus/Read|Získáte SKU.|
|/sourcecontrols/Read|Získáte ovládací prvky zdroje.|
|/ sourcecontrols/zápisu|Aktualizace ovládacích prvků zdroje.|
|nebo zrušit registraci nebo akce|Zrušte registraci poskytovatele prostředků Microsoft.Web pro předplatné.|
|/ ověření nebo akce|Ověření.|
|/verifyhostingenvironmentvnet/action|Ověřte, hostování prostředí virtuální sítě.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Operace | Popis |
|---|---|
|/Components/Read|Prostředky operace čtení|
|/healthInstances/read|Prostředky operace čtení|
|/ Operací/čtení|Prostředky operace čtení|
|/workloads/DELETE|Odstraní prostředek pracovního vytížení|
|/workloads/Read|Načte prostředek pracovního vytížení|
|/ úlohy/zápisu|Zapíše zatížení prostředků|

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit vlastní roli](role-based-access-control-custom-roles.md).
- Zkontrolujte [integrovaným rolím RBAC](role-based-access-built-in-roles.md).
- Zjistěte, jak spravovat přístup k přiřazení [uživatelem](role-based-access-control-manage-assignments.md) nebo [prostředkem](role-based-access-control-configure.md) 
- Zjistěte, jak [zobrazit protokoly aktivity akce u prostředků](~/articles/azure-resource-manager/resource-group-audit.md)
