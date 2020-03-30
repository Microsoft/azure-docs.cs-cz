---
title: Ukončení ssl s certifikáty Azure Key Vault
description: Zjistěte, jak můžete integrovat Azure Application Gateway s Trezorem klíčů pro serverové certifikáty, které jsou připojené k naslouchacím líným a https povoleno.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 5633dd7b72f4de22cd34b7d093e8ec4d9cb411f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137704"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Ukončení ssl s certifikáty trezoru klíčů

[Azure Key Vault](../key-vault/key-vault-overview.md) je úložiště tajných klíčů spravované platformou, které můžete použít k zabezpečení tajných kódů, klíčů a certifikátů SSL. Azure Application Gateway podporuje integraci s trezorem klíčů pro serverové certifikáty, které jsou připojené k naslouchací procesy s podporou PROTOKOLU HTTPS. Tato podpora je omezena na skladovou položku v2 aplikační brány.

Integrace trezoru klíčů nabízí dva modely pro ukončení SSL:

- Můžete explicitně poskytnout SSL certifikáty připojené k naslouchací proces. Tento model je tradiční způsob, jak předat certifikáty SSL do aplikační brány pro ukončení SSL.
- Volitelně můžete poskytnout odkaz na existující certifikát trezoru klíčů nebo tajný klíč při vytváření naslouchací proces s podporou protokolu HTTPS.

Integrace aplikační brány s trezorem klíčů nabízí mnoho výhod, včetně:

- Silnější zabezpečení, protože certifikáty SSL nejsou přímo zpracovány týmem pro vývoj aplikací. Integrace umožňuje samostatnému týmu zabezpečení:
  * Nastavte aplikační brány.
  * Řízení životního cyklu aplikační brány.
  * Udělte vybraným aplikačním bránám oprávnění pro přístup k certifikátům uloženým v trezoru klíčů.
- Podpora importu existujících certifikátů do trezoru klíčů. Pomocí souborů API úložiště klíčů můžete také vytvářet a spravovat nové certifikáty s libovolným důvěryhodným partnerem trezoru klíčů.
- Podpora automatického obnovení certifikátů, které jsou uloženy v trezoru klíčů.

Aplikační brána v současné době podporuje pouze certifikáty ověřené softwarem. Certifikáty ověřené modulem hardwarového zabezpečení (HSM) nejsou podporovány. Po nakonfigurování aplikační brány pro použití certifikátů trezoru klíčů načíst certifikát z trezoru klíčů a nainstalovat je místně pro ukončení SSL. Instance také dotazování trezoru klíčů v 24hodinových intervalech k načtení obnovené verze certifikátu, pokud existuje. Pokud je nalezen aktualizovaný certifikát, certifikát SSL, který je aktuálně přidružen k naslouchací procesu HTTPS, se automaticky otočí.

> [!NOTE]
> Portál Azure podporuje jenom certifikáty KeyVault, ne tajné klíče. Aplikační brána stále podporuje odkazování na tajné klíče z KeyVault, ale pouze prostřednictvím prostředků, které nejsou portálem, jako je PowerShell, CLI, API, ARM šablony atd. 

## <a name="how-integration-works"></a>Jak integrace funguje

Integrace aplikační brány s trezorem klíčů vyžaduje proces konfigurace ve třech krocích:

1. **Vytvoření spravované identity přiřazené uživatelem**

   Vytvoříte nebo znovu použijete existující spravovanou identitu přiřazenou uživatelem, kterou aplikační brána používá k načtení certifikátů z trezoru klíčů vaším jménem. Další informace najdete v tématu [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md). Tento krok vytvoří novou identitu v tenantovi služby Azure Active Directory. Identita je důvěryhodná odběr, který se používá k vytvoření identity.

1. **Konfigurace trezoru klíčů**

   Potom importujte existující certifikát nebo vytvořte nový v trezoru klíčů. Certifikát budou používat aplikace, které běží prostřednictvím aplikační brány. V tomto kroku můžete také použít tajný klíč trezoru klíčů, který je uložen jako základní 64kódovaný soubor PFX bez hesla. Doporučujeme použít typ certifikátu z důvodu funkce automatického obnovení, která je k dispozici s objekty typu certifikátu v trezoru klíčů. Po vytvoření certifikátu nebo tajného klíče definujete v trezoru klíčů zásady přístupu, které umožní *udělení* identity k tajnému klíči.

1. **Nakonfigurujte aplikační bránu**

   Po dokončení dvou předchozích kroků můžete nastavit nebo upravit existující aplikační bránu tak, aby používala spravovanou identitu přiřazenou uživateli. Můžete také nakonfigurovat certifikát SSL naslouchací proces HTTP tak, aby ukazoval na úplný identifikátor URI certifikátu trezoru klíčů nebo ID tajného klíče.

   ![Certifikáty trezoru klíčů](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další kroky

[Konfigurace ukončení protokolu SSL pomocí certifikátů trezoru klíčů pomocí Azure PowerShellu](configure-keyvault-ps.md)
