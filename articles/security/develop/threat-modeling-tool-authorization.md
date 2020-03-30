---
title: Autorizace – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728373"
---
# <a name="security-frame-authorization--mitigations"></a>Bezpečnostní rámec: Autorizace | Skutečnosti snižující závažnost rizika 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že správné licence ACL jsou konfigurovány tak, aby omezovaly neoprávněný přístup k datům v zařízení.](#acl-restricted-access)</li><li>[Ujistěte se, že citlivý obsah aplikace specifický pro uživatele je uložen v adresáři profilů uživatelů](#sensitive-directory)</li><li>[Ujistěte se, že nasazené aplikace jsou spuštěny s nejnižšími oprávněními](#deployed-privileges)</li></ul> |
| **Webová aplikace** | <ul><li>[Vynucení pořadí sekvenčních kroků při zpracování toků obchodní logiky](#sequential-logic)</li><li>[Implementovat mechanismus omezení rychlosti, aby se zabránilo výčtu](#rate-enumeration)</li><li>[Zajistěte, aby bylo zavedeno řádné oprávnění a aby byla dodržována zásada nejnižších práv](#principle-least-privilege)</li><li>[Rozhodnutí o autorizaci obchodní logiky a přístupu k prostředkům by neměla být založena na parametrech příchozích požadavků.](#logic-request-parameters)</li><li>[Zajistit, aby obsah a prostředky nebyly početné nebo přístupné prostřednictvím vynuceného procházení](#enumerable-browsing)</li></ul> |
| **Databáze** | <ul><li>[Ujistěte se, že se k databázovému serveru používají nejméně privilegované účty](#privileged-server)</li><li>[Implementujte RLS zabezpečení na úrovni řádku, abyste zabránili klientům v přístupu k datům ostatních](#rls-tenants)</li><li>[Role Sysadmin by měla mít pouze platné potřebné uživatele.](#sysadmin-users)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Připojení ke cloudové bráně pomocí nejméně privilegovaných tokenů](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Použití klíče SAS pouze pro odesílání pro generování tokenů zařízení](#sendonly-sas)</li><li>[Nepoužívejte přístupové tokeny, které poskytují přímý přístup k centru událostí](#access-tokens-hub)</li><li>[Připojení k centru událostí pomocí klíčů SAS, které mají požadovaná minimální oprávnění](#sas-minimum-permissions)</li></ul> |
| **Databáze dokumentů Azure** | <ul><li>[Kdykoli je to možné, použijte tokeny prostředků k připojení k Azure Cosmos DB](#resource-docdb)</li></ul> |
| **Hranice důvěryhodnosti Azure** | <ul><li>[Povolení správy jemně odstupňovaných přístupů k předplatnému Azure pomocí RBAC](#grained-rbac)</li></ul> |
| **Hranice vztahu důvěryhodnosti služby Fabric** | <ul><li>[Omezení přístupu klienta k operacím clusteru pomocí RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Provádění modelování zabezpečení a v případě potřeby použití zabezpečení na úrovni pole](#modeling-field)</li></ul> |
| **Portál Dynamics CRM** | <ul><li>[Provádění modelování zabezpečení účtů portálu s ohledem na to, že model zabezpečení pro portál se liší od zbytku aplikace CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Udělit jemně odstupňovaná oprávnění pro celou řadu entit v Azure Table Storage](#permission-entities)</li><li>[Povolení řízení přístupu na základě rolí (RBAC) k účtu úložiště Azure pomocí Správce prostředků Azure](#rbac-azure-manager)</li></ul> |
| **Mobilní klient** | <ul><li>[Implementujte implicitní útěk z vězení nebo detekci zakořenění](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Odkaz na slabou třídu v WCF](#weak-class-wcf)</li><li>[Ovládací prvek autorizace WCF-Implement](#wcf-authz)</li></ul> |
| **Web API** | <ul><li>[Implementace mechanismu správné autorizace v ASP.NET webovém rozhraní API](#authz-aspnet)</li></ul> |
| **Zařízení IoT** | <ul><li>[Provádění kontrol autorizací v zařízení, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění](#device-permission)</li></ul> |
| **Brána ioT pole** | <ul><li>[Provádění kontrol autorizací v bráně polí, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Ujistěte se, že správné licence ACL jsou konfigurovány tak, aby omezovaly neoprávněný přístup k datům v zařízení.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že správné licence ACL jsou konfigurovány tak, aby omezovaly neoprávněný přístup k datům v zařízení.|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Ujistěte se, že citlivý obsah aplikace specifický pro uživatele je uložen v adresáři profilů uživatelů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že citlivý obsah aplikace specifický pro uživatele je uložen v adresáři profilu uživatele. To to je zabránit více uživatelům počítače v přístupu k datům navzájem.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Ujistěte se, že nasazené aplikace jsou spuštěny s nejnižšími oprávněními

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že nasazená aplikace je spuštěna s nejnižšími oprávněními. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Vynucení pořadí sekvenčních kroků při zpracování toků obchodní logiky

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Chcete-li ověřit, že tato fáze byla spuštěna skutečným uživatelem, chcete vynutit aplikaci pouze pro zpracování toků obchodní logiky pouze v pořadí sekvenčních kroků, přičemž všechny kroky jsou zpracovávány v realistickém lidském čase a nezpracovávají mimo pořadí, přeskočily kroky , zpracované kroky od jiného uživatele nebo příliš rychle odeslané transakce.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementovat mechanismus omezení rychlosti, aby se zabránilo výčtu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že citlivé identifikátory jsou náhodné. Implementujte ovládací prvek CAPTCHA na anonymních stránkách. Ujistěte se, že chyba a výjimka by neměly odhalit konkrétní data|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Zajistěte, aby bylo zavedeno řádné oprávnění a aby byla dodržována zásada nejnižších práv

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Tato zásada znamená, že uživatelský účet poskytuje pouze ta oprávnění, která jsou pro to, aby uživatelé pracovali, jsou nezbytná. Například uživatel zálohy nemusí instalovat software: proto má uživatel zálohy práva pouze ke spuštění aplikací souvisejících se zálohováním a zálohováním. Všechna ostatní oprávnění, například instalace nového softwaru, jsou blokována. Princip platí i pro uživatele osobního počítače, který obvykle pracuje v běžném uživatelském účtu a otevírá privilegovaný účet chráněný heslem (tj. superuživatele) pouze tehdy, když to situace naprosto vyžaduje. </p><p>Tento princip lze použít i pro vaše webové aplikace. Místo toho, aby pouze v závislosti na metodách ověřování založených na rolích pomocí relací, chceme spíše přiřadit oprávnění uživatelům prostřednictvím systému ověřování na základě databáze. Stále používáme relace k určení, zda byl uživatel přihlášen správně, teprve nyní namísto přiřazení tohoto uživatele s konkrétní rolí mu přiřadíme oprávnění k ověření, které akce má v systému oprávnění. Také velký pro tuto metodu je, kdykoli uživatel musí být přiřazena méně oprávnění vaše změny budou použity za běhu, protože přiřazení nezávisí na relaci, která jinak musela vypršet jako první.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Rozhodnutí o autorizaci obchodní logiky a přístupu k prostředkům by neměla být založena na parametrech příchozích požadavků.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Kdykoli kontrolujete, zda je uživatel omezen na kontrolu určitých dat, měla by být zpracovat omezení přístupu na straně serveru. Id uživatele by mělo být uloženo uvnitř proměnné relace při přihlášení a mělo by být použito k načtení uživatelských dat z databáze. |

### <a name="example"></a>Příklad
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nyní možný útočník nemůže manipulovat a měnit operaci aplikace, protože identifikátor pro načítání dat je zpracován na straně serveru.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Zajistit, aby obsah a prostředky nebyly početné nebo přístupné prostřednictvím vynuceného procházení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Citlivé statické a konfigurační soubory by neměly být uchovávány ve webovém kořenovém adresáři. Aby obsah nemusel být veřejný, měly by být použity buď řádné kontroly přístupu, nebo odstranění samotného obsahu.</p><p>Také, vynucené procházení je obvykle v kombinaci s brute force techniky shromažďovat informace tím, že se pokusí o přístup k co nejvíce adres URL, jak je to možné vytvořit výčet adresářů a souborů na serveru. Útočníci mohou zkontrolovat všechny varianty běžně existujících souborů. Vyhledávání souborů hesel by například zahrnovalo soubory včetně souborů psswd.txt, password.htm, password.dat a dalších variant.</p><p>Chcete-li zmírnit to, měly by být zahrnuty možnosti pro detekci pokusů o hrubou sílu.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Ujistěte se, že se k databázovému serveru používají nejméně privilegované účty

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Hierarchie oprávnění databáze SQL](https://msdn.microsoft.com/library/ms191465), [seřizovatelné databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Nejméně privilegované účty by měly být použity pro připojení k databázi. Přihlášení aplikace by měla být omezena v databázi a by měla spouštět pouze vybrané uložené procedury. Přihlášení aplikace by nemělo mít přímý přístup k tabulce. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementujte RLS zabezpečení na úrovni řádku, abyste zabránili klientům v přístupu k datům ostatních

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Sql Azure, OnPrem |
| **Atributy**              | VERZE SQL - V12, VERZE SQL - MSSQL2016 |
| **Odkazy**              | [Zabezpečení na úrovni řádků serveru SQL Server (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroky** | <p>Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění).</p><p>Zabezpečení na úrovni řádků (RLS) zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pro zajištění, že pracovníci mají přístup pouze k datovým řádkům, které se vztahují k jejich oddělení, nebo pro omezení přístupu zákazníků pouze k datům souvisejícím s jejich společností.</p><p>Logika omezení přístupu se nachází v databázové vrstvě, nikoli mimo data v jiné aplikační vrstvě. Databázový systém použije omezení přístupu při každém pokusu o přístup k datům z libovolné vrstvy. Díky tomu je bezpečnostní systém spolehlivější a robustnější díky zmenšení plochy bezpečnostního systému.</p><p>|

Upozorňujeme, že rls jako out-of-the-box databázi funkce je použitelná pouze pro SQL Server od 2016 a Azure SQL databáze. Pokud není implementována funkce RLS, mělo by být zajištěno, že přístup k datům je omezen pomocí zobrazení a postupů.

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Role Sysadmin by měla mít pouze platné potřebné uživatele.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Hierarchie oprávnění databáze SQL](https://msdn.microsoft.com/library/ms191465), [seřizovatelné databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Členové pevné role serveru SysAdmin by měly být velmi omezené a nikdy obsahovat účty používané aplikacemi.  Zkontrolujte seznam uživatelů v roli a odeberte všechny nepotřebné účty.|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Připojení ke cloudové bráně pomocí nejméně privilegovaných tokenů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Odkazy**              | [Řízení přístupu k centru Iot](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroky** | Poskytněte oprávnění k různým součástem, které se připojují ke cloudové bráně (IoT Hub). Typickým příkladem je – součást správy a zřizování zařízení používá registryread/write, Event Processor (ASA) používá Service Connect. Jednotlivá zařízení se připojují pomocí přihlašovacích údajů zařízení|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Použití klíče SAS pouze pro odesílání pro generování tokenů zařízení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Přehled modelu ověřování a zabezpečení centra událostí](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Klíč SAS se používá ke generování tokenů jednotlivých zařízení. Při generování tokenu zařízení pro daného vydavatele použijte klíč SAS pouze pro odesílání.|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Nepoužívejte přístupové tokeny, které poskytují přímý přístup k centru událostí

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Přehled modelu ověřování a zabezpečení centra událostí](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Token, který uděluje přímý přístup k centru událostí by nemělbýt poskytnuta zařízení. Použití nejméně privilegovaného tokenu pro zařízení, které poskytuje přístup pouze vydavateli, by pomohlo identifikovat a zařadit jej na černou listinu, pokud by bylo zjištěno, že je nepoctivé nebo kompromitované zařízení.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Připojení k centru událostí pomocí klíčů SAS, které mají požadovaná minimální oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Přehled modelu ověřování a zabezpečení centra událostí](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Poskytněte oprávnění k různým back-endovým aplikacím, které se připojují k centru událostí. Generovat samostatné klíče SAS pro každou back-endovou aplikaci a poskytnout pouze požadovaná oprávnění - Odeslat, Přijmout nebo Spravovat na ně.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Pokud je to možné, použijte tokeny prostředků pro připojení k Cosmos DB

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze dokumentů Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Token prostředku je přidružený k prostředku oprávnění Azure Cosmos DB a zachycuje vztah mezi uživatelem databáze a oprávněním, které má uživatel pro konkrétní prostředek aplikace Azure Cosmos DB (např. kolekce, dokument). Vždy používejte token prostředku pro přístup k Azure Cosmos DB, pokud klientovi nelze důvěřovat při zpracování hlavních klíčů nebo klíčů jen pro čtení – jako je aplikace koncového uživatele, jako je mobilní nebo desktopový klient. Použijte hlavní klíč nebo klíče jen pro čtení z back-endových aplikací, které mohou tyto klíče bezpečně ukládat.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Povolení správy jemně odstupňovaných přístupů k předplatnému Azure pomocí RBAC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroky** | Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Omezení přístupu klienta k operacím clusteru pomocí RBAC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti služby Fabric | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Řízení přístupu na základě rolí pro klienty Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroky** | <p>Azure Service Fabric podporuje dva různé typy řízení přístupu pro klienty, kteří jsou připojeni k clusteru Service Fabric: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým operacím clusteru pro různé skupiny uživatelů, čímž se cluster více zabezpečí.</p><p>Správci mají plný přístup k možnostem správy (včetně možností čtení a zápisu). Uživatelé mají ve výchozím nastavení pouze přístup pro čtení k možnostem správy (například možnosti dotazů) a možnost řešení aplikací a služeb.</p><p>Dvě role klienta (správce a klient) při vytváření clusteru zadáte poskytnutím samostatných certifikátů pro každou z nich.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Provádění modelování zabezpečení a v případě potřeby použití zabezpečení na úrovni pole

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Provádění modelování zabezpečení a v případě potřeby použití zabezpečení na úrovni pole|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Provádění modelování zabezpečení účtů portálu s ohledem na to, že model zabezpečení pro portál se liší od zbytku aplikace CRM

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Portál Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Provádění modelování zabezpečení účtů portálu s ohledem na to, že model zabezpečení pro portál se liší od zbytku aplikace CRM|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Udělit jemně odstupňovaná oprávnění pro celou řadu entit v Azure Table Storage

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – tabulka |
| **Odkazy**              | [Jak delegovat přístup k objektům v účtu úložiště Azure pomocí SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroky** | V některých obchodních scénářích azure table storage může být vyžadováno k ukládání citlivých dat, která se zaměřuje na různé strany. Například citlivé údaje týkající se různých zemí/oblastí. V takových případech lze vytvořit podpisy SAS zadáním rozsahů klíčů oddílu a řádku tak, aby uživatel měl přístup k datům specifickým pro určitou zemi nebo oblast.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Povolení řízení přístupu na základě rolí (RBAC) k účtu úložiště Azure pomocí Správce prostředků Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Jak zabezpečit účet úložiště pomocí řízení přístupu na základě rolí (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroky** | <p>Když vytvoříte nový účet úložiště, vyberete model nasazení Klasické nebo Azure Resource Manager. Klasický model vytváření prostředků v Azure umožňuje pouze přístup k předplatnému a zase účet úložiště.</p><p>S modelem Azure Resource Manager uložte účet úložiště do skupiny prostředků a řídíte přístup k rovině správy tohoto konkrétního účtu úložiště pomocí služby Azure Active Directory. Můžete například poskytnout konkrétním uživatelům možnost přístupu ke klíčům účtu úložiště, zatímco ostatní uživatelé mohou zobrazit informace o účtu úložiště, ale nemají přístup ke klíčům účtu úložiště.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementujte implicitní útěk z vězení nebo detekci zakořenění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Aplikace by měla chránit svou vlastní konfiguraci a uživatelská data v případě, že telefon je zakořeněný nebo vězení rozbité. Zakořenění / vězení lámání znamená neoprávněný přístup, který normální uživatelé nebudou dělat na svých vlastních telefonech. Proto aplikace by měla mít implicitní logiku zjišťování při spuštění aplikace, zjistit, zda byl telefon zakořeněn.</p><p>Logika zjišťování může být jednoduše přístup k souborům, které obvykle pouze root uživatel může přistupovat, například:</p><ul><li>/systém/aplikace/Superuser.apk</li><li>/sbin/su</li><li>/systém/přihrádka/su</li><li>/systém/xbin/su</li><li>/data/místní/xbin/su</li><li>/data/místní/bin/su</li><li>/systém/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/místní/su</li></ul><p>Pokud aplikace může přistupovat k některému z těchto souborů, znamená to, že aplikace je spuštěna jako kořenový uživatel.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Odkaz na slabou třídu v WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Systém používá odkaz na slabou třídu, která by mohla útočníkovi umožnit spuštění neoprávněného kódu. Program odkazuje na uživatelem definovanou třídu, která není jednoznačně identifikována. Když rozhraní .NET načte tuto slabě identifikovanou třídu, zavaděč typu CLR vyhledá třídu v následujících umístěních v zadaném pořadí:</p><ol><li>Pokud je sestavení typu známo, zavaděč prohledá umístění přesměrování konfiguračního souboru, GAC, aktuální sestavení pomocí informací o konfiguraci a základní adresář aplikace</li><li>Pokud je sestavení neznámé, zavaděč prohledá aktuální sestavení, mscorlib a umístění vrácené obslužnou rutinou události TypeResolve.</li><li>Toto pořadí hledání CLR lze upravit pomocí háků, jako je například mechanismus přeposílání typu a událost AppDomain.TypeResolve.</li></ol><p>Pokud útočník zneužije pořadí hledání CLR vytvořením alternativní třídy se stejným názvem a jejím umístěním na alternativním místě, které se načte clr jako první, clr neúmyslně spustí kód dodaný útočníkem</p>|

### <a name="example"></a>Příklad
Prvek `<behaviorExtensions/>` konfiguračního souboru WCF níže pokyn WCF přidat vlastní třídu chování na konkrétní rozšíření WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Použití plně kvalifikovaných (silných) názvů jednoznačně identifikuje typ a dále zvyšuje zabezpečení systému. Při registraci typů v souborech machine.config a app.config používejte plně kvalifikované názvy sestavení.

### <a name="example"></a>Příklad
Prvek `<behaviorExtensions/>` konfiguračního souboru WCF níže pokyn WCF přidat silně odkazované vlastní třídy chování na konkrétní rozšíření WCF.
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

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>Ovládací prvek autorizace WCF-Implement

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Tato služba nepoužívá ovládací prvek autorizace. Když klient volá určitou službu WCF, WCF poskytuje různá autorizační schémata, která ověřují, že volající má oprávnění ke spuštění metody služby na serveru. Pokud autorizační ovládací prvky nejsou povoleny pro služby WCF, může ověřený uživatel dosáhnout eskalace oprávnění.</p>|

### <a name="example"></a>Příklad
Následující konfigurace instruuje WCF, aby při provádění služby nekontroloval úroveň autorizace klienta:
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
Pomocí schématu autorizace služby ověřte, zda je k tomu oprávněn volající metody služby. WCF poskytuje dva režimy a umožňuje definici vlastního autorizačního schématu. Režim UseWindowsGroups používá role systému Windows a uživatele a režim UseAspNetRoles používá k ověření zprostředkovatele rolí ASP.NET, například SQL Server.

### <a name="example"></a>Příklad
Následující konfigurace dává wcf pokyn, aby se před spuštěním služby Add ujistil, že klient je součástí skupiny Administrators:
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
Služba je pak deklarována jako následující:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementace mechanismu správné autorizace v ASP.NET webovém rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, MVC5 |
| **Atributy**              | Není k tomu, zprostředkovatel identity – ADFS, zprostředkovatel identity – Azure AD |
| **Odkazy**              | [Ověřování a autorizace ve webovém rozhraní API ASP.NET](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroky** | <p>Informace o rolích pro uživatele aplikace lze odvodit z deklarací Azure AD nebo ADFS, pokud se na ně aplikace spoléhá jako na poskytovatele identity nebo že ji může poskytnout samotná aplikace. V každém z těchto případů by měla vlastní implementace autorizace ověřit informace o roli uživatele.</p><p>Informace o rolích pro uživatele aplikace lze odvodit z deklarací Azure AD nebo ADFS, pokud se na ně aplikace spoléhá jako na poskytovatele identity nebo že ji může poskytnout samotná aplikace. V každém z těchto případů by měla vlastní implementace autorizace ověřit informace o roli uživatele.</p>

### <a name="example"></a>Příklad
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
Všechny řadiče a akční metody, které je třeba chránit by měly být dekorovány výše atribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Provádění kontrol autorizací v zařízení, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Zařízení by mělo volajícímu povolit ke kontrole, zda má volající požadovaná oprávnění k provedení požadované akce. Řekněme například, že zařízení je inteligentní zámek dveří, který lze sledovat z cloudu, a navíc poskytuje funkce, jako je vzdálené zamykání dveří.</p><p>Inteligentní zámek dveří poskytuje funkci odemykání pouze tehdy, když se někdo fyzicky přiblíží ke dveřím s kartou. V tomto případě by implementace dálkového příkazu a řízení měla být provedena takovým způsobem, že neposkytuje žádné funkce pro odemknutí dveří, protože cloudová brána není oprávněna odeslat příkaz k odemknutí dveří.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Provádění kontrol autorizací v bráně polí, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána ioT pole | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Brána polí by měla volajícímu povolit ke kontrole, zda má volající požadovaná oprávnění k provedení požadované akce. Například by měla existovat různá oprávnění pro uživatelské rozhraní správce/rozhraní API používané ke konfiguraci zařízení v/s brány v poli, která se k němu připojují.|
