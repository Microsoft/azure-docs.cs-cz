---
title: Ukončení protokolu TLS s certifikáty Azure Key Vault
description: Přečtěte si, jak můžete integrovat Azure Application Gateway s Key Vault pro certifikáty serveru, které jsou připojené k posluchačům s povoleným protokolem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 934cf854b0c526ed994c7dc91763f65de64fd14b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617512"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Ukončení protokolu TLS s certifikáty Key Vault

[Azure Key Vault](../key-vault/general/overview.md) je úložiště tajné databáze spravované platformou, které můžete použít k ochraně tajných klíčů, klíčů a certifikátů TLS/SSL. Azure Application Gateway podporuje integraci s Key Vault pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. Tato podpora je omezená na SKU v2 Application Gateway.

Key Vault Integration nabízí dva modely pro ukončení protokolu TLS:

- Můžete explicitně poskytnout certifikáty TLS/SSL připojené k naslouchacího procesu. Tento model je tradiční způsob, jak předat certifikát TLS/SSL Application Gateway pro ukončení protokolu TLS.
- Volitelně můžete zadat odkaz na existující Key Vault certifikát nebo tajný kód při vytváření naslouchacího procesu s povoleným protokolem HTTPS.

Application Gateway integrace s Key Vault nabízí spoustu výhod, včetně:

- Silnější zabezpečení, protože certifikáty TLS/SSL nejsou přímo zpracovávány týmem vývoje aplikace. Integrace umožňuje samostatnému bezpečnostnímu týmu:
  * Nastavte aplikační brány.
  * Řízení životního cyklu brány Application Gateway.
  * Udělte oprávnění vybraným aplikačním branám přístup k certifikátům uloženým v trezoru klíčů.
- Podpora importu existujících certifikátů do trezoru klíčů. Nebo můžete použít rozhraní Key Vault API k vytváření a správě nových certifikátů s některým z důvěryhodných partnerů Key Vault.
- Podpora automatického obnovování certifikátů uložených v trezoru klíčů.

Application Gateway aktuálně podporuje jenom certifikáty ověřované softwarem. Certifikáty ověřované modulem hardwarového zabezpečení (HSM) nejsou podporovány. Po nakonfigurování Application Gateway k používání certifikátů Key Vault jejich instance načtou certifikát z Key Vault a nainstalují je místně pro ukončení protokolu TLS. Instance také dotazují Key Vault ve 24hodinovém intervalu pro načtení obnovené verze certifikátu, pokud existuje. Pokud se najde aktualizovaný certifikát, automaticky se otočí certifikát TLS/SSL, který je aktuálně přidružený k naslouchacímu procesu HTTPS.

> [!NOTE]
> Azure Portal podporuje pouze certifikáty trezoru klíčů, nikoli tajné klíče. Application Gateway dál podporuje odkazování na tajné klíče z trezoru klíčů, ale jenom prostřednictvím jiných prostředků než na portálu, jako je PowerShell, CLI, API, šablony ARM atd. 

## <a name="how-integration-works"></a>Jak funguje integrace

Application Gateway integrace s Key Vault vyžaduje proces konfigurace se třemi kroky:

1. **Vytvoření spravované identity přiřazené uživatelem**

   Můžete vytvořit nebo znovu použít existující spravovanou identitu přiřazenou uživatelem, kterou Application Gateway používá k načtení certifikátů od Key Vault vaším jménem. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md). Tento krok vytvoří novou identitu v tenantovi Azure Active Directory. Identita je důvěryhodná pro předplatné, které se používá k vytvoření identity.

1. **Konfigurace trezoru klíčů**

   Pak buď importujte existující certifikát, nebo vytvořte nový v trezoru klíčů. Certifikát budou používat aplikace, které běží přes Aplikační bránu. V tomto kroku můžete použít taky tajný klíč trezoru klíčů, který je uložený jako soubor PFX s kódováním bez hesla (Base-64). Pro možnost autorecovery, která je k dispozici pro objekty typu certifikátu v trezoru klíčů, doporučujeme použít typ certifikátu. Po vytvoření certifikátu nebo tajného klíče v trezoru klíčů Definujte zásady přístupu, aby bylo možné udělení identity *získat* přístup k tajnému kódu.
   
   > [!NOTE]
   > Pokud službu Application Gateway nasadíte přes šablonu ARM, ať už pomocí Azure CLI nebo PowerShellu, nebo přes aplikaci Azure nasazenou z Azure Portal, certifikát SSL, který je uložený v trezoru klíčů, jako soubor PFX s kódováním Base-64, **musí být bez hesla**. Také je nutné provést kroky v části [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../azure-resource-manager/templates/key-vault-parameter.md). Je zvláště důležité nastavit `enabledForTemplateDeployment` na. `true`

1. **Nakonfigurujte aplikační bránu**

   Po dokončení obou předchozích kroků můžete nastavit nebo upravit existující Aplikační bránu tak, aby používala uživatelsky přiřazenou spravovanou identitu. Certifikát TLS/SSL naslouchacího procesu HTTP můžete nakonfigurovat také tak, aby odkazoval na úplný identifikátor URI Key Vault certifikátu nebo tajného ID.

   ![Certifikáty trezoru klíčů](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další kroky

[Konfigurace ukončení TLS pomocí Key Vault certifikátů pomocí Azure PowerShell](configure-keyvault-ps.md)
