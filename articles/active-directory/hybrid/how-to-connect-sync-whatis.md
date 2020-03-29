---
title: 'Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace | Dokumenty společnosti Microsoft'
description: Vysvětluje, jak synchronizace Azure AD Connect funguje a jak ji přizpůsobit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348735"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace
Synchronizační služby Azure Active Directory Connect (synchronizace Azure AD Connect) jsou hlavní součástí služby Azure AD Connect. Postará se o všechny operace, které souvisejí se synchronizací dat identity mezi místním prostředím a službou Azure AD. Synchronizace Azure AD Connect je nástupcem DirSync, Azure AD Sync a Forefront Identity Manager s nakonfigurovaným konektorem Azure Active Directory.

Toto téma je domovpro **synchronizaci Azure AD Connect** (označované také jako **modul synchronizace**) a obsahuje odkazy na všechna ostatní témata, která s ním souvisejí. Odkazy na Azure AD Connect najdete [v tématu Integrace místních identit pomocí Služby Azure Active Directory](whatis-hybrid-identity.md).

Služba synchronizace se skládá ze dvou součástí, místní **součásti synchronizace Azure AD Connect** a na straně služby ve službě Azure AD s názvem **Synchronizační služba Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Témata synchronizace služby Azure AD Connect
| Téma | Co pokrývá a kdy číst |
| --- | --- |
| **Základy synchronizace Azure AD Connect** | |
| [Principy architektury](concept-azure-ad-connect-sync-architecture.md) |Pro ty z vás, kteří jsou na synchronizační engine a chcete se dozvědět o architektuře a použité termíny. |
| [Technické koncepce](how-to-connect-sync-technical-concepts.md) |Krátká verze tématu architektury a stručně vysvětluje použité termíny. |
| [Topologie pro Azure AD Connect](plan-connect-topologies.md) |Popisuje různé topologie a scénáře, které podporuje synchronizační modul. |
| **Vlastní konfigurace** | |
| [Znovu spustit Průvodce instalací](how-to-connect-installation-wizard.md) |Vysvětluje, jaké možnosti máte k dispozici při opětovném spuštění Průvodce instalací služby Azure AD Connect. |
| [Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md) |Popisuje model konfigurace s názvem deklarativní zřizování. |
| [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Popisuje syntaxi jazyka výrazu používaného při deklarativním zřizování. |
| [Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md) |Popisuje pravidla out-of-box a výchozí konfiguraci. Také popisuje, jak pravidla spolupracují pro out-of-box scénáře pracovat. |
| [Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md) |Pokračuje v předchozím tématu a popisuje, jak konfigurace pro uživatele a kontakty funguje společně, zejména v prostředí s více doménovými strukturami. |
| [Jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md) |Provede vás, jak provést společnou změnu konfigurace toků atributů. |
| [Osvědčené postupy pro změnu výchozí konfigurace](how-to-connect-sync-best-practices-changing-default-configuration.md) |Omezení podpory a provádění změn v konfiguraci předboxu. |
| [Konfigurace filtrování](how-to-connect-sync-configure-filtering.md) |Popisuje různé možnosti, jak omezit objekty, které jsou synchronizovány do služby Azure AD a krok za krokem, jak tyto možnosti konfigurovat. |
| **Funkce a scénáře** | |
| [Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Popisuje funkci *zabránit náhodnému odstranění* a jak ji nakonfigurovat. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Popisuje předdefinovaný plánovač, který importuje, synchronizuje a exportuje data. |
| [Implementace synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) |Popisuje, jak synchronizace hesel funguje, jak implementovat a jak pracovat a řešit potíže. |
| [Zpětný zápis zařízení](how-to-connect-device-writeback.md) |Popisuje, jak funguje zpětný zápis zařízení ve službě Azure AD Connect. |
| [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) |Popisuje, jak rozšířit schéma Azure AD s vlastní atributy. |
| [Umístění PreferredDataLok office 365](how-to-connect-sync-feature-preferreddatalocation.md) |Popisuje, jak umístit prostředky office 365 uživatele ve stejné oblasti jako uživatel. |
| **Služba synchronizace** | |
| [Funkce synchronizační služby Azure AD Connect](how-to-connect-syncservice-features.md) |Popisuje stranu synchronizační služby a jak změnit nastavení synchronizace ve službě Azure AD. |
| [Odolnost proti chybám duplicitního atributu](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Popisuje, jak povolit a používat **adresu userPrincipalName** a **proxyAddresses,** odolnost idlivence atributů. |
| **Operace a ui** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Popisuje ui správce synchronizačních služeb, včetně [karet Operace](how-to-connect-sync-service-manager-ui-operations.md), [Konektory](how-to-connect-sync-service-manager-ui-connectors.md), [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)a [Metaverse Search.](how-to-connect-sync-service-manager-ui-mvsearch.md) |
| [Provozní úkoly a úvahy](how-to-connect-sync-operations.md) |Popisuje provozní problémy, jako je například zotavení po havárii. |
| **Jak...** | |
| [Obnovení účtu Azure AD](how-to-connect-azureadaccount.md) |Jak obnovit přihlašovací údaje účtu služby, který se používá k připojení ze synchronizace Azure AD Connect do Azure AD. |
| **Další informace a reference** | |
| [Porty](reference-connect-ports.md) |Uvádí, které porty je třeba otevřít mezi synchronizačním strojem a místními adresáři a službou Azure AD. |
| [Atributy synchronizované se službou Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Zobrazí seznam všech atributů synchronizovaných mezi místním službou AD a službou Azure AD. |
| [Reference k funkcím](reference-connect-sync-functions-reference.md) |Uvádí všechny funkce, které jsou k dispozici v deklarativní zřizování. |

## <a name="additional-resources"></a>Další zdroje
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
