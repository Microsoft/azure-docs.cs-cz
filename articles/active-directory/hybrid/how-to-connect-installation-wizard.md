---
title: Azure AD Connect opětovným spuštěním Průvodce instalací | Dokumentace Microsoftu
description: Vysvětluje, jak funguje Průvodce instalací druhý čas spuštění.
keywords: Průvodce instalací Azure AD Connect vám umožní nakonfigurovat nastavení údržby druhém spuštění
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
ms.openlocfilehash: c5f5f59329e8f4e34a4c08a5819d0e4d96fe2f5c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315277"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronizace Azure AD Connect: spuštění Průvodce instalací podruhé
Při prvním spuštění Průvodce instalací služby Azure AD Connect to vás provede instalaci nakonfigurujte. Pokud znovu spustíte Průvodce instalací, nabízí možnosti pro údržbu.

Průvodce instalací najdete v nabídce start s názvem **Azure AD Connect**.

![Nabídka Start](./media/how-to-connect-installation-wizard/startmenu.png)

Když spustíte Průvodce instalací, zobrazí se stránka s těmito možnostmi:

![Stránka se seznamem další úkoly](./media/how-to-connect-installation-wizard/additionaltasks.png)

Pokud jste nainstalovali služby AD FS s Azure AD Connect, máte i další možnosti. Další možnosti máte pro služby AD FS jsou dokumentovány v článku [správu služby AD FS](how-to-connect-fed-management.md#manage-ad-fs).

Vyberte jednu z úloh a klikněte na tlačítko **Další** pokračujte.

> [!IMPORTANT]
> Při otevření Průvodce instalací, všechny operace v modulu synchronizace jsou pozastavena. Zajistěte, aby že co nejdříve dokončili změny konfigurace zavřete průvodce instalací.
>
>

## <a name="view-current-configuration"></a>Zobrazit aktuální konfiguraci
Tato možnost poskytuje rychlý přehled o aktuálně nakonfigurovaných možností.

![Stránka se seznamem všech možností a jejich stav](./media/how-to-connect-installation-wizard/viewconfig.png)

Klikněte na tlačítko **předchozí** zpátky. Pokud vyberete **ukončovací**, zavřete průvodce instalací.

## <a name="customize-synchronization-options"></a>Přizpůsobit možnosti synchronizace
Tato možnost umožňuje provádět změny konfigurace synchronizace. Zobrazí podmnožinu možnosti z cesty instalace vlastní konfigurace. Tato možnost se zobrazí i v případě, že jste původně použili Expresní instalace.

* [Přidat další adresáře](how-to-connect-install-custom.md#connect-your-directories). Odebírání adresáře, naleznete v tématu [odstranit konektor](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Změna domény a organizační jednotky filtrování](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Filtrování skupiny odebrat.
* [Změnit volitelné funkce](how-to-connect-install-custom.md#optional-features).

Další možnosti z počáteční instalaci nelze změnit a nejsou k dispozici. Tyto možnosti jsou:

* Změňte atribut, který chcete použít pro userPrincipalName a sourceAnchor.
* Změňte metodu spojovacího pro objekty z jiné doménové struktuře.
* Povolte filtrování podle skupin.

## <a name="refresh-directory-schema"></a>Aktualizovat schéma adresáře
Tato možnost se používá, pokud jste nezměnili schéma v jednom z vaší místní doménové struktury AD DS. Například můžete nainstalovat Exchange a upgradovat na Windows serveru 2012 schéma s objekty zařízení. V takovém případě musíte dáte pokyn, aby Azure AD Connect a znovu načíst schéma ze služby AD DS aktualizovat uloženou v mezipaměti. Tato akce obnoví také synchronizační pravidla. Pokud přidáte schématu serveru Exchange jako příklad jsou synchronizační pravidla pro Exchange v konfiguraci přidaný.

Když vyberete tuto možnost, jsou uvedeny všechny adresáře v konfiguraci. Můžete ponechat výchozí nastavení a aktualizovat všechny doménové struktury nebo zrušte výběr některé z nich.

![Stránka se seznamem všech adresářů v prostředí](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurovat pracovní režim
Tato možnost umožňuje povolit nebo zakázat pracovní režim na serveru. Další informace o pracovní režim a způsobu jejich využití najdete v [operace](how-to-connect-sync-operations.md#staging-mode).

Možnost zobrazí, pokud pracovní je aktuálně povoleno nebo zakázáno:  
![Možnost, která se také zobrazuje aktuální stav pracovní režim](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Chcete-li ke změně stavu, vyberte tuto možnost a vyberte nebo zrušte zaškrtnutí políčka.  
![Možnost, která se také zobrazuje aktuální stav pracovní režim](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Změna přihlašovacích údajů uživatele
Tato možnost umožňuje změnit metodu přihlašování uživatele do a z synchronizace hodnot hash hesel, předávacího ověřování nebo federace. Nelze změnit na **nekonfigurujte**.

Další informace o této možnosti najdete v tématu [přihlášení uživatele](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu používá synchronizace Azure AD Connect v [Principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
