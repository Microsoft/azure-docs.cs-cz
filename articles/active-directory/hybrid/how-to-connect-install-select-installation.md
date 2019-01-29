---
title: 'Azure AD Connect: Vyberte typ instalace | Dokumentace Microsoftu'
description: Toto téma vás provede postupy vybrat typ instalace pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: d04cf1665d32590620c9b968e3344513db2b5f7b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152115"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Výběr typu instalace pro Azure AD Connect
Azure AD Connect zahrnuje dva typy instalace pro novou instalaci: Expresní a přizpůsobenou. Toto téma pomáhá při rozhodování, kterou možnost použít během instalace.

## <a name="express"></a>Express
Express je nejběžnější možnost a používá přibližně 90 % všechna nová zařízení. Je navržená k poskytování konfiguraci, která funguje nejběžnějších scénářů zákazníků.

Předpokládá:

- Máte jeden Active Directory v místní doménové struktury.
- Máte účet správce podnikové sítě, které můžete použít k instalaci.
- Máte méně než 100 000 objektů ve vašem místním Active Directory.

Získáte:

- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) z místního do služby Azure AD pro jednotné přihlašování.
- Konfigurace, která se synchronizuje [uživatele, skupiny, kontakty a počítače s Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synchronizace všechny oprávněné objekty ve všech doménách a všech organizačních jednotkách.
- [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) je povoleno, ujistěte se, že vždy používáte nejnovější dostupnou verzi.

Možnosti, kde můžete dál používat Express:

- Pokud nechcete synchronizovat všechny organizační jednotky, můžete stále použít Express a na poslední stránce, zrušte výběr ** spustit proces synchronizace... ***. Potom znovu spusťte Průvodce instalací a změnit organizační jednotky ve [možnosti konfigurace](how-to-connect-installation-wizard.md#customize-synchronization-options) a povolení plánované synchronizace.
- Chcete povolit jednou z funkcí v Azure AD Premium, jako je například zpětný zápis hesla. Nejdřív projděte express získat počáteční instalace byla dokončena. Potom znovu spusťte Průvodce instalací a změnit [možnosti konfigurace](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Vlastní
Vlastní cesta umožňuje mnohem víc možností než express. Byste měli použít ve všech případech, kdy je popsané v předchozí části pro expresní konfigurace není reprezentativní pro vaši organizaci.

Použijte, když:

- Nemáte přístup k účtu podnikového správce ve službě Active Directory.
- Máte více než jednu doménovou strukturu nebo máte v úmyslu synchronizovat více než jednu doménovou strukturu v budoucnu.
- Máte domén v doménové struktuře není dostupný ze serveru připojit.
- Máte v plánu používat federaci nebo předávací ověřování pro přihlášení uživatele.
- Máte více než 100 000 objektů a potřebují používat plnou instalaci systému SQL Server.
- Máte v plánu použít filtrování podle skupin a nejen domény nebo filtrování podle organizačních jednotek.

## <a name="upgrade-from-dirsync"></a>Upgrade z nástroje DirSync
Pokud aktuálně používáte DirSync, postupujte podle pokynů v [upgradu z nástroje DirSync](how-to-dirsync-upgrade-get-started.md) upgradovat existující konfiguraci. Existují dvě různé možnosti upgradu:

- Místní upgrade na instalaci na stejném serveru připojit.
- Paralelní nasazení k instalaci Connect na nový server při stávající server DirSync je stále v provozu.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade z Azure AD Sync
Pokud aktuálně používáte Azure AD Sync, pak můžete postupovat podle [stejný postup](how-to-upgrade-previous-version.md) jako při upgradu z jedné verze připojit na novější. Existují dvě různé možnosti upgradu:

- Místní upgrade na instalaci na stejném serveru připojit.
- Postupné migraci na instalaci Connect na nový server při existující server služby Azure AD Sync je stále v provozu.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrace z FIM2010 nebo v MIM2016
Pokud aktuálně používáte Forefront Identity Manager 2010 nebo Microsoft Identity Manageru 2016 se konektor služby Azure AD, je vaší jedinou možností migrace. Postupujte podle kroků popsaných v [postupné migraci](how-to-upgrade-previous-version.md#swing-migration). V krocích nahraďte v FIM2010/MIM2016 uvedeny žádné informace o Azure AD Sync.

## <a name="next-steps"></a>Další postup
V závislosti na možnosti, které jste vybrali použijte obsah vlevo najít svůj článek s podrobnými kroky.
