---
title: Autorizace – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: b9ad3ceeb77a4adc2c47b262aa40a48c14423198
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019513"
---
# <a name="security-frame-authorization--mitigations"></a>Zabezpečení rámce: Autorizace | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Počítač hranice vztahů důvěryhodnosti** | <ul><li>[Ujistěte se, zda jsou nakonfigurovány správné seznamy řízení přístupu k omezení neoprávněného přístupu k datům v zařízení](#acl-restricted-access)</li><li>[Ujistěte se, že je v adresáři profil uživatele uložený obsah aplikace citlivé specifický pro uživatele](#sensitive-directory)</li><li>[Ujistěte se, že nasazené aplikace jsou spouštěny s nejnižšími oprávněními](#deployed-privileges)</li></ul> |
| **Webové aplikace** | <ul><li>[Vynutit sekvenční krok pořadí při zpracování toky obchodní logiky](#sequential-logic)</li><li>[Implementace míru omezení mechanismus, aby se zabránilo – výčet](#rate-enumeration)</li><li>[Zajistěte, aby se místní správné ověření a je následovaný Princip nejnižších oprávnění](#principle-least-privilege)</li><li>[Obchodní logiky a prostředků přístup autorizačních rozhodnutích by neměl být na základě příchozí požadavek parametrů](#logic-request-parameters)</li><li>[Ujistěte se, že obsah a nejsou prostředky výčtové nebo zpřístupněno vynuceného procházení](#enumerable-browsing)</li></ul> |
| **Database** | <ul><li>[Ujistěte se, že nejméně privilegovaných účtů se používají pro připojení k databázi serveru](#privileged-server)</li><li>[Implementace zabezpečení na úrovni řádku RLS zabránit klientům v přístupu k výměně dat](#rls-tenants)</li><li>[Sysadmin role by měla mít pouze platné potřeby uživatelů](#sysadmin-users)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Připojení k bráně cloudu pomocí nejméně privilegovaným tokeny](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Použít jen odesílání oprávnění klíče SAS ke generování tokenů zařízení](#sendonly-sas)</li><li>[Nepoužívejte přístupové tokeny, které poskytuje přímý přístup do centra událostí](#access-tokens-hub)</li><li>[Připojení k Centru událostí pomocí klíče SAS, které mají minimální oprávnění potřebná](#sas-minimum-permissions)</li></ul> |
| **Azure Documentdb** | <ul><li>[Použít prostředků tokeny pro připojení k databázi Azure Cosmos kdykoli je to možné](#resource-docdb)</li></ul> |
| **Hranice vztahů důvěryhodnosti Azure** | <ul><li>[Povolit přesnou správu přístupu pomocí RBAC předplatné Azure](#grained-rbac)</li></ul> |
| **Hranice vztahů důvěryhodnosti Service Fabric** | <ul><li>[Omezení přístupu klienta pro operace clusteru pomocí RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Provedení modelování zabezpečení a použití zabezpečení na úrovni pole případě požadavku](#modeling-field)</li></ul> |
| **Dynamics CRM portálu** | <ul><li>[Provedení modelování zabezpečení portálu účtů dodržíte, který se liší od zbytku CRM model zabezpečení pro portál](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Jemně odstupňovaná oprávnění grant na rozsahu entit v Azure Table Storage](#permission-entities)</li><li>[Povolit na základě rolí řízení přístupu (RBAC) na účet úložiště Azure pomocí Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobilního klienta** | <ul><li>[Implementovat implicitní jailbreaků nebo vytvoření kořenového adresáře detekce](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Slabé odkazu ve WCF](#weak-class-wcf)</li><li>[Řízení autorizace implementace WCF](#wcf-authz)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Implementace mechanismus správné autorizace v rozhraní ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Zařízení IoT** | <ul><li>[Provedení kontroly autorizace v zařízení, pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění](#device-permission)</li></ul> |
| **Brána pole IoT** | <ul><li>[Provedení kontroly autorizace v brána pole, pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Ujistěte se, zda jsou nakonfigurovány správné seznamy řízení přístupu k omezení neoprávněného přístupu k datům v zařízení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, zda jsou nakonfigurovány správné seznamy řízení přístupu k omezení neoprávněného přístupu k datům v zařízení|

## <a id="sensitive-directory"></a>Ujistěte se, že je v adresáři profil uživatele uložený obsah aplikace citlivé specifický pro uživatele

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že je v adresáři profil uživatele uložený obsah aplikace citlivé specifický pro uživatele. Toto je zabránit v přístupu k výměně dat více uživatelů počítače.|

## <a id="deployed-privileges"></a>Ujistěte se, že nasazené aplikace jsou spouštěny s nejnižšími oprávněními

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že nasazené aplikace běží s nejnižšími oprávněními. |

## <a id="sequential-logic"></a>Vynutit sekvenční krok pořadí při zpracování toky obchodní logiky

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Chcete-li ověřte, zda byla tato fáze spustit originální uživatelem chcete vynutit aplikace pouze všechny kroky, které zpracovává realistické lidského včas zpracovat toky obchodní logiku v sekvenčních krok pořadí a není zpracovat mimo pořadí, přeskočen kroky, zpracovaná kroky z jiného uživatele, nebo příliš rychle odeslaná transakcí.|

## <a id="rate-enumeration"></a>Implementace míru omezení mechanismus, aby se zabránilo – výčet

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zajistěte, aby citlivá identifikátory náhodné. Implementovat řízení CAPTCHA na stránkách anonymní. Ujistěte se, že chyby a výjimky by neměl odhalit konkrétní data|

## <a id="principle-least-privilege"></a>Zajistěte, aby se místní správné ověření a je následovaný Princip nejnižších oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Princip znamená poskytnete jenom oprávnění, které jsou nezbytné, aby uživatelé pracují uživatelský účet. Například zálohování uživatele není potřeba instalovat software: proto zálohování uživatel má oprávnění pouze ke spuštění zálohování a spojených s aplikací. Další oprávnění, například při instalaci nového softwaru, jsou zablokované. Princip platí také pro uživatele osobní počítače, který obvykle pracuje v jako běžný uživatelský účet a otevře privilegovaného, chráněný heslem účtu (tj. superuživatel) pouze když absolutně vyžádá situace ho. </p><p>Tuto zásadu lze použít také k webovým aplikacím. Místo výhradně v závislosti na metody ověřování na základě rolí pomocí relace, místo chceme přiřadit oprávnění pro uživatele pomocí ověřování založeného na databázi systému. Chcete-li zjistit, zda byl uživatel přihlášen správně, pouze teď místo přiřazení tento uživatel má určitou roli, kterou jsme mu přiřadit s oprávněními k ověření akce, které mu je privilegovaný provést na serveru stále používáme relací. Také velký pro této metody se vždy, když má uživatel k přiřazení méně oprávnění, změny se použijí za chodu, vzhledem k tomu, že přiřazování nezávisí na relace, který jinak museli nejprve vyprší.</p>|

## <a id="logic-request-parameters"></a>Obchodní logiky a prostředků přístup autorizačních rozhodnutích by neměl být na základě příchozí požadavek parametrů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Vždy, když se kontrola, zda je uživatel s omezeným přístupem ke kontrole určitých údajů, omezení přístupu musí být zpracován straně serveru. ID uživatele by měly být uložené v proměnné relace na přihlášení a slouží k načtení dat uživatele z databáze |

### <a name="example"></a>Příklad:
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nyní nelze útočník možné manipulovat a změnit zpracovává operaci aplikace vzhledem k tomu, že je identifikátor pro načítání dat na straně serveru.

## <a id="enumerable-browsing"></a>Ujistěte se, že obsah a nejsou prostředky výčtové nebo zpřístupněno vynuceného procházení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>V kořenu webové nesmí uchovávat citlivé soubory statické a konfigurace. Pro obsah, nevyžaduje byly veřejné, měla by se použít buď správný přístup k ovládacím prvkům nebo odebrání samotný obsah.</p><p>Navíc vynuceného procházení obvykle spolu s hrubou silou techniky informace shromáždit podle pokusu o přístup k tolik adresy URL jako možné vytvořit výčet adresářů a souborů na serveru. Útočníci mohou vyhledat všechny varianty běžně existující soubory. Hledání souborů a hesla by zahrnovat například soubory včetně psswd.txt, password.htm, password.dat a dalších odlišností.</p><p>Toto riziko lze snížit možnosti pro zjišťování pokusů hrubou silou by měly být zahrnuty.</p>|

## <a id="privileged-server"></a>Ujistěte se, že nejméně privilegovaných účtů se používají pro připojení k databázi serveru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [SQL Database oprávnění hierarchie](https://msdn.microsoft.com/library/ms191465), [zabezpečitelné prostředky databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Nejméně privilegovaným účtům se použít k připojení k databázi. Přihlášení aplikace by měla být omezena v databázi a by měla spustit pouze vybrané uložené procedury. Přihlášení aplikace by měla mít žádný přímý přístup k tabulce. |

## <a id="rls-tenants"></a>Implementace zabezpečení na úrovni řádku RLS zabránit klientům v přístupu k výměně dat

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Sql Azure, OnPrem |
| **Atributy**              | MsSQL2016 verze - V12, verze SQL - SQL |
| **Odkazy**              | [Zabezpečení SQL serveru úrovni řádků (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroky** | <p>Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění).</p><p>Zabezpečení úrovni řádků (RLS) zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pro zajištění, že pracovníci mají přístup pouze k datovým řádkům, které se vztahují k jejich oddělení, nebo pro omezení přístupu zákazníků pouze k datům souvisejícím s jejich společností.</p><p>Logika omezení přístupu je umístěn v databázové vrstvy spíše než tokeny z dat v jiném aplikační vrstvě. Databázový systém platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Díky systému zabezpečení spolehlivější a robustní snížením plochy zabezpečení systému.</p><p>|

Upozorňujeme, že RLS jako databáze se na pole funkce se vztahuje pouze na systém SQL Server od 2016 a Azure SQL database. Pokud není implementovaná funkce RLS se na pole, je třeba zajistit, že je přístup k datům s omezeným přístupem pomocí zobrazení a postupy

## <a id="sysadmin-users"></a>Sysadmin role by měla mít pouze platné potřeby uživatelů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [SQL Database oprávnění hierarchie](https://msdn.microsoft.com/library/ms191465), [zabezpečitelné prostředky databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Členové role SysAdmin pevného serveru by měl být velmi omezená a nikdy nesmí obsahovat účty používané aplikace.  Zkontrolujte seznam uživatelů v roli a odeberte všechny nepotřebné účty|

## <a id="cloud-least-privileged"></a>Připojení k bráně cloudu pomocí nejméně privilegovaným tokeny

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Volba brány - Azure IoT Hub |
| **Odkazy**              | [Řízení přístupu služby IOT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroky** | Zadejte alespoň oprávnění pro různé součásti, které se připojují k Cloudová brána (IoT Hub). Typickým příkladem je – součást zřizování nebo správy zařízení používá registryread a zápis, procesor událostí (ASA) používá služba připojit. Jednotlivých zařízení se připojují přes přihlašovací údaje zařízení|

## <a id="sendonly-sas"></a>Použít jen odesílání oprávnění klíče SAS ke generování tokenů zařízení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Klíč SAS se používá ke generování tokenů jednotlivých zařízení. Pomocí oprávnění jen pro odeslání SAS klíč při generování token zařízení pro daného vydavatele|

## <a id="access-tokens-hub"></a>Nepoužívejte přístupové tokeny, které poskytuje přímý přístup do centra událostí

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Token, který uděluje přímý přístup do centra událostí by se neměla poskytovat do zařízení. Použití token nejnižšími oprávněními pro zařízení, která poskytuje přístup pouze k vydavatel by pomáhají identifikovat a pokud ji blokovaných nalezen podvodný nebo dojde k ohrožení zařízení.|

## <a id="sas-minimum-permissions"></a>Připojení k Centru událostí pomocí klíče SAS, které mají minimální oprávnění potřebná

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Zadejte alespoň oprávnění pro různé aplikace back-end, které se připojují k Centru událostí. Generovat klíče samostatné SAS pro každou aplikaci back-end a pouze poskytují nezbytné oprávnění - odesílání, Receive nebo spravovat na ně.|

## <a id="resource-docdb"></a>Použití prostředků tokeny pro připojení k databázi Cosmos. Pokud je to možné

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Token prostředku je přidružený prostředek Azure Cosmos DB oprávnění a zachytí vztah mezi uživatelem databáze a oprávnění tento uživatel má pro určitý prostředek Azure Cosmos DB aplikace (např. kolekce a dokumentu). Vždy používejte token prostředků pro přístup k databázi Cosmos Azure, pokud klient nemůže být považován za důvěryhodný nakládání s klíči hlavní nebo jen pro čtení - jako koncový uživatel aplikace, jako je mobilní nebo desktopové klienta. Pomocí hlavního klíče nebo klíče jen pro čtení z back-end aplikace, které můžete bezpečně uložit tyto klíče.|

## <a id="grained-rbac"></a>Povolit přesnou správu přístupu pomocí RBAC předplatné Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Azure | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroky** | Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh.|

## <a id="cluster-rbac"></a>Omezení přístupu klienta pro operace clusteru pomocí RBAC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Řízení přístupu na základě rolí pro klienty Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroky** | <p>Azure Service Fabric podporuje dva typy ovládacích prvků různý přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.</p><p>Správci mají plný přístup k funkcím správy (včetně možnosti pro čtení i zápis). Uživatelé, ve výchozím nastavení, mají pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby.</p><p>Tím, že poskytuje samostatné certifikáty pro každou zadáte dva klientské role (správce a klient) v době vytváření clusteru.</p>|

## <a id="modeling-field"></a>Provedení modelování zabezpečení a použití zabezpečení na úrovni pole případě požadavku

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Provedení modelování zabezpečení a použití zabezpečení na úrovni pole případě požadavku|

## <a id="portal-security"></a>Provedení modelování zabezpečení portálu účtů dodržíte, který se liší od zbytku CRM model zabezpečení pro portál

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM portálu | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Provedení modelování zabezpečení portálu účtů dodržíte, který se liší od zbytku CRM model zabezpečení pro portál|

## <a id="permission-entities"></a>Jemně odstupňovaná oprávnění grant na rozsahu entit v Azure Table Storage

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | StorageType – tabulka |
| **Odkazy**              | [Postup delegovat přístup k objektům v účtu úložiště Azure, pomocí SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroky** | V některých scénářích firmy může být nutné Azure Table Storage ukládat citlivá data, který je určen jiný stranám. Například citlivá data, která se týkají různých zemí. V takových případech SAS podpisy jde konstruovat zadáním klíče rozsahy oddílu a řádku, tak, že uživatel může přistupovat k data specifická pro konkrétní země.| 

## <a id="rbac-azure-manager"></a>Povolit na základě rolí řízení přístupu (RBAC) na účet úložiště Azure pomocí Azure Resource Manager

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Postupy pro zabezpečení vašeho účtu úložiště pomocí řízení přístupu na základě Role (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroky** | <p>Když vytvoříte nový účet úložiště, můžete vybrat model nasazení Classic nebo Azure Resource Manager. Klasický model vytváření prostředků v Azure umožňuje pouze vše nebo nic přístup k předplatnému a naopak účet úložiště.</p><p>S modelem Azure Resource Manager vložíte účet úložiště v prostředku skupiny a řízení přístupu pro správu roviny tohoto účtu konkrétní úložiště pomocí služby Azure Active Directory. Například můžete uživatelům konkrétní přístup klíče účtu úložiště jiných uživatelů můžete zobrazit informace o účtu úložiště, ale nemůže přístupové klíče účtu úložiště.</p>|

## <a id="rooting-detection"></a>Implementovat implicitní jailbreaků nebo vytvoření kořenového adresáře detekce

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Aplikace by měla chránit svou vlastní konfiguraci a uživatelská data v případě, pokud je telefonní root nebo jailbreak. Vytvoření kořenového adresáře nebo jailbreak nejnovější znamená neoprávněným přístupem, které běžným uživatelům nebude provádět na svých vlastních telefonech. Proto aplikace by měla mít logika pro detekci implicitní při spuštění aplikace, ke zjištění, jestli má root telefonu.</p><p>Logika pro detekci můžete jednoduše přistupovat k soubory, které obvykle jenom kořenový uživatel získat přístup, například:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/System/Bin/su</li><li>/system/xbin/su</li><li>/data/Local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/Local/su</li></ul><p>Pokud aplikace mohou přistupovat k některým z těchto souborů, označuje, že je aplikace spuštěna jako kořenové uživatele.</p>|

## <a id="weak-class-wcf"></a>Slabé odkazu ve WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | <p>Slabé třída odkaz, který může útočníkovi umožnit spouštění kódu vytvořeného neoprávněným používá systém. Program odkazuje uživatelsky definované třídy, která není jednoznačně identifikovat. Když .NET načte tato slabě identifikovaných třída, načítání typu CLR vyhledá – třída v následujících umístěních v uvedeném pořadí:</p><ol><li>Pokud je znám sestavení typu zavaděč prohledá umístění konfiguračního souboru přesměrování, GAC, aktuální sestavení pomocí informace o konfiguraci a základního adresáře aplikace</li><li>Pokud sestavení neznámý, vyhledá zavaděč aktuální sestavení, mscorlib a umístění vrácený obslužné rutiny události TypeResolve</li><li>Toto pořadí hledání CLR lze upravit s háky například mechanismus předávání typu a AppDomain.TypeResolve události</li></ol><p>Pokud útočník zneužije pořadí hledání CLR vytvořením alternativní třídy se stejným názvem a umístit ho do alternativního umístění, modul CLR nejprve načte modul CLR bude neúmyslně spusťte útočník zadaný kód</p>|

### <a name="example"></a>Příklad:
`<behaviorExtensions/>` Prvek konfiguračního souboru WCF, které jsou níže dá pokyn WCF pro přidání vlastní chování třídy pro konkrétní rozšíření WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Pomocí plně kvalifikované názvy (silné) jednoznačně identifikuje typu a ještě zvyšuje zabezpečení vašeho systému. Pomocí názvů plně kvalifikovaný při registraci typy v souboru machine.config a app.config soubory.

### <a name="example"></a>Příklad:
`<behaviorExtensions/>` Prvek konfiguračního souboru WCF, které jsou níže dá pokyn WCF pro přidání třídy silného odkazuje vlastní chování konkrétní rozšíření WCF.
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

## <a id="wcf-authz"></a>Řízení autorizace implementace WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | <p>Tato služba nevyužívá ovládací prvek autorizace. Když klient zavolá konkrétní službu WCF, poskytuje WCF různých režimů autorizace, které ověřte, zda má volající oprávnění ke spuštění metody služby na serveru. Pokud autorizace ovládací prvky nejsou povolené pro služby WCF, ověřeného uživatele můžete dosáhnout zvýšení úrovně oprávnění.</p>|

### <a name="example"></a>Příklad:
Následující konfigurace dá pokyn k není zkontrolovat úroveň ověření klienta při spouštění služby WCF:
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
Ověřte, že je volající metody služby oprávnění k tomu pomocí schématu autorizace služby. WCF poskytuje dva režimy a umožňuje definici schématu vlastní autorizace. Režim UseWindowsGroups používá role systému Windows a uživatelé a režim UseAspNetRoles používá poskytovatele rolí prostředí ASP.NET, jako je SQL Server, k ověření.

### <a name="example"></a>Příklad:
WCF a ujistěte se, že klient je součástí skupiny Administrators před spuštěním služby přidat dá pokyn, následující konfiguraci:
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

## <a id="authz-aspnet"></a>Implementace mechanismus správné autorizace v rozhraní ASP.NET Web API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Generic, MVC5 |
| **Atributy**              | Není k dispozici, zprostředkovatel Identity zprostředkovatel – AD FS, Identity – Azure AD |
| **Odkazy**              | [Ověřování a autorizace v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroky** | <p>Informace o rolích uživatelů aplikace může být odvozen z Azure AD nebo uvedli deklarací služby AD FS, pokud aplikace využívá na ně jako zprostředkovatel Identity nebo vlastní aplikace. V některém z těchto případech by měl provedení vlastní autorizace ověření informací o roli uživatele.</p><p>Informace o rolích uživatelů aplikace může být odvozen z Azure AD nebo uvedli deklarací služby AD FS, pokud aplikace využívá na ně jako zprostředkovatel Identity nebo vlastní aplikace. V některém z těchto případech by měl provedení vlastní autorizace ověření informací o roli uživatele.</p>

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
Všechny řadiče a metody akce, které je potřeba chránit by měl být doplněny pomocí výše atribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Provedení kontroly autorizace v zařízení, pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Zařízení musí autorizovat volající ke kontrole, pokud má volající potřebná oprávnění k provedení požadované akce. Pro umožňuje například stát, že zařízení je Smart Lock dveří, který se dá sledovat z cloudu a navíc poskytuje funkce, jako je vzdálené uzamčení dveří.</p><p>Smart Lock dveře poskytuje odemykání funkce jenom v případě, že někdo fyzicky součástí téměř dvířka na kartě. V takovém případě by mělo být provedeno implementace vzdálené příkazy a ovládání tak, že nenabízí žádné funkce odemknout dvířka jako cloudová brána nemá oprávnění k odeslání příkazu k odemknutí dveří.</p>|

## <a id="field-permission"></a>Provedení kontroly autorizace v brána pole, pokud ji podporuje různé akce, které vyžadují různé úrovně oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Brána pole by měl povolit volající ke kontrole, pokud má volající potřebná oprávnění k provedení požadované akce. Například by měla být jiná oprávnění správce uživatelské rozhraní nebo rozhraní API slouží ke konfiguraci zařízení brány v nebo s pole, které se připojují k němu.|
