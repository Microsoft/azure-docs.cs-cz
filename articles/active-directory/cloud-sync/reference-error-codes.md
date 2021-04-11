---
title: Azure AD Connect kódy a popisy chyb synchronizace cloudu
description: referenční článek pro kódy chyb synchronizace cloudu
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0aeb2e9649a4bbbb2520eac683836ebbf7dfd0a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075045"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect kódy a popisy chyb synchronizace cloudu
Následuje seznam kódů chyb a jejich popis.


## <a name="error-codes"></a>Kódy chyb

|Kód chyby|Podrobnosti|Scenario|Řešení|
|-----|-----|-----|-----|
|Prodlev|Chybová zpráva: při kontaktování místního agenta a synchronizaci konfigurace jsme zjistili chybu časového limitu žádosti. Další problémy související s agentem Cloud Sync najdete v našich pokynech k řešení potíží.|Vypršel časový limit žádosti. Aktuální hodnota časového limitu je 10 minut.|Přečtěte si naše [pokyny k řešení potíží](how-to-troubleshoot.md) .|
|HybridSynchronizationActiveDirectoryInternalServerError|Chybová zpráva: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud se tento problém opakuje, obraťte se prosím na podporu a zadejte následující identifikátor úlohy: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Další podrobnosti: zpracování požadavku HTTP vedlo k výjimce. |Nepovedlo se zpracovat parametry přijaté v žádosti SCIM do žádosti o vyhledávání.|Podrobnosti naleznete v odpovědi HTTP vrácené vlastností Response této výjimky.|
|HybridIdentityServiceNoAgentsAssigned|Chybová zpráva: nemůžeme najít aktivního agenta pro doménu, kterou se pokoušíte synchronizovat. Zkontrolujte prosím, jestli se agenti odebrali. V takovém případě znovu nainstalujte agenta znovu.|Nejsou spuštěni žádní agenti. Pravděpodobně došlo k odebrání agentů. Zaregistrujte nového agenta.|V tomto případě se nezobrazuje žádný agent přiřazený k doméně na portálu.|
|HybridIdentityServiceNoActiveAgents|Chybová zpráva: nemůžeme najít aktivního agenta pro doménu, kterou se pokoušíte synchronizovat. Zkontrolujte prosím, jestli je agent spuštěný, tak, že na server, kde je nainstalovaný agent, a zkontrolujte, jestli je spuštěný Microsoft Azure AD agent synchronizace cloudu v části služby.|"Agenti neposlouchá koncový bod ServiceBus. [Agent je za bránou firewall, která neumožňuje připojení ke službě Service Bus.](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Chybová zpráva: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud se tento problém opakuje, obraťte se prosím na podporu a zadejte následující identifikátor úlohy: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Další podrobnosti: Zdá se, že došlo k potížím s nastavením synchronizace vašeho cloudu. Zaregistrujte prosím agenta Cloud Sync v Prem doméně služby AD a restartujte konfiguraci z webu Azure Portal.|Název prostředku musí být nastaven, aby věděl, který agent má kontaktovat.|Zaregistrujte prosím agenta Cloud Sync v Prem doméně služby AD a restartujte konfiguraci z webu Azure Portal.|
|HybridIdentityServiceAgentSignalingError|Chybová zpráva: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud se tento problém opakuje, obraťte se prosím na podporu a zadejte následující identifikátor úlohy: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9. Další podrobnosti: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace).|Service Bus není možné odeslat zprávu agentovi. Může dojít k výpadku ve službě Service Bus nebo agent nereaguje.|Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace).|
|AzureDirectoryServiceServerBusy|Chybová zpráva: došlo k chybě. Kód chyby: 81. Popis chyby: Azure Active Directory je aktuálně zaneprázdněna. Tato operace bude automaticky opakována. Pokud tento problém trvá déle než 24 hodin, obraťte se na technickou podporu. ID sledování: název serveru 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f:|Azure Active Directory je aktuálně zaneprázdněna.|Pokud tento problém trvá déle než 24 hodin, obraťte se na technickou podporu.|
|AzureActiveDirectoryInvalidCredential|Chybová zpráva: zjistili jsme problém s účtem služby, který se používá ke spuštění Azure AD Connect synchronizace cloudu. Účet cloudové služby můžete opravit podle pokynů uvedených [tady](./how-to-troubleshoot.md). Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace). Další podrobnosti o chybě: CredentialsInvalid AADSTS50034: uživatelský účet {EmailHidden} neexistuje v adresáři skydrive365.onmicrosoft.com. Chcete-li se přihlásit k této aplikaci, je nutné účet přidat do adresáře. ID trasování: ID korelace 14b63033-3bc9-4bd4-b871-5eb4b3500200:57d93ed1-be4d-483c-997c-a3b6f03deb00 časové razítko: 2021-01-12 21:08:29Z |Tato chyba se vyvolá v případě, že účet synchronizační služby ADToAADSyncServiceAccount v tenantovi neexistuje. Může to být způsobeno náhodným odstraněním účtu.|K opravě účtu služby použijte [příkaz Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) .|
|AzureActiveDirectoryExpiredCredentials|Chybová zpráva: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace). Další podrobnosti o chybě: CredentialsExpired AADSTS50055: platnost hesla vypršela. ID trasování: ID korelace 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00:1c69b196-1c3a-4381-9187-c84747807155 časové razítko: 2021-01-12 20:59:31Z | Stavový kód odpovědi neindikuje úspěch: 401 (Neautorizováno).<br> Vypršela platnost přihlašovacích údajů k účtu služby AAD Sync.|Účet cloudové služby můžete opravit podle pokynů uvedených v tématu https://go.microsoft.com/fwlink/?linkid=2150988 . Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace).  Další podrobnosti o chybě: vaše přihlašovací údaje tenanta pro správu Azure Active Directory byly vyměněny pro token OAuth, který uplynul od vypršení platnosti. "|
|AzureActiveDirectoryAuthenticationFailed|Chybová zpráva: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud se tento problém opakuje, obraťte se prosím na podporu a zadejte následující identifikátor úlohy: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac. Další podrobnosti: v tuto chvíli se nám nepovedlo zpracovat tento požadavek. Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace). Další podrobnosti o chybě: UnexpectedError.|Neznámou chybu.|Pokud s tím budou dál problémy, obraťte se prosím na podporu s ID úlohy (z podokna stav vaší konfigurace).|

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
