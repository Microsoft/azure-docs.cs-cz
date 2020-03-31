---
title: Opětovné spuštění Průvodce instalací služby Azure AD Connect | Dokumenty společnosti Microsoft
description: Vysvětluje, jak funguje průvodce instalací při druhém spuštění.
keywords: Průvodce instalací služby Azure AD Connect umožňuje konfigurovat nastavení údržby při jeho spuštění
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261330"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronizace služby Azure AD Connect: Podruhé spuštění Průvodce instalací
Při prvním spuštění průvodce instalací služby Azure AD Connect vás provede postupem konfigurace instalace. Pokud průvodce instalací znovu spustíte, nabídne možnosti údržby.

>[!IMPORTANT]
>Uvědomte si, že průvodce instalací nelze spustit v průběhu synchronizace.  Před spuštěním průvodce ověřte, zda synchronizace není spuštěna.

Průvodce instalací najdete v nabídce Start s názvem **Azure AD Connect**.

![Nabídka Start](./media/how-to-connect-installation-wizard/startmenu.png)

Při spuštění Průvodce instalací se zobrazí stránka s těmito možnostmi:

![Stránka se seznamem dalších úkolů](./media/how-to-connect-installation-wizard/additionaltasks.png)

Pokud jste nainstalovali Službu ADFS s Azure AD Connect, máte ještě více možností. Další možnosti, které máte pro systém ADFS, jsou popsány ve [správě systému ADFS](how-to-connect-fed-management.md#manage-ad-fs).

Vyberte jeden z úkolů a pokračujte klepnutím na tlačítko **Další.**

> [!IMPORTANT]
> Při otevření průvodce instalací jsou všechny operace v synchronizačním modulu pozastaveny. Po dokončení změn konfigurace zavřete průvodce instalací.
>
>

## <a name="view-current-configuration"></a>Zobrazit aktuální konfiguraci
Tato možnost poskytuje rychlý přehled o aktuálně nakonfigurovaných možnostech.

![Stránka se seznamem všech možností a jejich stavem](./media/how-to-connect-installation-wizard/viewconfig.png)

Chcete-li se vrátit, klepněte na tlačítko **Předchozí.** Pokud vyberete **možnost Ukončit**, zavřete průvodce instalací.

## <a name="customize-synchronization-options"></a>Přizpůsobení možností synchronizace
Tato možnost se používá k provádění změn konfigurace synchronizace. Zobrazí se podmnožina možností z vlastní cesty instalace konfigurace. Tato možnost se zobrazí i v případě, že jste původně použili expresní instalaci.

* [Přidejte další adresáře](how-to-connect-install-custom.md#connect-your-directories). Odebrání adresáře naleznete v [tématu Odstranění konektoru](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Změna filtrování domény a ou.](how-to-connect-install-custom.md#domain-and-ou-filtering)
* Odebrat filtrování skupin.
* [Změna volitelných funkcí](how-to-connect-install-custom.md#optional-features).

Ostatní možnosti z počáteční instalace nelze změnit a nejsou k dispozici. Jedná se o tyto možnosti:

* Změňte atribut, který se má použít pro userPrincipalName a sourceAnchor.
* Změňte metodu spojování objektů z jiné doménové struktury.
* Povolte filtrování založené na skupinách.

## <a name="refresh-directory-schema"></a>Aktualizovat schéma adresáře
Tato možnost se používá, pokud jste změnili schéma v jedné z místních doménových struktur ad DS. Je možné, že jste například nainstalovali Exchange nebo upgradovali na schéma systému Windows Server 2012 s objekty zařízení. V takovém případě budete muset pokyn Azure AD Connect znovu číst schéma ze služby AD DS a aktualizovat jeho mezipaměti. Tato akce také regeneruje pravidla synchronizace. Pokud přidáte schéma serveru Exchange, jako příklad jsou do konfigurace přidána pravidla synchronizace pro exchange.

Vyberete-li tuto možnost, budou uvedeny všechny adresáře v konfiguraci. Můžete zachovat výchozí nastavení a aktualizovat všechny doménové struktury nebo zrušit výběr některých z nich.

![Stránka se seznamem všech adresářů v prostředí](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurace pracovního režimu
Tato možnost umožňuje povolit a zakázat pracovní režim na serveru. Další informace o pracovním režimu a způsobu jeho použití naleznete v [správě Operations](how-to-connect-sync-staging-server.md).

Tato možnost ukazuje, zda je pracovní fáze aktuálně povolena nebo zakázána:  
![Možnost, která také zobrazuje aktuální stav pracovního režimu](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Chcete-li změnit stav, zaškrtněte tuto volbu a zaškrtněte nebo zrušte zaškrtnutí políčka.  
![Možnost, která také zobrazuje aktuální stav pracovního režimu](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Změna přihlášení uživatele
Tato možnost umožňuje změnit metodu přihlášení uživatele na a z synchronizace hash hesla, předávacího ověřování nebo federace. Nelze změnit **nekonfigurovat**.

Další informace o této možnosti naleznete [v tématu přihlášení uživatele](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Další kroky
* Další informace o konfiguračním modelu používaném synchronizací Azure AD Connect v [článku Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
