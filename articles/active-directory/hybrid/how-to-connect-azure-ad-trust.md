---
title: Azure AD Connect – Správa AD FS důvěry pomocí služby Azure AD pomocí Azure AD Connect | Microsoft Docs
description: Provozní podrobnosti o zpracování vztahů důvěryhodnosti Azure AD pomocí služby Azure AD Connect.
keywords: AD FS, ADFS, Správa AD FS, AAD Connect, Connect, Azure AD, Trust, AAD, deklarace identity, pravidla deklarací identity, vystavení, transformace, pravidla, zálohování, obnovení
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360091"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Správa vztahu důvěryhodnosti AD FS s Azure AD s využitím Azure AD Connectu

## <a name="overview"></a>Přehled

Azure AD Connect může spravovat federaci mezi místními službami Active Directory služba FS (Federation Service) (AD FS) a službou Azure AD. Tento článek poskytuje přehled:

* Různá nastavení nakonfigurovaná u vztahu důvěryhodnosti nástrojem Azure AD Connect
* Pravidla transformace vystavení (pravidla deklarace identity) nastavená Azure AD Connect
* Jak zálohovat a obnovovat pravidla deklarací mezi upgrady a aktualizacemi konfigurace. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Nastavení řízená pomocí Azure AD Connect

Azure AD Connect spravuje **jenom** nastavení související se vztahem důvěryhodnosti služby Azure AD. Azure AD Connect nemění žádná nastavení v jiných vztazích důvěryhodnosti předávající strany v AD FS. V následující tabulce jsou uvedena nastavení, která jsou ovládána nástrojem Azure AD Connect.

| Nastavení | Popis |
| :--- | :--- |
| Podpisový certifikát tokenu | K resetování a opětovnému vytvoření vztahu důvěryhodnosti s Azure AD se dá použít Azure AD Connect. Azure AD Connect je jednorázová okamžitá Výměna certifikátů podepisování tokenů pro AD FS a aktualizuje nastavení federace domény Azure AD.|
| Podpisový algoritmus tokenu | Microsoft doporučuje jako algoritmus podepisování tokenů používat SHA-256. Azure AD Connect může zjistit, jestli je podpisový algoritmus tokenu nastavený na méně bezpečnou hodnotu než SHA-256. Nastavení bude v další možné konfigurační operaci aktualizovat na SHA-256. Aby bylo možné použít nový podpisový certifikát tokenu, je nutné aktualizovat jiný vztah důvěryhodnosti předávající strany. |
| Identifikátor vztahu důvěryhodnosti Azure AD | Azure AD Connect nastaví správnou hodnotu identifikátoru pro vztah důvěryhodnosti služby Azure AD. AD FS jedinečně identifikuje vztah důvěryhodnosti služby Azure AD pomocí hodnoty identifikátoru. |
| Koncové body služby Azure AD | Azure AD Connect zajistí, že koncové body nakonfigurované pro vztah důvěryhodnosti služby Azure AD jsou vždycky podle nejnovějších doporučených hodnot pro odolnost a výkon. |
| Pravidla transformace vystavení | Existují počty pravidel deklarací identity, které jsou potřebné k zajištění optimálního výkonu funkcí služby Azure AD ve federovaném nastavení. Azure AD Connect zajistí, že vztah důvěryhodnosti služby Azure AD je vždycky nakonfigurovaný se správnou sadou doporučených pravidel deklarací identity. |
| Alternativní ID | Pokud je synchronizace nakonfigurovaná tak, aby používala alternativní ID, Azure AD Connect nakonfiguruje AD FS, aby se provádělo ověřování pomocí alternativního ID. |
| Automatická aktualizace metadat | Vztah důvěryhodnosti se službou Azure AD je nakonfigurovaný pro automatickou aktualizaci metadat. AD FS pravidelně kontroluje metadata vztahu důvěryhodnosti služby Azure AD a udržuje je v aktuálním stavu pro případ, že se změní na straně Azure AD. |
| Integrované ověřování systému Windows (IWA) | Během operace připojení k hybridní službě Azure AD je IWA povolená pro registraci zařízení, aby se usnadnilo hybridní připojení ke službě Azure AD pro zařízení se starší verzí. |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Toky spuštění a nastavení federace nakonfigurované pomocí Azure AD Connect

