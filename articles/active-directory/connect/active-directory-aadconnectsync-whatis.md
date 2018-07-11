---
title: 'Synchronizace Azure AD Connect: Principy a přizpůsobit synchronizaci podle toho | Dokumentace Microsoftu'
description: Vysvětluje, jak Azure AD Connect synchronizaci funguje a jak přizpůsobit.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 247b7bb5b5dbce94c8f8339a6f06c8ae5dab75d9
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919361"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizace Azure AD Connect: Principy a přizpůsobení synchronizace
Synchronizace služby Azure Active Directory Connect (Azure AD Connect sync) je hlavní součástí služby Azure AD Connect. Postará se o všechny operace, které se vztahují k synchronizaci dat identity mezi místním prostředím a Azure AD. Synchronizace Azure AD Connect je nástupcem nástroje DirSync, Azure AD Sync a produktu Forefront Identity Manager pomocí Azure Active Directory konektoru nakonfigurované.

Toto téma je domovská stránka pro **synchronizace Azure AD Connect** (také nazývané **synchronizační modul**) a obsahuje odkazy na další témata, které s ním souvisejí. Odkazy na Azure AD Connect, naleznete v části [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

Službě synchronizace se skládá ze dvou částí: místní **synchronizace Azure AD Connect** komponenty a na straně služby ve službě Azure AD s názvem **služby synchronizace Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Témata služby Azure AD Connect sync
| Téma | Co zahrnuje a kdy se má načíst |
| --- | --- |
| **Základy služby Azure AD Connect sync** | |
| [Pochopení architektury](active-directory-aadconnectsync-understanding-architecture.md) |Pro ty z vás, kteří jsou nové synchronizačního modulu a chcete se dozvědět o architektuře a termíny používané. |
| [Technické koncepce](active-directory-aadconnectsync-technical-concepts.md) |Zkrácené verze architektury tématu a stručně popisuje termíny používané. |
| [Topologie pro Azure AD Connect](active-directory-aadconnect-topologies.md) |Popisuje různé topologie a scénáře, které podporuje synchronizačního modulu. |
| **Vlastní konfigurace** | |
| [Znovu spustit Průvodce instalací](active-directory-aadconnectsync-installation-wizard.md) |Vysvětluje, jaké možnosti můžete mít k dispozici, když znovu spustíte Průvodce instalací Azure AD Connect. |
| [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Popisuje model konfigurace nazývá deklarativní zřizování. |
| [Principy výrazů deklarativního zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Popisuje syntaxi pro výraz jazyk používaný v deklarativní zřizování. |
| [Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md) |Popisuje pravidla out-of-box a výchozí konfigurace. Také popisuje, jak spolupracují pravidla pro scénáře out-of-box pracovat. |
| [Principy uživatelů a kontaktů](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Pokračuje v předchozím tématu a popisuje, jak konfigurace pro uživatele a kontakty spolupracují, zejména v prostředí s více doménovými strukturami. |
| [Jak provést změnu výchozí konfigurace](active-directory-aadconnectsync-change-the-configuration.md) |Provede můžete provádět změny pro toky atributů společné konfigurace. |
| [Osvědčené postupy pro změnu výchozí konfigurace](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Omezení podpory a k provádění změn konfigurace out-of-box. |
| [Konfigurace filtrování](active-directory-aadconnectsync-configure-filtering.md) |Popisuje různé možnosti pro omezení, které objekty se synchronizované do služby Azure AD a podrobné konfigurace těchto možností. |
| **Funkce a scénáře** | |
| [Prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Popisuje *prevence náhodného odstranění* funkce a jeho konfiguraci. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |Popisuje integrované plánovače, která je importování, synchronizaci a export dat. |
| [Implementace synchronizace hodnot hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |Popisuje, jak funguje synchronizace hesel, jak implementovat a jak používat a řešení potíží s. |
| [Zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md) |Popisuje, jak funguje zpětný zápis zařízení ve službě Azure AD Connect. |
| [Rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md) |Popisuje, jak rozšířit schéma služby Azure AD s vlastní atributy. |
| [PreferredDataLocation Office 365](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Popisuje, jak vložit prostředky uživatele Office 365 ve stejné oblasti jako uživatel. |
| **Synchronizační služba** | |
| [Funkce služby synchronizace Azure AD Connect](active-directory-aadconnectsyncservice-features.md) |Popisuje na straně služby sync a jak změnit nastavení synchronizace ve službě Azure AD. |
| [Odolnost vůči duplicitě atributů](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Popisuje, jak povolit a používat **userPrincipalName** a **proxyAddresses** odolnost duplicitních atributů hodnoty. |
| **Operace a uživatelského rozhraní** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Popisuje uživatelské rozhraní Synchronization Service Manager, včetně [operace](active-directory-aadconnectsync-service-manager-ui-operations.md), [konektory](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Návrhář Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), a [vyhledávání Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) karty. |
| [Provozní úlohy a důležité informace](active-directory-aadconnectsync-operations.md) |Popisuje provozní otázky, jako je zotavení po havárii. |
| **Jak...** | |
| [Obnovení účtu služby Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Jak resetovat přihlašovací údaje účtu služby používaného pro připojení z Azure AD Connect sync ke službě Azure AD. |
| **Další informace a odkazy** | |
| [Porty](active-directory-aadconnect-ports.md) |Uvádí porty, které budete muset otevřít mezi synchronizačního modulu a místními adresáři a Azure AD. |
| [Atributy synchronizované se službou Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Zobrazuje seznam všech atributů synchronizovaných mezi místním AD a Azure AD. |
| [Reference k funkcím](active-directory-aadconnectsync-functions-reference.md) |Seznam všech funkcí, které jsou k dispozici v deklarativní zřizování. |

## <a name="additional-resources"></a>Další prostředky
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
