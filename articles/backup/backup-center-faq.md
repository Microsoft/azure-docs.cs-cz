---
title: Centrum zálohování – Nejčastější dotazy
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se centra zálohování.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b10a9e73e65cf12c43ce28b429a8f12e0b960a76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90995777"
---
# <a name="backup-center---frequently-asked-questions"></a>Centrum zálohování – Nejčastější dotazy

## <a name="management"></a>Správa

### <a name="can-backup-center-be-used-across-tenants"></a>Může se centrum zálohování používat napříč klienty?

Ano, pokud používáte [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) a máte delegovaný přístup k předplatným v různých klientech, můžete použít službu Backup Center jako jedno podokno skla ke správě záloh napříč klienty.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>Dá se centrum zálohování použít ke správě trezorů Recovery Services a trezorů služby Backup?

Ano, centrum zálohování může spravovat jak [trezory Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) , tak i [trezory služby Backup](backup-vault-overview.md).

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Je před datovými plochami v zálohovacím centru nějaká prodleva?

Centrum zálohování se zaměřuje na poskytování informací v reálném čase. Mezi časem, kdy se entita zobrazuje na obrazovce jednotlivého trezoru, může trvat několik sekund a čas, kdy se stejná entita zobrazí v centru zálohování.

## <a name="configuration"></a>Konfigurace

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Potřebuji něco nakonfigurovat, aby se zobrazila data v centru záloh?

Ne. Centrum zálohování je připravené z boxu. Pokud ale chcete zobrazit [sestavy zálohování](https://docs.microsoft.com/azure/backup/configure-reports) v části Backup Center, musíte nakonfigurovat vytváření sestav pro vaše trezory.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Musím pro používání služby Backup Center potřebovat nějaká zvláštní oprávnění?

Centrum zálohování, které nepotřebuje žádná nová oprávnění Pokud máte pro prostředky, které spravujete, správnou úroveň přístupu RBAC, můžete pro tyto prostředky použít Centrum zálohování. Pokud například chcete zobrazit informace o zálohování, budete potřebovat přístup **Čtenář** k vašim trezorům. Pokud chcete nakonfigurovat zálohování a provádět další akce související se zálohováním, budete potřebovat role **přispěvatele zálohování** nebo **operátora zálohování** . Přečtěte si další informace o [rolích RBAC pro Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="pricing"></a>Ceny

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Musím pro použití Průzkumníka zálohování platit cokoli navíc?

V současné době nejsou k dispozici žádné další náklady (od nákladů na zálohování), které slouží k používání služby Backup Center. Pokud však používáte [sestavy zálohování](https://docs.microsoft.com/azure/backup/configure-reports) v rámci služby Backup Center, je k dispozici [Cena](https://azure.microsoft.com/pricing/details/monitor/) za použití protokolů Azure monitor pro sestavy zálohování.

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

* [Běžné dotazy k trezorům Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [Běžné dotazy k zálohování virtuálních počítačů Azure](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)