Služba Azure AD Connect během konfiguračních toků neaktualizuje všechna nastavení vztahu důvěryhodnosti služby Azure AD. Změněná nastavení závisí na tom, který úkol nebo tok spouštění je právě prováděn. V následující tabulce jsou uvedena nastavení ovlivněná v různých tocích spuštění.

| Tok spuštění | Ovlivněná nastavení |
| :--- | :--- |
| První předání instalace (Express) | Žádné |
| První předání instalace (nová AD FS farma) | Vytvoří se nová farma AD FS a od nuly se vytvoří vztah důvěryhodnosti se službou Azure AD. |
| První předání instalace (existující AD FS farma, existující vztah důvěryhodnosti Azure AD) | Identifikátor vztahu důvěryhodnosti Azure AD, pravidla transformace vystavování, koncových bodů Azure AD, alternativní ID (Pokud je potřeba), Automatická aktualizace metadat |
| Resetovat vztah důvěryhodnosti služby Azure AD | Podpisový certifikát tokenu, podpisový algoritmus tokenu, identifikátor vztahu důvěryhodnosti Azure AD, pravidla transformace pro vystavování, koncové body Azure AD, alternativní ID (v případě potřeby) Automatická aktualizace metadat |
| Přidat federační server | Žádné |
| Přidat server WAP | Žádné |
| Možnosti zařízení | Pravidla transformace pro vystavování IWA pro registraci zařízení |
| Přidat federované domény | Pokud se doména přidávají poprvé, znamená to, že se instalace mění z doménové federace na více doménových federace – Azure AD Connect znovu vytvoří vztah důvěryhodnosti od začátku. Pokud je vztah důvěryhodnosti s Azure AD už nakonfigurovaný pro víc domén, upraví se jenom pravidla transformace vystavování. |
| Aktualizace TLS | Žádné |

Při všech operacích, ve kterých se změní jakékoli nastavení, Azure AD Connect vytvoří zálohu aktuálních nastavení vztahu důvěryhodnosti na **%ProgramData%\AADConnect\ADFS**

