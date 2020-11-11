---
title: Autorizace – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o zmírnění autorizace v Threat Modeling Tool. Podívejte se na seznam potenciálních hrozeb a pokyny pro zmírnění rizik.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d99295fbb355b3efa22a64c9adc04311508e474
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517559"
---
# <a name="security-frame-authorization--mitigations"></a>Rámec zabezpečení: autorizace | Hrozeb 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že jsou nakonfigurovány správné seznamy ACL pro omezení neoprávněného přístupu k datům v zařízení.](#acl-restricted-access)</li><li>[Zajistěte, aby byl v adresáři User-Profile uložený obsah aplikace citlivý pro uživatele.](#sensitive-directory)</li><li>[Zajistěte, aby byly nasazené aplikace spuštěné s nejnižšími oprávněními.](#deployed-privileges)</li></ul> |
| **Webová aplikace** | <ul><li>[Vymáhat pořadí sekvenčních kroků při zpracování toků obchodních logiky](#sequential-logic)</li><li>[Implementujte mechanismus omezování četnosti, aby se zabránilo výčtu.](#rate-enumeration)</li><li>[Zajistěte, aby byla zajištěna správná autorizace a následovala zásada minimálního oprávnění.](#principle-least-privilege)</li><li>[Obchodní logika a rozhodnutí o autorizaci přístupu k prostředkům by neměly být založené na parametrech příchozích požadavků.](#logic-request-parameters)</li><li>[Ujistěte se, že obsah a prostředky nejsou vyčíslitelné nebo přístupné prostřednictvím vynuceného procházení.](#enumerable-browsing)</li></ul> |
| **Databáze** | <ul><li>[Zajistěte, aby se pro připojení k databázovému serveru používaly minimálně privilegované účty.](#privileged-server)</li><li>[Implementujte zabezpečení na úrovni řádků, abyste klientům zabránili v přístupu k ostatním datům.](#rls-tenants)</li><li>[Role sysadmin by měla mít pouze platné potřebné uživatele.](#sysadmin-users)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Připojení ke cloudové bráně s použitím nejnižších privilegovaných tokenů](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Pro generování tokenů zařízení použít jenom klíč SAS s oprávněním pro odesílání](#sendonly-sas)</li><li>[Nepoužívejte přístupové tokeny, které poskytují přímý přístup k centru událostí.](#access-tokens-hub)</li><li>[Připojte se k centru událostí pomocí klíčů SAS, které mají minimální požadovaná oprávnění.](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Pokud je to možné, použijte k připojení Azure Cosmos DB tokeny prostředků.](#resource-docdb)</li></ul> |
| **Hranice důvěry Azure** | <ul><li>[Povolení jemně odstupňovaného řízení přístupu k předplatnému Azure pomocí RBAC](#grained-rbac)</li></ul> |
| **Service Fabric hranice důvěryhodnosti** | <ul><li>[Omezení přístupu klienta k operacím clusteru pomocí RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[V případě potřeby proveďte modelování zabezpečení a použijte zabezpečení na úrovni polí.](#modeling-field)</li></ul> |
| **Portál Dynamics CRM** | <ul><li>[Provedete si modelování zabezpečení účtů portálu a mějte na paměti, že se model zabezpečení portálu liší od zbytku CRM.](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Udělení jemně odstupňovaného oprávnění pro řadu entit v Azure Table Storage](#permission-entities)</li><li>[Povolení Role-Based Access Control (RBAC) pro účet úložiště Azure pomocí Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobilní klient** | <ul><li>[Implementovat implicitní jailbreaků nebo detekci kořene](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Slabý odkaz na třídu ve WCF](#weak-class-wcf)</li><li>[WCF – implementace autorizačního řízení](#wcf-authz)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Implementace správného autorizačního mechanismu ve webovém rozhraní API ASP.NET](#authz-aspnet)</li></ul> |
| **Zařízení IoT** | <ul><li>[Provádět kontroly autorizace v zařízení, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění](#device-permission)</li></ul> |
| **Brána pole IoT** | <ul><li>[Provádět kontroly autorizace v bráně pole, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění.](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Ujistěte se, že jsou nakonfigurovány správné seznamy ACL pro omezení neoprávněného přístupu k datům v zařízení.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Ujistěte se, že jsou nakonfigurovány správné seznamy ACL pro omezení neoprávněného přístupu k datům v zařízení.|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Zajistěte, aby byl v adresáři User-Profile uložený obsah aplikace citlivý pro uživatele.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Zajistěte, aby byl v adresáři uživatelských profilů uložený obsah aplikace citlivý pro uživatele. K tomu je potřeba zabránit více uživatelům počítače v přístupu k ostatním datům.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Zajistěte, aby byly nasazené aplikace spuštěné s nejnižšími oprávněními.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Zajistěte, aby byla nasazená aplikace spuštěná s nejnižšími oprávněními. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Vymáhat pořadí sekvenčních kroků při zpracování toků obchodních logiky

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Aby bylo možné ověřit, že byla tato fáze spuštěna pomocí originálního uživatele, který chcete vynutilit, aby aplikace zpracovala toky obchodních logiky v sekvenčním pořadí, se všemi kroky zpracovávanými v reálných případech, které jsou zpracovávány v reálném čase, byly přeskočeny kroky, zpracovány kroky od jiného uživatele nebo příliš rychle odeslané transakce.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementujte mechanismus omezování četnosti, aby se zabránilo výčtu.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Zajistěte, aby byly citlivé identifikátory náhodné. Implementuje ovládací prvek CAPTCHA na anonymních stránkách. Zajistěte, aby chyba a výjimka neměly odhalit konkrétní data|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Zajistěte, aby byla zajištěna správná autorizace a následovala zásada minimálního oprávnění.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Princip poskytuje uživatelský účet pouze ta oprávnění, která jsou pro tyto uživatele zásadní. Například uživatel pro zálohování nemusí instalovat software: proto má uživatel pro zálohování oprávnění pouze ke spouštění aplikací souvisejících se zálohováním a zálohováním. Všechna další oprávnění, jako je instalace nového softwaru, jsou blokovaná. Princip platí také pro uživatele osobního počítače, který obvykle pracuje v normálním uživatelském účtu a otevírá privilegovaný účet chráněný heslem (tj. uživatel) pouze v případě, že je přesně vydává. </p><p>Tato zásada se dá použít i pro vaše webové aplikace. Místo výhradně v závislosti na metodách ověřování založených na rolích pomocí relací chceme k uživatelům přiřadit oprávnění prostřednictvím Database-Basedho ověřovacího systému. Pořád používáme relace, abychom zjistili, jestli byl uživatel přihlášený správně, jenom teď namísto přiřazení tohoto uživatele k určité roli, kterou mu přiřadíme, aby ověřil, které akce má k systému oprávnění provést. Tato metoda je také velká pro tuto metodu, kdykoli se uživateli přiřadí méně oprávnění, která se použijí, protože přiřazení nezávisí na relaci, která jinak musela nejdřív vypršet.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Obchodní logika a rozhodnutí o autorizaci přístupu k prostředkům by neměly být založené na parametrech příchozích požadavků.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Kdykoli kontrolujete, jestli je uživatel omezený, aby zkontroloval určitá data, měla by být omezení přístupu zpracovaná na straně serveru. Identifikátor userID by měl být uložený v proměnné relace při přihlášení a měl by se použít k načtení uživatelských dat z databáze. |

### <a name="example"></a>Příklad
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nyní možný Útočník nemůže manipulovat a měnit operaci aplikace, protože identifikátor pro načítání dat je zpracován na straně serveru.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Ujistěte se, že obsah a prostředky nejsou vyčíslitelné nebo přístupné prostřednictvím vynuceného procházení.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Citlivé statické a konfigurační soubory by neměly být uchovávány v kořenovém adresáři webu. Pro obsah, který není nezbytný k veřejnému, by se měly použít buď správné ovládací prvky pro přístup, nebo odebrat samotný obsah.</p><p>Vynucené procházení je obvykle kombinované s technikami hrubou silou pro shromažďování informací pomocí pokusu o přístup k tolika adresám URL, aby bylo možné vytvořit výčet adresářů a souborů na serveru. Útočníci můžou kontrolovat všechny varianty často existujících souborů. Například hledání souborů hesla by zahrnovalo soubory, mezi které patří psswd.txt, password.htm, Password. dat a další variace.</p><p>Chcete-li tyto možnosti zmírnit, je třeba zahrnout funkce pro detekci pokusů o útok hrubou silou.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Zajistěte, aby se pro připojení k databázovému serveru používaly minimálně privilegované účty.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Hierarchie oprávnění SQL](/sql/relational-databases/security/permissions-hierarchy-database-engine), [zabezpečit SQL](/sql/relational-databases/security/securables) |
| **Kroky** | Pro připojení k databázi by se měly použít aspoň privilegované účty. Přihlášení aplikace by mělo být omezeno v databázi a mělo by se provádět pouze vybrané uložené procedury. Přihlašovací jméno aplikace by nemělo mít přímý přístup k tabulce. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementujte zabezpečení na úrovni řádků, abyste klientům zabránili v přístupu k ostatním datům.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | SQL verze – V12, verze SQL – MsSQL2016 |
| **Reference**              | [Zabezpečení SQL Server Row-Level (RLS)](/sql/relational-databases/security/row-level-security) |
| **Kroky** | <p>Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění).</p><p>Row-Level Security (RLS) zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pro zajištění, že pracovníci mají přístup pouze k datovým řádkům, které se vztahují k jejich oddělení, nebo pro omezení přístupu zákazníků pouze k datům souvisejícím s jejich společností.</p><p>Logika omezení přístupu se nachází v databázové vrstvě, nikoli z dat v jiné aplikační vrstvě. Databázový systém použije omezení přístupu při každém pokusu o přístup k datům z libovolné úrovně. Tím se zajistí spolehlivější a robustní zabezpečení systému tím, že se zmenší plocha plochy systému zabezpečení.</p><p>|

Mějte na paměti, že funkce RLS jako dostupná databáze je k dispozici pouze pro SQL Server spouštění 2016, Azure SQL Database a SQL Managed instance. Pokud není implementovaná funkce samoobslužná aplikace, měla by být zajištěna omezení přístupu k datům pomocí zobrazení a postupů.

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Role sysadmin by měla mít pouze platné potřebné uživatele.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Hierarchie oprávnění SQL](/sql/relational-databases/security/permissions-hierarchy-database-engine), [zabezpečit SQL](/sql/relational-databases/security/securables) |
| **Kroky** | Členové pevné role serveru SysAdmin by měli být velmi omezené a nikdy neobsahují účty používané aplikacemi.  Zkontrolujte seznam uživatelů v roli a odeberte všechny nepotřebné účty.|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Připojení ke cloudové bráně s použitím nejnižších privilegovaných tokenů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Reference**              | [Access Control IoT Hub](../../iot-hub/iot-hub-devguide.md) |
| **Kroky** | Poskytněte minimální oprávnění pro různé komponenty, které se připojují ke cloudové bráně (IoT Hub). Typickým příkladem je – služba Device Management/zřizování používá registryread/Write. procesor událostí (ASA) používá službu Connect. Jednotlivá zařízení se připojují pomocí přihlašovacích údajů zařízení.|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Pro generování tokenů zařízení použít jenom klíč SAS s oprávněním pro odesílání

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Přehled ověřování a modelu zabezpečení Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Kroky** | Klíč SAS se používá ke generování jednotlivých tokenů zařízení. Při generování tokenu zařízení pro daného vydavatele používejte oprávnění SAS jenom pro odesílání.|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Nepoužívejte přístupové tokeny, které poskytují přímý přístup k centru událostí.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Přehled ověřování a modelu zabezpečení Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Kroky** | Token, který uděluje přímý přístup do centra událostí, by neměl být předaný zařízení. Použití s nejnižším privilegovaným tokenem pro zařízení, které poskytuje přístup jenom vydavateli, pomůžete ho identifikovat a zakázat, pokud se zjistilo, že se jedná o neautorizovaný nebo ohrožené zařízení.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Připojte se k centru událostí pomocí klíčů SAS, které mají minimální požadovaná oprávnění.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Přehled ověřování a modelu zabezpečení Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Kroky** | Poskytněte minimální oprávnění pro různé back-endové aplikace, které se připojují k centru událostí. Vygenerujte samostatné klíče SAS pro každou back-end aplikaci a poskytněte jim pouze požadovaná oprávnění – Odeslat, přijmout nebo spravovat.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Pokud je to možné, použijte k připojení Cosmos DB tokeny prostředků.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Document DB | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Token prostředku je přidružen k prostředku oprávnění Azure Cosmos DB a zachycuje vztah mezi uživatelem databáze a oprávněním, které má uživatel pro určitý prostředek Azure Cosmos DB aplikace (např. kolekce, dokument). Pro přístup k Azure Cosmos DB vždy použít token prostředku, pokud klienta nemůžete důvěřovat pomocí řídicího panelu nebo klíčů jen pro čtení, jako je aplikace koncového uživatele, jako je například mobilní klient nebo stolní počítač. Použijte hlavní klíč nebo klíče jen pro čtení z back-endu aplikací, které můžou tyto klíče bezpečně uložit.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Povolení jemně odstupňovaného řízení přístupu k předplatnému Azure pomocí RBAC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěry Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md)  |
| **Kroky** | Řízení přístupu na základě role Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu pro Azure. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Omezení přístupu klienta k operacím clusteru pomocí RBAC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Reference**              | [Řízení přístupu na základě role pro klienty Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md) |
| **Kroky** | <p>Azure Service Fabric podporuje pro klienty, kteří jsou připojení ke clusteru Service Fabric, dva různé typy řízení přístupu: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým clusterovým operacím pro různé skupiny uživatelů a tím zvýšit zabezpečení clusteru.</p><p>Správci mají plný přístup k funkcím správy (včetně funkcí pro čtení a zápis). Uživatelé mají ve výchozím nastavení přístup jen pro čtení k funkcím pro správu (například možnosti dotazů) a možnost přeložit aplikace a služby.</p><p>V době vytváření clusteru určíte dvě role klienta (správce a klient), a to tak, že pro každý z nich poskytnete samostatné certifikáty.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>V případě potřeby proveďte modelování zabezpečení a použijte zabezpečení na úrovni polí.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | V případě potřeby proveďte modelování zabezpečení a použijte zabezpečení na úrovni polí.|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Provedete si modelování zabezpečení účtů portálu a mějte na paměti, že se model zabezpečení portálu liší od zbytku CRM.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Portál Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Provedete si modelování zabezpečení účtů portálu a mějte na paměti, že se model zabezpečení portálu liší od zbytku CRM.|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Udělení jemně odstupňovaného oprávnění pro řadu entit v Azure Table Storage

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – tabulka |
| **Reference**              | [Jak delegovat přístup k objektům ve vašem účtu Azure Storage pomocí SAS](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Kroky** | V některých obchodních scénářích může být pro Azure Table Storage nutné ukládat citlivá data, která jsou v různých stranách. Například citlivá data týkající se různých zemí nebo oblastí. V takových případech je možné signatury SAS vytvořit zadáním rozsahu klíče a rozsahů klíčů řádků, aby uživatel měl přístup k datům, která jsou specifická pro určitou zemi nebo oblast.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Povolení Role-Based Access Control (RBAC) pro účet úložiště Azure pomocí Azure Resource Manager

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Jak zabezpečit svůj účet úložiště pomocí Role-Based Access Control (RBAC)](../../storage/blobs/security-recommendations.md) |
| **Kroky** | <p>Když vytváříte nový účet úložiště, vyberete model nasazení Classic nebo Azure Resource Manager. Klasický model vytváření prostředků v Azure umožňuje jenom přístup k předplatnému a k tomuto předplatnému a zároveň účet úložiště.</p><p>Pomocí modelu Azure Resource Manager umístíte účet úložiště do skupiny prostředků a řídíte přístup k rovině správy tohoto konkrétního účtu úložiště pomocí Azure Active Directory. Můžete například udělit konkrétním uživatelům přístup k klíčům účtu úložiště, zatímco jiní uživatelé mohou zobrazit informace o účtu úložiště, ale nemají přístup k klíčům účtu úložiště.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementovat implicitní jailbreaků nebo detekci kořene

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Aplikace by měla chránit vlastní konfiguraci a uživatelská data v případě, že telefon je rootem nebo Jailbreak. Zajailbreakm nebo přerušením implikuje neoprávněný přístup, které normální uživatelé nedělají na svých vlastních telefonech. Proto by měla aplikace mít implicitní detekci detekce při spuštění aplikace, aby zjistila, zda byl telefon rootem.</p><p>Logika detekce může jednoduše přistupovat k souborům, které obvykle mají přístup root user, například:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Pokud aplikace má přístup k některým z těchto souborů, označuje, že aplikace je spuštěna jako uživatel root.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Slabý odkaz na třídu ve WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Systém používá slabý odkaz na třídu, který může útočníkovi umožnit spustit neautorizovaný kód. Program odkazuje na uživatelsky definovanou třídu, která není jednoznačně identifikována. Když rozhraní .NET načte tuto slabě identifikovanou třídu, vyhledá zavaděč typu CLR třídu v následujících umístěních v zadaném pořadí:</p><ol><li>Pokud je známo sestavení typu, zavaděč vyhledá umístění pro přesměrování konfiguračního souboru, GAC, aktuální sestavení s použitím informací o konfiguraci a základního adresáře aplikace.</li><li>Pokud je sestavení neznámé, zavaděč vyhledá aktuální sestavení, mscorlib a umístění, které vrátila obslužná rutina události TypeResolve.</li><li>Toto pořadí vyhledávání CLR se dá upravit pomocí háčků, jako je mechanismus předávání typů a událost AppDomain. TypeResolve.</li></ol><p>Pokud útočník zneužije pořadí vyhledávání CLR vytvořením alternativní třídy se stejným názvem a umístěním do alternativního umístění, které modul CLR načte jako první, modul CLR neúmyslně neprovede kód poskytovaný útočníkem.</p>|

### <a name="example"></a>Příklad
`<behaviorExtensions/>`Element konfiguračního souboru služby WCF vydá pokyn pro WCF, aby přidal vlastní třídu chování do konkrétního rozšíření WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Použití plně kvalifikovaných (silných) názvů jednoznačně identifikuje typ a zvyšuje zabezpečení systému. Při registraci typů do machine.config a app.config souborů použijte plně kvalifikované názvy sestavení.

### <a name="example"></a>Příklad
`<behaviorExtensions/>`Element konfiguračního souboru služby WCF vydá pokyn WCF pro přidání silně odkazované třídy vlastního chování do konkrétního rozšíření WCF.
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

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF – implementace autorizačního řízení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Tato služba nepoužívá řízení autorizací. Když klient zavolá konkrétní službu WCF, poskytuje WCF různá autorizační schémata, která ověřují, jestli má volající oprávnění ke spuštění metody služby na serveru. Pokud nejsou pro služby WCF povolené ovládací prvky pro autorizaci, může ověřený uživatel dosáhnout eskalace oprávnění.</p>|

### <a name="example"></a>Příklad
Následující konfigurace instruuje službu WCF, aby při spuštění služby nekontrolovala úroveň autorizace klienta:
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
Pomocí autorizačního schématu služby ověřte, zda je volající metoda služby autorizován. WCF nabízí dva režimy a umožňuje definici vlastního autorizačního schématu. Režim UseWindowsGroups používá role a uživatele systému Windows a režim UseAspNetRoles používá poskytovatele role ASP.NET, například SQL Server, k ověření.

### <a name="example"></a>Příklad
Následující konfigurace instruuje službu WCF, aby před spuštěním služby přidání zajistila, že je klient součástí skupiny Administrators:
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
Služba se pak deklaruje jako následující:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementace správného autorizačního mechanismu ve webovém rozhraní API ASP.NET

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, MVC5 |
| **Atributy**              | N/A, zprostředkovatel identity – ADFS, zprostředkovatel identity – Azure AD |
| **Reference**              | [Ověřování a autorizace ve webovém rozhraní API ASP.NET](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroky** | <p>Informace o rolích pro uživatele aplikace můžou být odvozené z deklarací Azure AD nebo AD FS, pokud je aplikace využívá jako zprostředkovatel identity nebo samotná aplikace. V některých těchto případech by implementace vlastní autorizace měla ověřit informace o roli uživatele.</p><p>Informace o rolích pro uživatele aplikace můžou být odvozené z deklarací Azure AD nebo AD FS, pokud je aplikace využívá jako zprostředkovatel identity nebo samotná aplikace. V některých těchto případech by implementace vlastní autorizace měla ověřit informace o roli uživatele.</p>

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
Všechny řadiče a metody akcí, které je třeba chránit, by měly být upraveny pomocí výše uvedeného atributu.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Provádět kontroly autorizace v zařízení, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Zařízení by mělo autorizovat volajícího, aby zkontroloval, jestli má volající potřebná oprávnění k provedení požadované akce. Například umožňuje vyslovit, že zařízení je zámek inteligentních dveří, který se dá monitorovat z cloudu, a poskytuje funkce, jako je vzdálené blokování dveří.</p><p>Zámek inteligentních dvířek poskytuje možnost odemykání funkcí jenom v případě, že se někdo fyzicky nachází poblíž dveří s kartou. V takovém případě by implementace vzdáleného příkazu a ovládacího prvku měla být provedena takovým způsobem, že neposkytuje žádné funkce pro odemknutí dveří, protože cloudová brána nemá autorizaci k odeslání příkazu k odemknutí dveří.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Provádět kontroly autorizace v bráně pole, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Brána Field by měla autorizovat volajícího, aby zkontroloval, jestli má volající potřebná oprávnění k provedení požadované akce. Například by měla existovat různá oprávnění pro uživatelské rozhraní nebo rozhraní API pro správu, které slouží ke konfiguraci zařízení s bránou pole v/s, která se k němu připojují.|