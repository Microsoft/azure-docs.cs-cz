---
title: 'Synchronizace Azure AD Connect: prevence náhodného odstranění | Microsoft Docs'
description: Toto téma popisuje funkci prevence náhodného odstranění (prevence náhodného odstranění) ve službě Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ecec08dcfa251b94ccfd09e87a8499dc03164ea9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595066"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Synchronizace Azure AD Connect: Prevence náhodného odstranění
Toto téma popisuje funkci prevence náhodného odstranění (prevence náhodného odstranění) ve službě Azure AD Connect.

Při instalaci Azure AD Connect, zabránit náhodnému odstranění je ve výchozím nastavení povolené a nakonfigurované tak, aby nepovoloval exportu s více než 500 odstranění. Tato funkce slouží k ochraně před náhodným konfigurace změny a změny do vašeho místního adresáře, které má vliv na mnoho uživatelů a dalších objektů.

## <a name="what-is-prevent-accidental-deletes"></a>Co je prevence náhodného odstranění
Běžné scénáře až uvidíte mnoho odstranění patří:

* Změny [filtrování](active-directory-aadconnectsync-configure-filtering.md) kde celou [OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) nebo [domény](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) nezaškrtnuté.
* Všechny objekty v organizační jednotce se odstraní.
* Organizační jednotka se přejmenuje tak všechny objekty v ní se považují za mimo rozsah pro synchronizaci.

Výchozí hodnota 500 objektů lze změnit pomocí prostředí PowerShell pomocí `Enable-ADSyncExportDeletionThreshold`, který je součástí modulu AD Sync nainstalovat s Azure Active Directory Connect. Měli byste nakonfigurovat tuto hodnotu, aby odpovídal zadané velikosti vaší organizace. Vzhledem k tomu, že plánovače synchronizace spouští každých 30 minut, je hodnota číslo z odstranění vidět do 30 minut.

Pokud jsou moc odstranění připravený být exportovány do služby Azure AD, poté se zastaví exportu a obdržíte e-mail takto:

![Zabránit náhodného odstranění e-mailu](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hello (technický kontakt). V (čas) synchronizační služba Identity zjistil, že počet odstranění překročena prahová hodnota odstranění nakonfigurované pro (název organizace). Pro odstranění v této synchronizace identit spustit byly odeslány z (number) Celkový počet objektů. To splňuje nebo překračuje prahovou hodnotu nakonfigurované odstranění objektů (number). Potřebujeme, abyste k poskytování potvrzení, že tyto odstraněním měla by být zpracována před jsme bude pokračovat. Podrobnosti viz zabránilo náhodným odstraněním další informace o chybě uvedených v této e-mailové zprávy.*
>
> 

Stav můžete také zobrazit `stopped-deletion-threshold-exceeded` když se podíváte **Synchronization Service Manager** uživatelského rozhraní pro Export profil.
![Prevence náhodného odstranění uživatelského rozhraní Správce služby synchronizace](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Pokud jste to neočekávané, prozkoumejte a provést opravné akce. Pokud chcete zobrazit, které objekty se mají odstranit, postupujte takto:

1. Spustit **synchronizační služba** z nabídky Start.
2. Přejděte na **konektory**.
3. Vyberte konektor s typem **Azure Active Directory**.
4. V části **akce** napravo, vyberte **hledání prostoru konektoru**.
5. V místní nabídce v části **oboru**, vyberte **odpojen od** a vyberte čas v minulosti. Klikněte na tlačítko **vyhledávání**. Tato stránka obsahuje zobrazení všech objektů, které má být odstraněn. Kliknutím na jednotlivé položky, můžete získat další informace o objektu. Můžete také kliknout na **sloupec nastavení** přidat další atributy, které mají být zobrazeny v mřížce.

![Hledání prostoru konektoru](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

V případě potřeby všechna odstranění se potom postupujte takto:

1. Chcete-li získat aktuální prahová hodnota odstranění, spusťte rutinu prostředí PowerShell `Get-ADSyncExportDeletionThreshold`. Zadejte účet Azure AD globálního správce a heslo. Výchozí hodnota je 500.
2. K dočasnému zakázání této ochrany a nechat tyto odstranění projít, spusťte rutinu prostředí PowerShell: `Disable-ADSyncExportDeletionThreshold`. Zadejte účet Azure AD globálního správce a heslo.
   ![Přihlašovací údaje](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Pomocí Azure konektor služby Active Directory stále vybrána, vyberte akci, která **spustit** a vyberte **exportovat**.
4. Chcete-li znovu povolit ochranu, spusťte rutinu prostředí PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Nahraďte hodnotu, kterou jste si všimli při načítání aktuální prahová hodnota odstranění 500. Zadejte účet Azure AD globálního správce a heslo.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
