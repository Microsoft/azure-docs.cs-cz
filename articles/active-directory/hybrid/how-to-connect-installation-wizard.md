---
title: Opětovné spuštění Průvodce instalací Azure AD Connect | Microsoft Docs
description: Vysvětluje, jak Průvodce instalací funguje podruhé při jeho spuštění.
keywords: Průvodce instalací Azure AD Connect umožňuje nakonfigurovat nastavení údržby podruhé, když ho spustíte.
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
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306119"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect synchronizace: podruhé spuštění Průvodce instalací
Při prvním spuštění Průvodce instalací Azure AD Connect vás provede konfigurací instalace. Pokud znovu spustíte Průvodce instalací, nabízí možnosti údržby.

>[!IMPORTANT]
>Mějte na paměti, že v průběhu synchronizace nemůžete spustit Průvodce instalací.  Před spuštěním Průvodce prosím ověřte, že není spuštěná synchronizace.

Průvodce instalací můžete najít v nabídce Start s názvem **Azure AD Connect**.

![Nabídka Start](./media/how-to-connect-installation-wizard/startmenu.png)

Po spuštění Průvodce instalací se zobrazí stránka s těmito možnostmi:

![Stránka se seznamem dalších úloh](./media/how-to-connect-installation-wizard/additionaltasks.png)

Pokud jste nainstalovali službu AD FS s Azure AD Connect, máte ještě více možností. Další možnosti, které máte v AD FS, najdete v části [Správa služby ADFS](how-to-connect-fed-management.md#manage-ad-fs).

Vyberte jednu z úloh a pokračujte kliknutím na tlačítko **Další** .

> [!IMPORTANT]
> Po otevření Průvodce instalací se všechny operace v synchronizačním modulu pozastaví. Nezapomeňte Průvodce instalací zavřít hned po dokončení změn konfigurace.
>
>

## <a name="view-current-configuration"></a>Zobrazit aktuální konfiguraci
Tato možnost nabízí rychlé zobrazení aktuálně nakonfigurovaných možností.

![Stránka se seznamem všech možností a jejich stavu](./media/how-to-connect-installation-wizard/viewconfig.png)

Vraťte se kliknutím na tlačítko **Předchozí** . Pokud vyberete možnost **ukončit**, Průvodce instalací zavřete.

## <a name="customize-synchronization-options"></a>Přizpůsobení možností synchronizace
Tato možnost slouží k provádění změn konfigurace synchronizace. Zobrazí se podmnožina možností z cesty k instalaci vlastní konfigurace. Tato možnost se zobrazí, i když jste nejdřív použili expresní instalaci.

* [Přidejte další adresáře](how-to-connect-install-custom.md#connect-your-directories). Informace o odebrání adresáře najdete v tématu [odstranění konektoru](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Změna filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering)
* Odeberte filtrování skupin.
* [Změnit volitelné funkce](how-to-connect-install-custom.md#optional-features).

Ostatní možnosti počáteční instalace nelze změnit a nejsou k dispozici. Jsou to tyto možnosti:

* Změňte atribut, který má být použit pro atribut userPrincipalName a sourceAnchor.
* Změňte metodu spojování objektů z jiné doménové struktury.
* Povolte filtrování na základě skupin.

## <a name="refresh-directory-schema"></a>Aktualizovat schéma adresáře
Tato možnost se používá v případě, že jste změnili schéma v jedné z místních doménových struktur služba AD DS. Můžete například nainstalovat Exchange nebo upgradovat na schéma Windows Serveru 2012 s objekty zařízení. V takovém případě je potřeba, abyste Azure AD Connect znovu načetli schéma z služba AD DS a aktualizovali jeho mezipaměť. Tato akce také znovu vygeneruje pravidla synchronizace. Pokud přidáte schéma serveru Exchange, jako příklad se do konfigurace přidají pravidla synchronizace pro Exchange.

Když vyberete tuto možnost, zobrazí se všechny adresáře ve vaší konfiguraci. Můžete ponechat výchozí nastavení a aktualizovat všechny doménové struktury nebo zrušit výběr některých z nich.

![Stránka se seznamem všech adresářů v prostředí](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurovat pracovní režim
Tato možnost umožňuje povolit nebo zakázat pracovní režim na serveru. Další informace o pracovním režimu a způsobu jejich použití najdete v tématu [operace](how-to-connect-sync-staging-server.md).

Možnost zobrazuje, zda je pracovní postup aktuálně povolen nebo zakázán:  
![Snímek obrazovky, který zobrazuje pracovní režim zakázán.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Chcete-li změnit stav, vyberte tuto možnost a zaškrtněte nebo zrušte zaškrtnutí políčka.  
![Možnost, která zobrazuje aktuální stav pracovního režimu](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Změna přihlášení uživatele
Tato možnost umožňuje změnit metodu přihlašování uživatelů na a ze synchronizace hodnot hash hesel, předávacího ověřování nebo federace. Nemůžete změnit **konfiguraci**.

Další informace o této možnosti najdete v tématu věnovaném [přihlášení uživatele](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace, který používá Azure AD Connect synchronizaci při [porozumění deklarativnímu zajišťování](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
