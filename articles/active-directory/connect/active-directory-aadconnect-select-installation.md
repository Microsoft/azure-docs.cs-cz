---
title: 'Azure AD Connect: Vyberte typ instalace | Microsoft Docs'
description: Toto téma vás provede jak vybrat typ instalace, který má být použit pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: df7cc8c978dfa215a79d087d2aa1f0dea258d6b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591639"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Vyberte typ instalace, která se má použít pro Azure AD Connect
Azure AD Connect má dva typy instalace pro novou instalaci: Express a přizpůsobit. V tomto tématu můžete rozhodnout, které můžete použít během instalace.

## <a name="express"></a>Express
Express je nejběžnější možnost a používá přibližně 90 % všechny nové instalace. Byla navržená tak, aby zadat konfigurace, která funguje pro nejběžnějších scénářů zákazníka.

Předpokládá:

- Máte jeden Active Directory doménové struktury na místě.
- Máte účet správce podnikové sítě, které můžete použít pro instalaci.
- Máte méně než 100 000 objektů ve vaší místní službě Active Directory.

Můžete získat:

- [Synchronizaci hodnoty hash hesla](active-directory-aadconnectsync-implement-password-hash-synchronization.md) z místním nasazením a Azure AD pro jednotné přihlašování.
- Konfigurace, který se synchronizuje [uživatelé, skupiny, kontakty a počítače s Windows 10](active-directory-aadconnectsync-understanding-default-configuration.md).
- Synchronizace všech oprávněných objektů ve všech doménách a ve všech organizačních.
- [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) je povolena a zajistěte, aby vždy použít nejnovější dostupnou verzi.

Možnosti, kde můžete dál používat Express:

- Pokud nechcete synchronizovat všechny organizační jednotky, můžete stále používají systém Express a na poslední stránce, zrušte výběr ** zahájit proces synchronizace... ***. Pak znovu spusťte Průvodce instalací a změnit organizačních jednotek ve [možnosti konfigurace](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options) a povolte plánované synchronizaci.
- Chcete povolit jedna z funkcí v Azure AD Premium, jako je například zpětný zápis hesla. Nejdřív projít express získat počáteční instalaci dokončit. Pak znovu spusťte Průvodce instalací a změnit [možnosti konfigurace](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Vlastní
Cesta k přizpůsobenému umožňuje mnohem víc možností než express. By být používána ve všech případech, kdy není konfigurace popsané v předchozí části pro expresní reprezentativní pro vaši organizaci.

Použijte, když:

- Nemáte přístup k účet správce podnikové sítě ve službě Active Directory.
- Máte více než jedné doménové struktuře nebo budete chtít v budoucnu synchronizovat více než jedné doménové struktuře.
- Máte domén v doménové struktuře není dostupný ze serveru připojit.
- Máte v plánu používat federaci nebo předávací ověřování pro přihlášení uživatele.
- Máte více než 100 000 objektů a potřebují používat plnou instalaci systému SQL Server.
- Máte v plánu pomocí filtrování podle skupiny a ne jen domény nebo filtrování založené na organizační jednotku.

## <a name="upgrade-from-dirsync"></a>Upgrade z nástroje DirSync
Pokud aktuálně používáte DirSync, postupujte podle pokynů v [upgradu z nástroje DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) k upgradu vaší stávající konfigurace. Existují dva různé možnosti upgradu:

- Místní upgrade na instalaci připojit na stejném serveru.
- Paralelní nasazení k instalaci Connect na nový server, zatímco existující server DirSync je stále v provozu.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade z Azure AD Sync
Pokud používáte Azure AD Sync, pak můžete provést [stejný postup](active-directory-aadconnect-upgrade-previous-version.md) jako při upgradu z jedné verze připojit na novější. Existují dva různé možnosti upgradu:

- Místní upgrade na instalaci připojit na stejném serveru.
- Migrace dráha instalace Connect na nový server při existující server Azure AD Sync je stále v provozu.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrace z FIM2010 nebo MIM2016
Pokud aktuálně používáte produktu Forefront Identity Manager 2010 nebo Microsoft Identity Manager 2016 se konektor služby Azure AD, je jedinou možností migrace. Postupujte podle kroků popsaných v [dráha migrace](active-directory-aadconnect-upgrade-previous-version.md#swing-migration). V krocích nahraďte FIM2010/MIM2016 uvedeny žádné informace o Azure AD Sync.

## <a name="next-steps"></a>Další postup
V závislosti na možnosti, kterou jste se rozhodli použít použijte v tabulce obsahu na levé straně najít váš článek s podrobné kroky.
