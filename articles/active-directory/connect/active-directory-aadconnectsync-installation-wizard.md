---
title: Znovu spustit Azure AD Connect Průvodce instalací | Microsoft Docs
description: Vysvětluje, jak funguje Průvodce instalací druhá, když jej spustíte.
keywords: Průvodce instalací služby Azure AD Connect vám umožní nakonfigurovat nastavení údržby druhý čas spuštění
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a33301904cff9a5a396eb6f641b7e3bc6b3e9801
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592530"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronizace Azure AD Connect: opětovným spuštěním Průvodce instalací
Při prvním spuštění Průvodce instalací služby Azure AD Connect, se vás provede procesem konfigurace instalace. Pokud znovu spustíte Průvodce instalací, nabízí možnosti pro údržbu.

Průvodce instalací můžete najít v nabídce start s názvem **Azure AD Connect**.

![Nabídka Start](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Když spustíte Průvodce instalací, zobrazí se stránka pomocí těchto možností:

![Stránka s seznam dalších úloh](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Pokud jste nainstalovali služby AD FS službou Azure AD Connect, máte i další možnosti. Další možnosti máte služby AD FS jsou dokumentovány v článku [správu služby AD FS](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Vyberte jednu z úloh a klikněte na **Další** pokračujte.

> [!IMPORTANT]
> Při otevření Průvodce instalací, jsou všechny operace v synchronizační modul pozastavit. Ujistěte se, že jakmile dokončíte změny konfigurace se zavřete průvodce instalací.
>
>

## <a name="view-current-configuration"></a>Zobrazit aktuální konfiguraci
Tato možnost poskytuje rychlý přehled o aktuálně nakonfigurované možnosti.

![Stránka se seznam všech možností a jejich stavu](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Klikněte na tlačítko **předchozí** a vraťte se. Pokud vyberete **ukončení**, zavřete průvodce instalací.

## <a name="customize-synchronization-options"></a>Přizpůsobit možnosti synchronizace
Tato možnost slouží k provádět změny konfigurace synchronizace. Zobrazí podmnožinu možnosti z cesty instalace vlastní konfigurace. Tato možnost zobrazí i v případě začátku použít rychlé instalace.

* [Další adresáře přidat](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Odebírání adresáře, najdete v části [odstranit konektor](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Změnit doménu a organizační jednotku filtrování](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Odstranit skupinu filtrování.
* [Změnit volitelné funkce](active-directory-aadconnect-get-started-custom.md#optional-features).

Další možnosti z počáteční instalaci nelze změnit a nejsou k dispozici. Tyto možnosti jsou:

* Změňte atribut, který chcete použít pro userPrincipalName a sourceAnchor.
* Změňte metodu spojující pro objekty z jiné doménové struktuře.
* Povolte filtrování podle skupiny.

## <a name="refresh-directory-schema"></a>Aktualizovat schéma adresáře
Tato možnost se používá, pokud jste změnili schéma v jednom z místními doménovými strukturami služby AD DS. Například je může Exchange nainstalovali nebo upgradovali schématu systému Windows Server 2012 s objekty zařízení. V takovém případě budete muset pokyn Azure AD Connect číst schéma znovu ze služby AD DS a aktualizovat své mezipaměti. Tato akce také regeneruje pravidla synchronizace. Pokud přidáte schéma Exchange jako příklad, pravidla synchronizace pro Exchange se přidají do konfigurace.

Když vyberete tuto možnost, jsou uvedeny všechny adresáře ve vaší konfiguraci. Můžete ponechat výchozí nastavení a aktualizujte všechny doménové struktury nebo zrušení výběru některé z nich.

![Stránka s seznamu všech adresářů v prostředí](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurovat pracovní režim
Tato možnost umožňuje povolit nebo zakázat pracovní režim na serveru. Další informace o pracovním režimu a způsobu jejich použití naleznete v [operace](active-directory-aadconnectsync-operations.md#staging-mode).

Možnost ukazuje, zda je aktuálně povolena nebo zakázána pracovní:  
![Možnost, která se také zobrazuje aktuální stav pracovní režim](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Na změnu stavu, vyberte tuto možnost a vyberte nebo zrušte výběr zaškrtávací políčko.  
![Možnost, která se také zobrazuje aktuální stav pracovní režim](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Změna přihlašovacích údajů uživatele
Tato možnost umožňuje změnit metoda přihlašování uživatele do a z synchronizace hodnot hash hesel, předávací ověřování nebo federace. Nelze změnit na **nekonfigurujte**.

Další informace o této možnosti najdete v tématu [přihlášení uživatele](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu používá synchronizaci Azure AD Connect v [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
