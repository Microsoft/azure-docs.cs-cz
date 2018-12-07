---
title: Autorizace – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: ddf40538fc3d6e39fe48ff49311f86314008b4ce
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994762"
---
# <a name="security-frame-authorization--mitigations"></a>Zabezpečení Frame: Povolení | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Hranice vztahů důvěryhodnosti počítače** | <ul><li>[Správné seznamy ACL jsou nakonfigurujte tak, aby omezení neoprávněnému přístupu k datům na zařízení](#acl-restricted-access)</li><li>[Ujistěte se, že obsahu citlivým aplikace specifické pro uživatele je uložená v adresáři profilu uživatele](#sensitive-directory)</li><li>[Ujistěte se, že nasazené aplikace pracují s nejnižšími oprávněními](#deployed-privileges)</li></ul> |
| **Webové aplikace** | <ul><li>[Vynutit sekvenční krok pořadí při zpracování obchodní logických toků](#sequential-logic)</li><li>[Implementovat mechanismus, aby se zabránilo výčet tady](#rate-enumeration)</li><li>[Zajistěte, aby se ověření na správné místo a potom Princip nejnižších oprávnění](#principle-least-privilege)</li><li>[Obchodní logiky a prostředků přístupu autorizační rozhodnutí by se neměl zakládat na příchozí žádosti o parametrech](#logic-request-parameters)</li><li>[Zkontrolujte, že obsah a prostředky nejsou vyčíslitelné nebo zpřístupněno vynuceného procházení](#enumerable-browsing)</li></ul> |
| **Database** | <ul><li>[Ujistěte se, že účty nejnižšími slouží k připojení k databázovému serveru](#privileged-server)</li><li>[Implementace řádek úroveň zabezpečení RLS zabránit klientům přístup k datům druhé strany](#rls-tenants)</li><li>[Sysadmin role by měl mít jenom nezbytné platní uživatelé](#sysadmin-users)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Připojení k bráně cloudu pomocí tokenů nejnižší úrovní oprávnění](#cloud-least-privileged)</li></ul> |
| **Centrum událostí Azure** | <ul><li>[Použít oprávnění jen pro odeslání klíč SAS pro generování tokenů zařízení](#sendonly-sas)</li><li>[Nepoužívejte přístupové tokeny, které poskytuje přímý přístup do centra událostí](#access-tokens-hub)</li><li>[Připojení k Centru událostí pomocí klíče SAS, které mají minimální sadu oprávnění potřebných](#sas-minimum-permissions)</li></ul> |
| **Azure Documentdb** | <ul><li>[Použít tokeny prostředků pro připojení ke službě Azure Cosmos DB kdykoli je to možné](#resource-docdb)</li></ul> |
| **Hranice vztahů důvěryhodnosti Azure** | <ul><li>[Povolení správy velice přesně kontrolovat přístup k předplatnému Azure pomocí RBAC](#grained-rbac)</li></ul> |
| **Hranice vztahů důvěryhodnosti Service Fabric** | <ul><li>[Omezit přístup klienta k operace clusteru pomocí RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Provádět modelování zabezpečení a použití zabezpečení na úrovni pole vyžadováno](#modeling-field)</li></ul> |
| **Portálu Dynamics CRM** | <ul><li>[Provádět modelování zabezpečení portálu účtů, dodržujte při tom, která se liší od zbývající části CRM model zabezpečení pro portál](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Jemně odstupňovaná oprávnění na mnoha různých entit ve službě Azure Table Storage](#permission-entities)</li><li>[Povolit na základě rolí řízení přístupu (RBAC) do účtu Azure storage pomocí Azure Resource Manageru](#rbac-azure-manager)</li></ul> |
| **Mobilního klienta** | <ul><li>[Implementace implicitní jailbreaků nebo kořenová detekce](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Slabé tříd ve WCF](#weak-class-wcf)</li><li>[Ovládací prvek WCF implementovat autorizaci](#wcf-authz)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Implementovat mechanismus správnou autorizaci v rozhraní ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Zařízení IoT** | <ul><li>[Pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění provádět kontroly autorizace v zařízení](#device-permission)</li></ul> |
| **Brána IoT pole** | <ul><li>[Pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění provádět kontroly autorizace v hraniční brána](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Správné seznamy ACL jsou nakonfigurujte tak, aby omezení neoprávněnému přístupu k datům na zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Správné seznamy ACL jsou nakonfigurujte tak, aby omezení neoprávněnému přístupu k datům na zařízení|

## <a id="sensitive-directory"></a>Ujistěte se, že obsahu citlivým aplikace specifické pro uživatele je uložená v adresáři profilu uživatele

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že obsahu citlivým aplikace specifické pro uživatele je uložená v adresáři profilu uživatele. To je více uživatelů na počítači zabránit v přístupu k datům uživatele toho druhého.|

## <a id="deployed-privileges"></a>Ujistěte se, že nasazené aplikace pracují s nejnižšími oprávněními

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že nasazené aplikace se spustí s nejnižšími oprávněními. |

## <a id="sequential-logic"></a>Vynutit sekvenční krok pořadí při zpracování obchodní logických toků

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ověřte, že byla tato fáze spustit originální uživatelem chcete vynutit aplikace pouze procesu obchodní logiky toky v pořadí sekvenčního krok s všechny kroky, které jsou zpracovávány v reálné čase lidské a ne zpracovávat mimo pořadí, vynecháno kroky , zpracování kroky z jiného uživatele, nebo příliš rychlé odeslané transakce.|

## <a id="rate-enumeration"></a>Implementovat mechanismus, aby se zabránilo výčet tady

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zajistěte, aby byly citlivých identifikátory náhodné. Implementaci ovládacího prvku test CAPTCHA. na stránkách anonymní. Ujistěte se, že chyby a výjimky by neměly zobrazit konkrétní data|

## <a id="principle-least-privilege"></a>Zajistěte, aby se ověření na správné místo a potom Princip nejnižších oprávnění

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Princip znamená, že uživatelský účet poskytuje pouze oprávnění, které jsou nezbytné, aby uživatelé pracovat. Například zálohování uživatele není potřeba instalovat software: proto zálohování uživatel má oprávnění pouze ke spouštění záloh a spojených s aplikací. Další oprávnění, jako je například instalace nového softwaru, jsou zablokované. Princip platí také pro osobní počítač uživatele, který obvykle funguje v jako běžný uživatelský účet a otevře privilegovaného, chráněné heslem účtu (to znamená, superuživatel) pouze při situace naprosto vyžaduje. </p><p>Tento princip můžete použít také pro vaše webové aplikace. Místo výhradně v závislosti na metody ověřování na základě role pomocí relací, místo toho chcete přiřadit oprávnění pro uživatele pomocí ověřování na základě databáze systému. Aby bylo možné určit, jestli byl uživatel přihlášen správně, pouze teď namísto přiřazení tento uživatel má určitou roli, kterou jsme mu přiřadit s oprávněními k ověření akce, které mu je privilegovaného provádět v systému stále používáme relace. Také aplikace pro velké objemy této metody je vždy, když uživatel má být přiřazena méně oprávnění, které vaše změny se použijí v reálném čase, protože přiřazení není závislý na relaci, který jinak museli nejdřív vyprší.</p>|

## <a id="logic-request-parameters"></a>Obchodní logiky a prostředků přístupu autorizační rozhodnutí by se neměl zakládat na příchozí žádosti o parametrech

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokaždé, když se kontroluje, zda je uživatel s omezeným přístupem ke kontrole určitých údajů, omezení přístupu by měla být zpracování na straně serveru. ID uživatele by měly být uložené v proměnné relace při přihlášení a slouží k načtení dat uživatele z databáze |

### <a name="example"></a>Příklad:
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nyní možné útočník nelze manipulovat a změňte operace aplikace, protože identifikátor pro načítání dat je zpracování na straně serveru.

## <a id="enumerable-browsing"></a>Zkontrolujte, že obsah a prostředky nejsou vyčíslitelné nebo zpřístupněno vynuceného procházení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Citlivé statické a konfiguračních souborů by neměl zachovány v kořenový adresář webové. Obsah není musí být veřejné, bude použito buď správný přístup k ovládacím prvkům nebo odstranění samotného obsahu.</p><p>Navíc vynuceného procházení je obvykle kombinovat s techniky útoku hrubou silou ke shromažďování informací pokusem o přístup k adresám URL tolik nejdříve k vytvoření výčtu adresářů a souborů na serveru. Útočníci mohou vyhledat všechny varianty běžně existující soubory. Hledání souborů heslo by zahrnovat například soubory včetně psswd.txt, password.htm, password.dat a další varianty konfigurací.</p><p>Chcete-li tento problém zmírnit, možnosti pro zjišťování o útoky hrubou silou by měly být zahrnuty.</p>|

## <a id="privileged-server"></a>Ujistěte se, že účty nejnižšími slouží k připojení k databázovému serveru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [SQL Database oprávnění hierarchie](https://msdn.microsoft.com/library/ms191465), [zabezpečitelné prostředky databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Účty nejnižšími by měla sloužit k připojení k databázi. Přihlášení aplikace by měly být omezené databáze a by se měl spustit pouze vybrané uložené procedury. Přihlášení aplikace by měla mít žádný přímý přístup k tabulce. |

## <a id="rls-tenants"></a>Implementace řádek úroveň zabezpečení RLS zabránit klientům přístup k datům druhé strany

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Sql Azure, OnPrem |
| **Atributy**              | MsSQL2016 verze - V12, verze SQL - SQL |
| **Odkazy**              | [SQL Server – zabezpečení na úrovni řádků (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroky** | <p>Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění).</p><p>Zabezpečení na úrovni řádků (RLS) zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pro zajištění, že pracovníci mají přístup pouze k datovým řádkům, které se vztahují k jejich oddělení, nebo pro omezení přístupu zákazníků pouze k datům souvisejícím s jejich společností.</p><p>Logiky přístupu k omezení je umístěn v databázové vrstvě spíše než pryč z dat v jiné aplikační vrstvy. Databázový systém platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Díky tomu systém zabezpečení spolehlivější a robustní snížením útoku na systém zabezpečení.</p><p>|

Všimněte si že RLS jako funkce databáze out-of-the-box se vztahuje pouze na serveru SQL Server spouští 2016 a Azure SQL database. Pokud není implementovaná funkce zabezpečení na úrovni řádků out-of-the-box, je třeba zajistit, že je přístup k datům omezené použití zobrazení a postupy

## <a id="sysadmin-users"></a>Sysadmin role by měl mít jenom nezbytné platní uživatelé

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [SQL Database oprávnění hierarchie](https://msdn.microsoft.com/library/ms191465), [zabezpečitelné prostředky databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Členové role pevného serveru správce sysadmin systému by měla být velmi omezená a účty používané aplikacemi, nikdy neobsahuje.  Zkontrolujte seznam uživatelů v roli a odeberte všechny nepotřebné účty|

## <a id="cloud-least-privileged"></a>Připojení k bráně cloudu pomocí tokenů nejnižší úrovní oprávnění

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Výběr brány - Azure IoT Hub |
| **Odkazy**              | [Řízení přístupu služby IOT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroky** | Zadejte alespoň oprávnění pro různé součásti, které se připojují k cloudu brány (služby IoT Hub). Typickým příkladem je – součást zřizování nebo správy zařízení používá registryread/zápis, procesor událostí (ASA) používá služba připojit. Jednotlivá zařízení připojit pomocí přihlašovacích údajů zařízení|

## <a id="sendonly-sas"></a>Použít oprávnění jen pro odeslání klíč SAS pro generování tokenů zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Klíč SAS se používá ke generování tokenů pro jednotlivá zařízení. Při generování token zařízení pro daného vydavatele používat klíč SAS pro oprávnění jen pro odesílání|

## <a id="access-tokens-hub"></a>Nepoužívejte přístupové tokeny, které poskytuje přímý přístup do centra událostí

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Zařízení by se neměla poskytovat token, který umožňuje přímý přístup do centra událostí. Použití nejnižším oprávněním token pro zařízení, která poskytuje přístup pouze pro vydavatele by vám pomůže identifikovat a seznam zakázaných Pokud vyhodnocený jako podvodný nebo dojde k ohrožení bezpečnosti zařízení.|

## <a id="sas-minimum-permissions"></a>Připojení k Centru událostí pomocí klíče SAS, které mají minimální sadu oprávnění potřebných

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Poskytuje nejnižší oprávnění na různé back endovým aplikacím, které se připojují k Centru událostí. Generovat různé klíče SAS pro každý back endová aplikace a poskytují pouze požadovaná oprávnění – odeslání, přijetí nebo spravovat pro ně.|

## <a id="resource-docdb"></a>Použít tokeny prostředků pro připojení ke službě Cosmos DB, kdykoli je to možné

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Token prostředku je přidružený prostředek služby Azure Cosmos DB oprávnění a zachytí vztah mezi uživatelem databáze a oprávnění má uživatel pro určitý prostředek aplikace služby Azure Cosmos DB (např. kolekce a dokumentu). Pro přístup k rozhraní Azure Cosmos DB, pokud klient nemůže být za důvěryhodné možností nakládání s klíči hlavní nebo jen pro čtení – například koncový uživatel aplikace, jako je mobilních nebo desktopových klientských vždy pomocí tokenu prostředku. Hlavní klíč nebo klíče jen pro čtení z back-endové aplikace, které můžete bezpečně uložit tyto klíče použít.|

## <a id="grained-rbac"></a>Povolení správy velice přesně kontrolovat přístup k předplatnému Azure pomocí RBAC

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Azure | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroky** | Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh.|

## <a id="cluster-rbac"></a>Omezit přístup klienta k operace clusteru pomocí RBAC

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Řízení přístupu na základě rolí pro klienty Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroky** | <p>Azure Service Fabric podporuje dva typy ovládacích prvků jiný přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.</p><p>Správci mají plný přístup k funkcím správy (včetně možností pro čtení a zápis). Uživatelé, ve výchozím nastavení, mají pouze přístup pro čtení k funkce pro správu (například schopnosti příkazů jazyka) a schopnost řešení aplikací a služeb.</p><p>Dva klientské role (správce a klient) je zadat v době vytváření clusteru poskytnutím samostatné certifikáty pro každý.</p>|

## <a id="modeling-field"></a>Provádět modelování zabezpečení a použití zabezpečení na úrovni pole vyžadováno

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Provádět modelování zabezpečení a použití zabezpečení na úrovni pole vyžadováno|

## <a id="portal-security"></a>Provádět modelování zabezpečení portálu účtů, dodržujte při tom, která se liší od zbývající části CRM model zabezpečení pro portál

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Portálu Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Provádět modelování zabezpečení portálu účtů, dodržujte při tom, která se liší od zbývající části CRM model zabezpečení pro portál|

## <a id="permission-entities"></a>Jemně odstupňovaná oprávnění na mnoha různých entit ve službě Azure Table Storage

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | StorageType – tabulky |
| **Odkazy**              | [Jak delegovat přístup k objektům v účtu služby Azure storage pomocí SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroky** | V některých obchodních scénářích Azure Table Storage může být nutné ukládat citlivá data, která určeného pro různé strany. Například citlivá data týkající se různých zemí. V takových případech lze SAS podpisy sestavit, zadáním klíče oblastí oddílu a řádku tak, že uživatel má přístup k datům, které jsou specifické pro konkrétní zemi.| 

## <a id="rbac-azure-manager"></a>Povolit na základě rolí řízení přístupu (RBAC) do účtu Azure storage pomocí Azure Resource Manageru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Jak zabezpečit svůj účet úložiště pomocí řízení přístupu na základě Role (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroky** | <p>Když vytvoříte nový účet úložiště, vyberte model nasazení Classic nebo Azure Resource Manageru. Model Classic vytváření prostředků v Azure umožňuje pouze rigidní přístup k předplatnému a v důsledku, účet úložiště.</p><p>S modelem Azure Resource Manageru kam si ukládáte účtu úložiště v prostředku skupiny a řízení přístupu k rovině správy tohoto účtu konkrétní úložiště pomocí Azure Active Directory. Například můžete konkrétním uživatelům udělit přístup klíče účtu úložiště, zatímco ostatní uživatelé mohou zobrazit informace o účtu úložiště, ale nelze přístupové klíče účtu úložiště.</p>|

## <a id="rooting-detection"></a>Implementace implicitní jailbreaků nebo kořenová detekce

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Aplikace by měla chránit svou vlastní konfiguraci a uživatelská data v případě, pokud telefonní root nebo jailbreak. Kořenová/jailbreak zásadní znamená neoprávněnému přístupu, které běžným uživatelům nebude provádět na jejich telefonech. Proto aplikace by měla mít implicitní detekční logiku při spuštění aplikace ke zjištění, jestli má root telefonu.</p><p>Logika zjišťování můžete jednoduše přistupovat k soubory, které obvykle jenom kořenový uživatel získat přístup, například:</p><ul><li>/system/app/Superuser.apk</li><li>/ sbin/su</li><li>/System/Bin/su</li><li>/System/xbin/su</li><li>/data/Local/xbin/su</li><li>/data/local/bin/su</li><li>/System/SD/xbin/su</li><li>/System/Bin/failsafe/su</li><li>/data/Local/su</li></ul><p>Pokud aplikace můžete použít žádnou z těchto souborů, označuje, že je aplikace spuštěna jako uživatel root.</p>|

## <a id="weak-class-wcf"></a>Slabé tříd ve WCF

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Systém používá odkaz na slabé třídu, která může útočníkovi umožnit spustit neoprávněný kód. Program odkazuje na třídu definovaný uživatelem, která není jednoznačně identifikovat. Po načtení této slabě zjištěné třídy rozhraní .NET vyhledá zavaděč typ CLR pro třídu v následujících umístěních v uvedeném pořadí:</p><ol><li>Pokud je sestavení typu je známé, zavaděč prohledá konfiguračního souboru přesměrování umístění mezipaměti GAC, aktuální sestavení s využitím informací o konfiguraci a základní adresář aplikace</li><li>Pokud sestavení není znám, prohledá zavaděč aktuálního sestavení, mscorlib a umístění vrácený TypeResolve obslužné rutiny události</li><li>Toto pořadí hledání CLR je možné upravit s háky například mechanismus předávání typu a AppDomain.TypeResolve událostí</li></ol><p>Pokud útočník zneužije pořadí hledání CLR vytvořením alternativních třídy se stejným názvem a že ho umístíte do alternativního umístění, že modul CLR bude nejdřív načíst modul CLR bude náhodně spouštění kódu vytvořeného poskytované útočník</p>|

### <a name="example"></a>Příklad:
`<behaviorExtensions/>` Element konfigurační soubor WCF níže dá pokyn WCF pro přidání vlastního chování třídy konkrétní rozšíření WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Pomocí plně kvalifikované názvy (silnou) jednoznačně identifikuje typ a ještě zvyšuje zabezpečení vašeho systému. Používejte plně kvalifikované názvy sestavení registrace typů v souboru machine.config a app.config.

### <a name="example"></a>Příklad:
`<behaviorExtensions/>` Element konfigurační soubor WCF níže dá pokyn WCF přidejte třídu silného odkazuje vlastní chování pro konkrétní rozšíření WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>Ovládací prvek WCF implementovat autorizaci

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Tato služba nepoužívá ovládacím prvku autorizace. Když klient volá konkrétní službu WCF, poskytuje WCF různých režimů ověřování, které ověřují, že volající nemá oprávnění k provedení metody služby na serveru. Pokud prvků pro ověřování nejsou povoleny pro služby WCF, ověřený uživatel může dosáhnout zvýšení úrovně oprávnění.</p>|

### <a name="example"></a>Příklad:
Následující konfigurace dává pokyn WCF není zkontrolovat úroveň autorizace klienta při spouštění služby:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Ověřte, že je k tomu oprávnění volající metody služby pomocí schéma autorizace služby. WCF nabízí dva režimy a umožňuje definovat schéma autorizace. Režim UseWindowsGroups používá role Windows a uživatelů a režim UseAspNetRoles použije k ověření poskytovatele rolí prostředí ASP.NET, jako je SQL Server.

### <a name="example"></a>Příklad:
Následující konfigurace dá pokyn, WCF, abyste měli jistotu, že klient je součástí skupiny správců před spuštěním služby přidat:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Služba je pak deklarován jako následující:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementovat mechanismus správnou autorizaci v rozhraní ASP.NET Web API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, MVC5 |
| **Atributy**              | Není k dispozici, zprostředkovatele Identity poskytovatele – služby AD FS, Identity – Azure AD |
| **Odkazy**              | [Ověřování a autorizace v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroky** | <p>Informace o rolích pro uživatele aplikace může být odvozena ze služby Azure AD nebo AD FS deklarací identity, pokud aplikace spoléhá na ně jako zprostředkovatele Identity nebo může samotná aplikace za předpokladu, že. Ve všech těchto případech by měla implementace vlastní autorizace ověření informací o roli uživatele.</p><p>Informace o rolích pro uživatele aplikace může být odvozena ze služby Azure AD nebo AD FS deklarací identity, pokud aplikace spoléhá na ně jako zprostředkovatele Identity nebo může samotná aplikace za předpokladu, že. Ve všech těchto případech by měla implementace vlastní autorizace ověření informací o roli uživatele.</p>

### <a name="example"></a>Příklad:
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Všechny řadiče a metody akce, které se musí chránit by měl být doplněny pomocí výše atribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění provádět kontroly autorizace v zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Zařízení by měl Povolit volajícímu zkontrolujte, jestli má volající požadovaná oprávnění k provedení požadované akce. Umožňuje například Dejme tomu, že zařízení je Smart Lock dveří, které je možné monitorovat z cloudu a navíc poskytuje funkce, jako například vzdálené uzamknutí dveří.</p><p>Smart Lock dveře poskytuje odemykání funkce jenom v případě, že někdo fyzicky přinášejí téměř dvířka na kartě. V tomto případě má počítat tak, že neposkytuje všechny funkce k odemknutí dveří jako cloudová brána nemá oprávnění k odesílání příkazu k odemknutí dveří provádění vzdálené příkazy a ovládání.</p>|

## <a id="field-permission"></a>Pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění provádět kontroly autorizace v hraniční brána

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT pole | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Hraniční brána by měl Povolit volajícímu zkontrolujte, jestli má volající požadovaná oprávnění k provedení požadované akce. Například by měla být jiná oprávnění správce uživatelského rozhraní/rozhraní API používá ke konfiguraci zařízení brány v/s pole, které k němu připojit.|