![Stránka Azure AD Connect zobrazující zprávu o existujícím zálohování vztahu důvěryhodnosti Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Před verzí 1.1.873.0 se záloha skládá jenom z pravidel transformace vystavování a byla zálohována v souboru protokolu trasování průvodce.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Vystavování pravidel transformace nastavených pomocí Azure AD Connect

Azure AD Connect zajistí, že vztah důvěryhodnosti služby Azure AD je vždycky nakonfigurovaný se správnou sadou doporučených pravidel deklarací identity. Microsoft doporučuje používat Azure AD Connect pro správu vztahu důvěryhodnosti Azure AD. V této části jsou uvedená sada pravidel transformace pro vystavování a jejich popis.

| Název pravidla | Popis |
| --- | --- |
| Vydávat hlavní název uživatele | Toto pravidlo vyhledá hodnotu userPrincipalName jako z atributu nakonfigurovaného v nastavení synchronizace pro userPrincipalName.|
| Dotaz na objectGUID a msdsconsistencyguid pro vlastní ImmutableId deklaraci identity | Toto pravidlo přidá do kanálu dočasnou hodnotu pro identifikátor objectGUID a hodnotu msdsconsistencyguid, pokud existuje. |
| Kontrolovat existenci msdsconsistencyguid | Na základě toho, jestli hodnota pro msdsconsistencyguid existuje nebo ne, nastavíme dočasný příznak, který se má použít jako ImmutableId. |
| Vystavení msdsconsistencyguid jako neměnný identifikátor, pokud existuje | Vystaví msdsconsistencyguid jako ImmutableId, pokud existuje hodnota. |
| Vystavení objectGuidRule, pokud pravidlo msdsConsistencyGuid neexistuje | Pokud hodnota pro msdsconsistencyguid neexistuje, hodnota objectGUID se vydá jako ImmutableId. |
| Problém NameIdentifier | Toto pravidlo vystavuje hodnotu pro NameIdentifier deklarací identity.|
| Vystavení AccountType počítačům připojeným k doméně | Pokud je entita ověřované zařízením připojené k doméně, toto pravidlo vystavuje typ účtu jako přehrávač DJ, který označuje zařízení připojené k doméně. |
| Vystavte si AccountType s hodnotou uživatel, když se nejedná o účet počítače. | Pokud je entita ověřované uživatelem, toto pravidlo vydá typ účtu jako uživatel. |
| Vystavení issuerid, pokud se nejedná o účet počítače | Toto pravidlo vystavuje hodnotu issuerId, pokud ověřovací entita není zařízení. Hodnota je vytvořena pomocí regulárního výrazu, který je nakonfigurován pomocí Azure AD Connect. Regulární výraz se vytvoří po zvážení všech domén federované pomocí Azure AD Connect. |
| Vystavení issuerid pro ověřování v počítačích DJ | Toto pravidlo vystavuje hodnotu issuerId, pokud je ověřovací entita zařízení. |
| Vystavení onpremobjectguid počítačům připojeným k doméně | Pokud je subjektem, který se ověřuje, zařízení připojené k doméně, vystavuje toto pravidlo místní objectGUID zařízení. |
| Předat primární identifikátor SID | Toto pravidlo vydá primární identifikátor SID ověřovací entity. |
| Předávací deklarace – insideCorporateNetwork | Toto pravidlo vystavuje deklaraci identity, která pomáhá službě Azure AD zjistit, jestli ověřování pochází z podnikové sítě nebo externě. |
| Předávací deklarace – PSSO |   |
| Vydávat deklarace identity vypršení platnosti hesla | Toto pravidlo vystavuje tři deklarace identity pro dobu vypršení platnosti hesla, počet dní, po které platnost hesla vyprší v ověřované entitě, a adresu URL, kam se má směrovat Změna hesla.|
| Předávací deklarace – authnmethodsreferences | Hodnota v deklaraci identity vydaná na základě tohoto pravidla indikuje typ ověřování, který se má u entity provést. |
| Předávací deklarace – multifactorauthenticationinstant | Hodnota této deklarace identity určuje čas ve standardu UTC, kdy uživatel naposledy provedl ověřování s více faktory. |
| Předávací deklarace – AlternateLoginID | Toto pravidlo vystavuje deklaraci identity AlternateLoginID, pokud bylo ověřování provedeno pomocí alternativního přihlašovacího ID. |

> [!NOTE]
> Pravidla deklarace identity pro hlavní název uživatele (UPN) a ImmutableId se budou lišit, pokud při konfiguraci Azure AD Connect použijete jinou než výchozí možnost.

## <a name="restore-issuance-transform-rules"></a>Obnovit pravidla transformace vystavení

Azure AD Connect verze 1.1.873.0 nebo novější vytvoří zálohu nastavení vztahu důvěryhodnosti služby Azure AD vždy, když se provede aktualizace nastavení vztahu důvěryhodnosti služby Azure AD. Nastavení vztahu důvěryhodnosti Azure AD se zálohují na **%ProgramData%\AADConnect\ADFS**. Název souboru je v následujícím formátu: AadTrust- &lt; Date &gt; - &lt; Time &gt; . txt, například AadTrust-20180710-150216.txt

![Snímek obrazovky s příkladem zálohování vztahu důvěryhodnosti Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Pravidla transformace vystavování můžete obnovit pomocí navrhovaných kroků níže.

1. Otevřete uživatelské rozhraní pro správu AD FS v Správce serveru
2. Otevřete vlastnosti vztahu důvěryhodnosti Azure AD tak, že přecházíte **AD FS &gt; vztahy důvěryhodnosti předávající strany &gt; systém Microsoft Office 365 Identity Platform &gt; upravit zásady vystavování deklarací identity** .
3. Klikněte na **Přidat pravidlo** .
4. V šabloně pravidla deklarace identity vyberte Odeslat deklarace identity pomocí vlastního pravidla a klikněte na **Další** .
5. Zkopírujte název pravidla deklarace identity ze záložního souboru a vložte ho do pole **název pravidla deklarace identity** .
6. Zkopírujte pravidlo deklarace identity ze záložního souboru do textového pole pro **vlastní pravidlo** a klikněte na **Dokončit** .

> [!NOTE]
> Ujistěte se, že vaše další pravidla nejsou v konfliktu s pravidly nakonfigurovanými nástrojem Azure AD Connect.

## <a name="next-steps"></a>Další kroky
* [Správa a přizpůsobení Active Directory Federation Services (AD FS) pomocí Azure AD Connect](how-to-connect-fed-management.md)
