---
title: Autorizace – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: zmírnění rizik pro ohrožené hrozby v Threat Modeling Tool
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
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728373"
---
# <a name="security-frame-authorization--mitigations"></a>Rámec zabezpečení: Autorizace | Hrozeb 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že jsou nakonfigurovány správné seznamy ACL pro omezení neoprávněného přístupu k datům v zařízení.](#acl-restricted-access)</li><li>[Zajistěte, aby byl v adresáři User-Profile uložený obsah aplikace citlivý pro uživatele.](#sensitive-directory)</li><li>[Zajistěte, aby byly nasazené aplikace spuštěné s nejnižšími oprávněními.](#deployed-privileges)</li></ul> |
| **Webová aplikace** | <ul><li>[Vymáhat pořadí sekvenčních kroků při zpracování toků obchodních logiky](#sequential-logic)</li><li>[Implementujte mechanismus omezování četnosti, aby se zabránilo výčtu.](#rate-enumeration)</li><li>[Zajistěte, aby byla zajištěna správná autorizace a následovala zásada minimálního oprávnění.](#principle-least-privilege)</li><li>[Obchodní logika a rozhodnutí o autorizaci přístupu k prostředkům by neměly být založené na parametrech příchozích požadavků.](#logic-request-parameters)</li><li>[Ujistěte se, že obsah a prostředky nejsou vyčíslitelné nebo přístupné prostřednictvím vynuceného procházení.](#enumerable-browsing)</li></ul> |
| **Database** | <ul><li>[Zajistěte, aby se pro připojení k databázovému serveru používaly minimálně privilegované účty.](#privileged-server)</li><li>[Implementujte zabezpečení na úrovni řádků, abyste klientům zabránili v přístupu k ostatním datům.](#rls-tenants)</li><li>[Role sysadmin by měla mít pouze platné potřebné uživatele.](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Připojení ke cloudové bráně s použitím nejnižších privilegovaných tokenů](#cloud-least-privileged)</li></ul> |
| **Centrum událostí Azure** | <ul><li>[Pro generování tokenů zařízení použít jenom klíč SAS s oprávněním pro odesílání](#sendonly-sas)</li><li>[Nepoužívejte přístupové tokeny, které poskytují přímý přístup k centru událostí.](#access-tokens-hub)</li><li>[Připojte se k centru událostí pomocí klíčů SAS, které mají minimální požadovaná oprávnění.](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Pokud je to možné, použijte k připojení Azure Cosmos DB tokeny prostředků.](#resource-docdb)</li></ul> |
| **Hranice důvěry Azure** | <ul><li>[Povolení jemně odstupňovaného řízení přístupu k předplatnému Azure pomocí RBAC](#grained-rbac)</li></ul> |
| **Service Fabric hranice důvěryhodnosti** | <ul><li>[Omezení přístupu klienta k operacím clusteru pomocí RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[V případě potřeby proveďte modelování zabezpečení a použijte zabezpečení na úrovni polí.](#modeling-field)</li></ul> |
| **Portál Dynamics CRM** | <ul><li>[Provedete si modelování zabezpečení účtů portálu a mějte na paměti, že se model zabezpečení portálu liší od zbytku CRM.](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Udělení jemně odstupňovaného oprávnění pro řadu entit v Azure Table Storage](#permission-entities)</li><li>[Povolení Access Control na základě rolí (RBAC) na účet služby Azure Storage pomocí Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobilní klient** | <ul><li>[Implementovat implicitní jailbreaků nebo detekci kořene](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Slabý odkaz na třídu ve WCF](#weak-class-wcf)</li><li>[WCF – implementace autorizačního řízení](#wcf-authz)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Implementace správného autorizačního mechanismu ve webovém rozhraní API ASP.NET](#authz-aspnet)</li></ul> |
| **Zařízení IoT** | <ul><li>[Provádět kontroly autorizace v zařízení, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění](#device-permission)</li></ul> |
| **Brána pole IoT** | <ul><li>[Provádět kontroly autorizace v bráně pole, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění.](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Ujistěte se, že jsou nakonfigurovány správné seznamy ACL pro omezení neoprávněného přístupu k datům v zařízení.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Ujistěte se, že jsou nakonfigurovány správné seznamy ACL pro omezení neoprávněného přístupu k datům v zařízení.|

## <a id="sensitive-directory"></a>Zajistěte, aby byl v adresáři User-Profile uložený obsah aplikace citlivý pro uživatele.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Zajistěte, aby byl v adresáři uživatelských profilů uložený obsah aplikace citlivý pro uživatele. K tomu je potřeba zabránit více uživatelům počítače v přístupu k ostatním datům.|

## <a id="deployed-privileges"></a>Zajistěte, aby byly nasazené aplikace spuštěné s nejnižšími oprávněními.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Zajistěte, aby byla nasazená aplikace spuštěná s nejnižšími oprávněními. |

## <a id="sequential-logic"></a>Vymáhat pořadí sekvenčních kroků při zpracování toků obchodních logiky

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Aby bylo možné ověřit, že byla tato fáze spuštěna pomocí originálního uživatele, který chcete aplikaci vynutilit, aby zpracovala toky obchodních logiky v sekvenčním pořadí, se všemi kroky zpracovávanými v reálných případech, které jsou zpracovávány v reálném čase, přeskočenými kroky , zpracování kroků od jiného uživatele nebo příliš rychle odeslaných transakcí.|

## <a id="rate-enumeration"></a>Implementujte mechanismus omezování četnosti, aby se zabránilo výčtu.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Zajistěte, aby byly citlivé identifikátory náhodné. Implementuje ovládací prvek CAPTCHA na anonymních stránkách. Zajistěte, aby chyba a výjimka neměly odhalit konkrétní data|

## <a id="principle-least-privilege"></a>Zajistěte, aby byla zajištěna správná autorizace a následovala zásada minimálního oprávnění.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | <p>Princip poskytuje uživatelský účet pouze ta oprávnění, která jsou pro tyto uživatele zásadní. Například uživatel pro zálohování nemusí instalovat software: proto má uživatel pro zálohování oprávnění pouze ke spouštění aplikací souvisejících se zálohováním a zálohováním. Všechna další oprávnění, jako je instalace nového softwaru, jsou blokovaná. Princip platí také pro uživatele osobního počítače, který obvykle pracuje v normálním uživatelském účtu a otevírá privilegovaný účet chráněný heslem (tj. uživatel) pouze v případě, že je přesně vydává. </p><p>Tato zásada se dá použít i pro vaše webové aplikace. Místo výhradně v závislosti na metodách ověřování založených na rolích pomocí relací chceme k uživatelům přiřadit oprávnění prostřednictvím databázového ověřovacího systému. Pořád používáme relace, abychom zjistili, jestli byl uživatel přihlášený správně, jenom teď namísto přiřazení tohoto uživatele k určité roli, kterou mu přiřadíme, aby ověřil, které akce má k systému oprávnění provést. Tato metoda je také velká pro tuto metodu, kdykoli se uživateli přiřadí méně oprávnění, která se použijí, protože přiřazení nezávisí na relaci, která jinak musela nejdřív vypršet.</p>|

## <a id="logic-request-parameters"></a>Obchodní logika a rozhodnutí o autorizaci přístupu k prostředkům by neměly být založené na parametrech příchozích požadavků.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Kdykoli kontrolujete, jestli je uživatel omezený, aby zkontroloval určitá data, měla by být omezení přístupu zpracovaná na straně serveru. Identifikátor userID by měl být uložený v proměnné relace při přihlášení a měl by se použít k načtení uživatelských dat z databáze. |

### <a name="example"></a>Příklad
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nyní možný Útočník nemůže manipulovat a měnit operaci aplikace, protože identifikátor pro načítání dat je zpracován na straně serveru.

## <a id="enumerable-browsing"></a>Ujistěte se, že obsah a prostředky nejsou vyčíslitelné nebo přístupné prostřednictvím vynuceného procházení.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | <p>Citlivé statické a konfigurační soubory by neměly být uchovávány v kořenovém adresáři webu. Pro obsah, který není nezbytný k veřejnému, by se měly použít buď správné ovládací prvky pro přístup, nebo odebrat samotný obsah.</p><p>Vynucené procházení je obvykle kombinované s technikami hrubou silou pro shromažďování informací pomocí pokusu o přístup k tolika adresám URL, aby bylo možné vytvořit výčet adresářů a souborů na serveru. Útočníci můžou kontrolovat všechny varianty často existujících souborů. Například hledání souborů hesla by zahrnovalo soubory, včetně psswd. txt, Password. htm, Password. dat a dalších variací.</p><p>Chcete-li tyto možnosti zmírnit, je třeba zahrnout funkce pro detekci pokusů o útok hrubou silou.</p>|

## <a id="privileged-server"></a>Zajistěte, aby se pro připojení k databázovému serveru používaly minimálně privilegované účty.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [SQL Database hierarchie oprávnění](https://msdn.microsoft.com/library/ms191465), [zabezpečit databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Pro připojení k databázi by se měly použít aspoň privilegované účty. Přihlášení aplikace by mělo být omezeno v databázi a mělo by se provádět pouze vybrané uložené procedury. Přihlašovací jméno aplikace by nemělo mít přímý přístup k tabulce. |

## <a id="rls-tenants"></a>Implementujte zabezpečení na úrovni řádků, abyste klientům zabránili v přístupu k ostatním datům.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Sql Azure, OnPrem |
| **Atributy**              | SQL verze – V12, verze SQL – MsSQL2016 |
| **Odkazy**              | [SQL Server zabezpečení na úrovni řádků (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroky** | <p>Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění).</p><p>Zabezpečení na úrovni řádků (RLS) zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pro zajištění, že pracovníci mají přístup pouze k datovým řádkům, které se vztahují k jejich oddělení, nebo pro omezení přístupu zákazníků pouze k datům souvisejícím s jejich společností.</p><p>Logika omezení přístupu se nachází v databázové vrstvě, nikoli z dat v jiné aplikační vrstvě. Databázový systém použije omezení přístupu při každém pokusu o přístup k datům z libovolné úrovně. Tím se zajistí spolehlivější a robustní zabezpečení systému tím, že se zmenší plocha plochy systému zabezpečení.</p><p>|

Mějte prosím na paměti, že funkce RLS jako připravená databáze je platná jenom pro SQL Server od 2016 do Azure SQL Database. Pokud není implementovaná funkce samoobslužná aplikace, měla by být zajištěna omezení přístupu k datům pomocí zobrazení a postupů.

## <a id="sysadmin-users"></a>Role sysadmin by měla mít pouze platné potřebné uživatele.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [SQL Database hierarchie oprávnění](https://msdn.microsoft.com/library/ms191465), [zabezpečit databáze SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroky** | Členové pevné role serveru SysAdmin by měli být velmi omezené a nikdy neobsahují účty používané aplikacemi.  Zkontrolujte seznam uživatelů v roli a odeberte všechny nepotřebné účty.|

## <a id="cloud-least-privileged"></a>Připojení ke cloudové bráně s použitím nejnižších privilegovaných tokenů

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | IoT Cloud Gateway | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Odkazy**              | [Access Control IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroky** | Poskytněte minimální oprávnění pro různé komponenty, které se připojují ke cloudové bráně (IoT Hub). Typickým příkladem je – služba Device Management/zřizování používá registryread/Write. procesor událostí (ASA) používá službu Connect. Jednotlivá zařízení se připojují pomocí přihlašovacích údajů zařízení.|

## <a id="sendonly-sas"></a>Pro generování tokenů zařízení použít jenom klíč SAS s oprávněním pro odesílání

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Přehled ověřování a modelu zabezpečení Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Klíč SAS se používá ke generování jednotlivých tokenů zařízení. Při generování tokenu zařízení pro daného vydavatele používejte oprávnění SAS jenom pro odesílání.|

## <a id="access-tokens-hub"></a>Nepoužívejte přístupové tokeny, které poskytují přímý přístup k centru událostí.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Přehled ověřování a modelu zabezpečení Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Token, který uděluje přímý přístup do centra událostí, by neměl být předaný zařízení. Použití s nejnižším privilegovaným tokenem pro zařízení, které poskytuje přístup pouze vydavateli, může pomoci identifikovat a nepoužívat, pokud se zjistí jako neautorizovaný nebo ohrožené zařízení.|

## <a id="sas-minimum-permissions"></a>Připojte se k centru událostí pomocí klíčů SAS, které mají minimální požadovaná oprávnění.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Přehled ověřování a modelu zabezpečení Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Poskytněte minimální oprávnění pro různé back-endové aplikace, které se připojují k centru událostí. Vygenerujte samostatné klíče SAS pro každou back-end aplikaci a poskytněte jim pouze požadovaná oprávnění – Odeslat, přijmout nebo spravovat.|

## <a id="resource-docdb"></a>Pokud je to možné, použijte k připojení Cosmos DB tokeny prostředků.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Document DB | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Token prostředku je přidružen k prostředku oprávnění Azure Cosmos DB a zachycuje vztah mezi uživatelem databáze a oprávněním, které má uživatel pro určitý prostředek Azure Cosmos DB aplikace (např. kolekce, dokument). Pro přístup k Azure Cosmos DB vždy použít token prostředku, pokud klienta nemůžete důvěřovat pomocí řídicího panelu nebo klíčů jen pro čtení, jako je aplikace koncového uživatele, jako je například mobilní klient nebo stolní počítač. Použijte hlavní klíč nebo klíče jen pro čtení z back-endu aplikací, které můžou tyto klíče bezpečně uložit.|

## <a id="grained-rbac"></a>Povolení jemně odstupňovaného řízení přístupu k předplatnému Azure pomocí RBAC

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěry Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroky** | Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh.|

## <a id="cluster-rbac"></a>Omezení přístupu klienta k operacím clusteru pomocí RBAC

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Řízení přístupu na základě role pro klienty Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroky** | <p>Azure Service Fabric podporuje pro klienty, kteří jsou připojení ke clusteru Service Fabric, dva různé typy řízení přístupu: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým clusterovým operacím pro různé skupiny uživatelů a tím zvýšit zabezpečení clusteru.</p><p>Správci mají plný přístup k funkcím správy (včetně funkcí pro čtení a zápis). Uživatelé mají ve výchozím nastavení přístup jen pro čtení k funkcím pro správu (například možnosti dotazů) a možnost přeložit aplikace a služby.</p><p>V době vytváření clusteru určíte dvě role klienta (správce a klient), a to tak, že pro každý z nich poskytnete samostatné certifikáty.</p>|

## <a id="modeling-field"></a>V případě potřeby proveďte modelování zabezpečení a použijte zabezpečení na úrovni polí.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | V případě potřeby proveďte modelování zabezpečení a použijte zabezpečení na úrovni polí.|

## <a id="portal-security"></a>Provedete si modelování zabezpečení účtů portálu a mějte na paměti, že se model zabezpečení portálu liší od zbytku CRM.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Portál Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Provedete si modelování zabezpečení účtů portálu a mějte na paměti, že se model zabezpečení portálu liší od zbytku CRM.|

## <a id="permission-entities"></a>Udělení jemně odstupňovaného oprávnění pro řadu entit v Azure Table Storage

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – tabulka |
| **Odkazy**              | [Jak delegovat přístup k objektům ve vašem účtu Azure Storage pomocí SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroky** | V některých obchodních scénářích může být pro Azure Table Storage nutné ukládat citlivá data, která jsou v různých stranách. Například citlivá data týkající se různých zemí nebo oblastí. V takových případech je možné signatury SAS vytvořit zadáním rozsahu klíče a rozsahů klíčů řádků, aby uživatel měl přístup k datům, která jsou specifická pro určitou zemi nebo oblast.| 

## <a id="rbac-azure-manager"></a>Povolení Access Control na základě rolí (RBAC) na účet služby Azure Storage pomocí Azure Resource Manager

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Jak zabezpečit svůj účet úložiště pomocí Access Control na základě rolí (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroky** | <p>Když vytváříte nový účet úložiště, vyberete model nasazení Classic nebo Azure Resource Manager. Klasický model vytváření prostředků v Azure umožňuje jenom přístup k předplatnému a k tomuto předplatnému a zároveň účet úložiště.</p><p>Pomocí modelu Azure Resource Manager umístíte účet úložiště do skupiny prostředků a řídíte přístup k rovině správy tohoto konkrétního účtu úložiště pomocí Azure Active Directory. Můžete například udělit konkrétním uživatelům přístup k klíčům účtu úložiště, zatímco jiní uživatelé mohou zobrazit informace o účtu úložiště, ale nemají přístup k klíčům účtu úložiště.</p>|

## <a id="rooting-detection"></a>Implementovat implicitní jailbreaků nebo detekci kořene

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | <p>Aplikace by měla chránit vlastní konfiguraci a uživatelská data v případě, že telefon je rootem nebo Jailbreak. Zajailbreakm nebo přerušením implikuje neoprávněný přístup, které normální uživatelé nedělají na svých vlastních telefonech. Proto by měla aplikace mít implicitní detekci detekce při spuštění aplikace, aby zjistila, zda byl telefon rootem.</p><p>Logika detekce může jednoduše přistupovat k souborům, které obvykle mají přístup root user, například:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Pokud aplikace má přístup k některým z těchto souborů, označuje, že aplikace je spuštěna jako uživatel root.</p>|

## <a id="weak-class-wcf"></a>Slabý odkaz na třídu ve WCF

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroky** | <p>Systém používá slabý odkaz na třídu, který může útočníkovi umožnit spustit neautorizovaný kód. Program odkazuje na uživatelsky definovanou třídu, která není jednoznačně identifikována. Když rozhraní .NET načte tuto slabě identifikovanou třídu, vyhledá zavaděč typu CLR třídu v následujících umístěních v zadaném pořadí:</p><ol><li>Pokud je známo sestavení typu, zavaděč vyhledá umístění pro přesměrování konfiguračního souboru, GAC, aktuální sestavení s použitím informací o konfiguraci a základního adresáře aplikace.</li><li>Pokud je sestavení neznámé, zavaděč vyhledá aktuální sestavení, mscorlib a umístění, které vrátila obslužná rutina události TypeResolve.</li><li>Toto pořadí vyhledávání CLR se dá upravit pomocí háčků, jako je mechanismus předávání typů a událost AppDomain. TypeResolve.</li></ol><p>Pokud útočník zneužije pořadí vyhledávání CLR vytvořením alternativní třídy se stejným názvem a umístěním do alternativního umístění, které modul CLR načte jako první, modul CLR neúmyslně neprovede kód poskytovaný útočníkem.</p>|

### <a name="example"></a>Příklad
`<behaviorExtensions/>` Element konfiguračního souboru služby WCF vydá pokyn pro WCF, aby přidal vlastní třídu chování do konkrétního rozšíření WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Použití plně kvalifikovaných (silných) názvů jednoznačně identifikuje typ a zvyšuje zabezpečení systému. Při registraci typů v souborech Machine. config a App. config použijte plně kvalifikované názvy sestavení.

### <a name="example"></a>Příklad
`<behaviorExtensions/>` Element konfiguračního souboru služby WCF vydá pokyn WCF pro přidání silně odkazované třídy vlastního chování do konkrétního rozšíření WCF.
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

## <a id="wcf-authz"></a>WCF – implementace autorizačního řízení

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
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

## <a id="authz-aspnet"></a>Implementace správného autorizačního mechanismu ve webovém rozhraní API ASP.NET

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, MVC5 |
| **Atributy**              | N/A, zprostředkovatel identity – ADFS, zprostředkovatel identity – Azure AD |
| **Odkazy**              | [Ověřování a autorizace ve webovém rozhraní API ASP.NET](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
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

## <a id="device-permission"></a>Provádět kontroly autorizace v zařízení, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | <p>Zařízení by mělo autorizovat volajícího, aby zkontroloval, jestli má volající potřebná oprávnění k provedení požadované akce. Například umožňuje vyslovit, že zařízení je zámek inteligentních dveří, který se dá monitorovat z cloudu, a poskytuje funkce, jako je vzdálené blokování dveří.</p><p>Zámek inteligentních dvířek poskytuje možnost odemykání funkcí jenom v případě, že se někdo fyzicky nachází poblíž dveří s kartou. V takovém případě by implementace vzdáleného příkazu a ovládacího prvku měla být provedena takovým způsobem, že neposkytuje žádné funkce pro odemknutí dveří, protože cloudová brána nemá autorizaci k odeslání příkazu k odemknutí dveří.</p>|

## <a id="field-permission"></a>Provádět kontroly autorizace v bráně pole, pokud podporuje různé akce, které vyžadují různé úrovně oprávnění.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Brána Field by měla autorizovat volajícího, aby zkontroloval, jestli má volající potřebná oprávnění k provedení požadované akce. Například by měla existovat různá oprávnění pro uživatelské rozhraní nebo rozhraní API pro správu, které slouží ke konfiguraci zařízení s bránou pole v/s, která se k němu připojují.|
