---
title: 'Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace | Microsoft Docs'
description: Vysvětluje, jak Azure AD Connect synchronizovat funguje a jak přizpůsobit.
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
ms.author: markvi
ms.openlocfilehash: 6b2724f4c9511d606ab8eeac2dedea8759283883
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595253"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace
Synchronizace služby Azure Active Directory Connect (Azure AD Connect sync) je hlavní součást služby Azure AD Connect. Se stará o všechny operace, které se vztahují k synchronizaci dat identity mezi místním prostředím a Azure AD. Synchronizace Azure AD Connect je nástupcem nástroje DirSync, Azure AD Sync a produktu Forefront Identity Manager s Azure Active Directory Connector nakonfigurované.

Toto téma je domovská stránka pro **synchronizace Azure AD Connect** (také nazývané **synchronizační modul**) a jsou uvedeny odkazy na další témata s ním souvisejí. Odkazy na Azure AD Connect, najdete v části [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

Synchronizační služby se skládá ze dvou komponent, místní **synchronizace Azure AD Connect** součásti a na straně služby ve službě Azure AD s názvem **služba Azure AD Connect sync**.

## <a name="azure-ad-connect-sync-topics"></a>Témata týkající se synchronizace Azure AD Connect
| Téma | Co pokrývá a kdy se má číst |
| --- | --- |
| **Základy synchronizace Azure AD Connect** | |
| [Znalost architektury](active-directory-aadconnectsync-understanding-architecture.md) |Pro ty z vás, kdo jsou nové synchronizačního modulu a chcete se dozvědět o architektuře a termínů používaných. |
| [Technické koncepty](active-directory-aadconnectsync-technical-concepts.md) |Zkrácený tématu architektura a stručně popisuje termínů používaných. |
| [Topologie pro Azure AD Connect](active-directory-aadconnect-topologies.md) |Popisuje různé topologie a synchronizační modul podporuje scénáře. |
| **Vlastní konfigurace** | |
| [Znovu spustit Průvodce instalací](active-directory-aadconnectsync-installation-wizard.md) |Vysvětluje, co možnosti budete mít k dispozici, pokud znovu spustíte Průvodce instalací služby Azure AD Connect. |
| [Seznámení s deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Popisuje konfigurační model názvem deklarativní zřizování. |
| [Principy výrazů deklarativního zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Popisuje syntaxi výrazu jazyka použitého v deklarativní zřizování. |
| [Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md) |Popisuje pravidla out-of-box a výchozí konfigurace. Také popisuje, jak spolupracují pravidla pro scénáře out-of-box pracovat. |
| [Principy uživatelů a kontaktů](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Pokračuje v předchozí tématu a popisuje, jak konfigurace pro uživatele a kontakty funguje společně, zejména v prostředí s více doménovými strukturami. |
| [Jak provést změnu výchozí konfigurace](active-directory-aadconnectsync-change-the-configuration.md) |Vás provede procesem vytvoření obvyklé konfigurace změňte toky atributů. |
| [Osvědčené postupy pro změnu výchozí konfigurace](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Omezení podpory a pro provádění změn konfigurace out-of-box. |
| [Konfigurace filtrování](active-directory-aadconnectsync-configure-filtering.md) |Popisuje různé možnosti pro omezení počtu objektů, které probíhá synchronizované do služby Azure AD a podrobný postup konfigurace tyto možnosti. |
| **Scénáře a funkce** | |
| [Prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Popisuje *prevence náhodného odstranění* funkce a jeho konfiguraci. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |Popisuje předdefinované Plánovač, která je importování, synchronizaci a export dat. |
| [Implementace synchronizace hesel hash](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |Popisuje, jak funguje synchronizace hesel, jak implementovat a pracovat a odstraňování souvisejících potíží. |
| [Zpětný zápis zařízení.](active-directory-aadconnect-feature-device-writeback.md) |Popisuje, jak funguje zpětný zápis zařízení v Azure AD Connect. |
| [Rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md) |Popisuje, jak rozšířit schéma služby Azure AD s vlastní atributy. |
| [PreferredDataLocation Office 365](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Popisuje, jak převést prostředky Office 365 uživatele ve stejné oblasti jako uživatel. |
| **Synchronizační služba mim** | |
| [Funkce služby Azure AD Connect sync](active-directory-aadconnectsyncservice-features.md) |Popisuje na straně služby sync a chcete-li změnit nastavení synchronizace ve službě Azure AD. |
| [Odolnost proti chybám duplicitní atribut](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Popisuje, jak povolit a používat **userPrincipalName** a **proxyAddresses** duplicitní atribut hodnoty odolnost proti chybám. |
| **Operace a uživatelského rozhraní** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Popisuje rozhraní Synchronization Service Manager, včetně [operace](active-directory-aadconnectsync-service-manager-ui-operations.md), [konektory](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse designeru](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), a [hledání úložiště Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) karty. |
| [Provozní úlohy a důležité informace](active-directory-aadconnectsync-operations.md) |Popisuje provozní otázky, jako je například zotavení po havárii. |
| **Jak...** | |
| [Resetování účet Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Jak se resetovat přihlašovací údaje účtu služby používaného pro připojení z Azure AD Connect sync ke službě Azure AD. |
| **Další informace a odkazy** | |
| [Porty](active-directory-aadconnect-ports.md) |Seznam portů, které je třeba otevřít mezi synchronizační modul a místních adresářů a Azure AD. |
| [Atributy synchronizované se službou Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Zobrazuje seznam všech atributů synchronizovaných mezi místní AD a Azure AD. |
| [Reference k funkcím](active-directory-aadconnectsync-functions-reference.md) |Obsahuje seznam všech funkcí, které jsou k dispozici v deklarativní zřizování. |

## <a name="additional-resources"></a>Další prostředky
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
