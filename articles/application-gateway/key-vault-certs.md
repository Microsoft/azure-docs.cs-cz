---
title: Ukončení tls s certifikáty Azure Key Vault
description: Zjistěte, jak můžete integrovat Azure Application Gateway s Trezorem klíčů pro serverové certifikáty, které jsou připojené k naslouchacím líným a https povoleno.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 934cf854b0c526ed994c7dc91763f65de64fd14b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617512"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Ukončení tls s certifikáty trezoru klíčů

[Azure Key Vault](../key-vault/general/overview.md) je úložiště tajných klíčů spravované platformou, které můžete použít k zabezpečení tajných kódů, klíčů a certifikátů TLS/SSL. Azure Application Gateway podporuje integraci s trezorem klíčů pro serverové certifikáty, které jsou připojené k naslouchací procesy s podporou PROTOKOLU HTTPS. Tato podpora je omezena na skladovou položku v2 aplikační brány.

Integrace trezoru klíčů nabízí dva modely pro ukončení TLS:

- Můžete explicitně poskytnout certifikáty TLS/SSL připojené k naslouchací proces. Tento model je tradiční způsob předání certifikátů TLS/SSL do aplikační brány pro ukončení TLS.
- Volitelně můžete poskytnout odkaz na existující certifikát trezoru klíčů nebo tajný klíč při vytváření naslouchací proces s podporou protokolu HTTPS.

Integrace aplikační brány s trezorem klíčů nabízí mnoho výhod, včetně:

- Silnější zabezpečení, protože certifikáty TLS/SSL nejsou přímo zpracovány vývojovým týmem aplikací. Integrace umožňuje samostatnému týmu zabezpečení:
  * Nastavte aplikační brány.
  * Řízení životního cyklu aplikační brány.
  * Udělte vybraným aplikačním bránám oprávnění pro přístup k certifikátům uloženým v trezoru klíčů.
- Podpora importu existujících certifikátů do trezoru klíčů. Pomocí souborů API úložiště klíčů můžete také vytvářet a spravovat nové certifikáty s libovolným důvěryhodným partnerem trezoru klíčů.
- Podpora automatického obnovení certifikátů, které jsou uloženy v trezoru klíčů.

Aplikační brána v současné době podporuje pouze certifikáty ověřené softwarem. Certifikáty ověřené modulem hardwarového zabezpečení (HSM) nejsou podporovány. Po nakonfigurování aplikační brány pro použití certifikátů trezoru klíčů načíst certifikát z trezoru klíčů a nainstalovat je místně pro ukončení TLS. Instance také dotazování trezoru klíčů v 24hodinových intervalech k načtení obnovené verze certifikátu, pokud existuje. Pokud je nalezen aktualizovaný certifikát, certifikát TLS/SSL, který je aktuálně přidružen k naslouchací proceshttps, se automaticky otočí.

> [!NOTE]
> Portál Azure podporuje jenom certifikáty KeyVault, ne tajné klíče. Aplikační brána stále podporuje odkazování na tajné klíče z KeyVault, ale pouze prostřednictvím prostředků, které nejsou portálem, jako je PowerShell, CLI, API, ARM šablony atd. 

## <a name="how-integration-works"></a>Jak integrace funguje

Integrace aplikační brány s trezorem klíčů vyžaduje proces konfigurace ve třech krocích:

1. **Vytvoření spravované identity přiřazené uživatelem**

   Vytvoříte nebo znovu použijete existující spravovanou identitu přiřazenou uživatelem, kterou aplikační brána používá k načtení certifikátů z trezoru klíčů vaším jménem. Další informace najdete v tématu [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md). Tento krok vytvoří novou identitu v tenantovi služby Azure Active Directory. Identita je důvěryhodná odběr, který se používá k vytvoření identity.

1. **Konfigurace trezoru klíčů**

   Potom importujte existující certifikát nebo vytvořte nový v trezoru klíčů. Certifikát budou používat aplikace, které běží prostřednictvím aplikační brány. V tomto kroku můžete také použít tajný klíč trezoru klíčů, který je uložen jako soubor PFX kódovaný bez hesla, base-64. Doporučujeme použít typ certifikátu z důvodu funkce automatického obnovení, která je k dispozici s objekty typu certifikátu v trezoru klíčů. Po vytvoření certifikátu nebo tajného klíče definujete v trezoru klíčů zásady přístupu, které umožní *udělení* identity k tajnému klíči.
   
   > [!NOTE]
   > Pokud nasadíte aplikační bránu pomocí šablony ARM, buď pomocí Azure CLI nebo PowerShell, nebo prostřednictvím aplikace Azure nasazené z portálu Azure, musí **být**certifikát SSL uložený v trezoru klíčů jako soubor PFX kódovaný základní 64. Také je nutné provést kroky v [použití trezoru klíčů Azure předat hodnotu zabezpečeného parametru během nasazení](../azure-resource-manager/templates/key-vault-parameter.md). Je obzvláště důležité nastavit `enabledForTemplateDeployment` `true`.

1. **Nakonfigurujte aplikační bránu**

   Po dokončení dvou předchozích kroků můžete nastavit nebo upravit existující aplikační bránu tak, aby používala spravovanou identitu přiřazenou uživateli. Můžete také nakonfigurovat certifikát TLS/SSL naslouchaného protokolu HTTP tak, aby ukazoval na úplný identifikátor URI certifikátu trezoru klíčů nebo ID tajného klíče.

   ![Certifikáty trezoru klíčů](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další kroky

[Konfigurace ukončení protokolu TLS pomocí certifikátů trezoru klíčů pomocí Azure PowerShellu](configure-keyvault-ps.md)
