---
title: 'Azure AD Connect: Začínáme s použitím expresního nastavení | Dokumentace Microsoftu'
description: Přečtěte si, jak stáhnout, nainstalovat a spustit Průvodce instalací pro Azure AD Connect.
services: active-directory
author: billmath
manager: mtillman
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1af54e3b0237d1d62ae7fb47939e7786fa1219b8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434666"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Začínáme se službou Azure AD Connect s použitím expresního nastavení
**Expresní nastavení** Azure AD Connect se používá, pokud máte jednoduchou doménovou strukturu a [synchronizaci hodnot hash hesel](how-to-connect-password-hash-synchronization.md) pro ověřování. **Expresní nastavení** je výchozí možnost a používá se u scénáře, který je nasazován nejčastěji. Stačí jen několik kliknutí a budete moci svůj místní adresář rozšířit do cloudu.

Před zahájením instalace služby Azure AD Connect nezapomeňte [stáhnout službu Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) a dokončit nezbytné kroky uvedené v tématu [Azure AD Connect: Hardware a nezbytné předpoklady](how-to-connect-install-prerequisites.md).

Pokud expresní nastavení neodpovídá vaší topologii, přečtěte si [související dokumentaci](#related-documentation) s dalšími scénáři.

## <a name="express-installation-of-azure-ad-connect"></a>Expresní instalace služby Azure AD Connect
Praktickou ukázku těchto kroků si můžete prohlédnout v sekci [videí](#videos).

1. Přihlaste se jako místní správce k serveru, kam chcete nainstalovat Azure AD Connect. Mělo by se jednat o server, který chcete používat k synchronizaci.
2. Přejděte k souboru **AzureADConnect.msi** a dvakrát na něj klikněte.
3. Na uvítací obrazovce zaškrtněte políčko, kterým odsouhlasíte licenční podmínky, a klikněte na **Pokračovat**.  
4. Na obrazovce expresního nastavení klikněte na **Použít expresní nastavení**.  
   ![Vítá vás Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. Na obrazovce připojení ke službě Azure AD zadejte uživatelské jméno a heslo pro globálního správce vaší služby Azure AD. Klikněte na **Další**.  
   ![Připojení k Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Pokud se zobrazí chyba a máte problémy s připojením, přečtěte si téma [Řešení problémů s připojením](tshoot-connect-connectivity.md).
6. Na obrazovce Připojení ke službě AD DS zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Součást domény můžete zadat buď ve formátu NetBios, nebo jako plně kvalifikovaný název domény, tj. FABRIKAM\administrator nebo fabrikam.com\administrator. Klikněte na **Další**.  
   ![Připojení ke službě AD DS](./media/how-to-connect-install-express/connectad.png)
7. Stránka [**Konfigurace přihlášení k Azure AD**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) se zobrazí pouze pokud jste nedokončili [ověření svých domén](../active-directory-domains-add-azure-portal.md) v rámci [požadavků](how-to-connect-install-prerequisites.md).
   ![Neověřené domény](./media/how-to-connect-install-express/unverifieddomain.png)  
   Pokud se vám zobrazí tato stránka, zkontrolujte všechny domény označené jako **Nepřidáno** a **Neověřeno**. Ujistěte se, že domény, které používáte, byly ověřeny v Azure AD. Po ověření domén klikněte na symbol obnovení.
8. Na obrazovce Připraveno ke konfiguraci klikněte na **Instalovat**.
   * Volitelně můžete na stránce Připraveno ke konfiguraci zrušit zaškrtnutí políčka **Po dokončení konfigurace spustit proces synchronizace**. Zaškrtnutí tohoto políčka zrušte, pokud chcete provést nějakou další konfiguraci, například [filtrování](how-to-connect-sync-configure-filtering.md). Pokud u této možnosti zrušíte zaškrtnutí, průvodce instalací provede konfiguraci synchronizace, ale plánovač ponechá vypnutý. Plánovač se nespustí, dokud jej ručně nezapnete tím, že [znovu spustíte průvodce instalací](how-to-connect-installation-wizard.md).
   * Pokud necháte políčko **Po dokončení konfigurace spustit proces synchronizace** zaškrtnuté, spustí se okamžitě úplná synchronizace všech uživatelů, skupin a kontaktů do Azure AD.
   * Pokud máte Exchange ve své místní službě Active Directory, pak máte také možnost povolit [**Hybridní nasazení systému Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Tuto možnost povolte, pokud chcete mít poštovní schránky systému Exchange zároveň v místním nasazení i v cloudu.
     ![Připraveno ke konfiguraci služby Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. Až instalace skončí, klikněte na **Konec**.
10. Po dokončení instalace se odhlaste a znovu přihlaste. Teprve pak použijte Synchronization Service Manager nebo Synchronization Rule Editor.

## <a name="videos"></a>Videa
Video o používání expresní instalace najdete v následujících tématech:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Další kroky
Nyní, když máte nainstalovanou službu Azure AD Connect, si můžete [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Zjistěte více o těchto funkcích, které byly povoleny v rámci instalace: [Automatický upgrade](how-to-connect-install-automatic-upgrade.md), [Prevence náhodných odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a spouštění synchronizace](how-to-connect-sync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Související dokumentace

| Téma | Odkaz |
| --- | --- |
| Přehled služby Azure AD Connect | [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
| Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md) |
| Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Účty použité k instalaci | [Další informace o účtech a oprávněních služby Azure AD Connect](reference-connect-accounts-permissions.md) |
