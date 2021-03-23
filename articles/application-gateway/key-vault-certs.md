---
title: Ukončení protokolu TLS s certifikáty Azure Key Vault
description: Přečtěte si, jak můžete integrovat Azure Application Gateway s Key Vault pro certifikáty serveru, které jsou připojené k posluchačům s povoleným protokolem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775730"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Ukončení šifrování TLS s využitím certifikátů služby Key Vault

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

Application Gateway aktuálně podporuje jenom certifikáty ověřované softwarem. Certifikáty ověřované modulem hardwarového zabezpečení (HSM) nejsou podporovány. Po nakonfigurování Application Gateway k používání certifikátů Key Vault jejich instance načtou certifikát z Key Vault a nainstalují je místně pro ukončení protokolu TLS. Instance také dotazují Key Vault ve 4 hodinách, aby načetly obnovenou verzi certifikátu, pokud existuje. Pokud se najde aktualizovaný certifikát, automaticky se otočí certifikát TLS/SSL, který je aktuálně přidružený k naslouchacímu procesu HTTPS.

> [!NOTE]
> Azure Portal podporuje pouze certifikáty trezoru klíčů, nikoli tajné klíče. Application Gateway dál podporuje odkazování na tajné klíče z trezoru klíčů, ale jenom prostřednictvím jiných prostředků než na portálu, jako je PowerShell, CLI, API, šablony ARM atd. 

## <a name="how-integration-works"></a>Jak funguje integrace

Application Gateway integrace s Key Vault vyžaduje proces konfigurace se třemi kroky:

1. **Vytvoření spravované identity přiřazené uživatelem**

   Můžete vytvořit nebo znovu použít existující spravovanou identitu přiřazenou uživatelem, kterou Application Gateway používá k načtení certifikátů od Key Vault vaším jménem. Další informace najdete v tématu [Vytvoření, vypsání, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Tento krok vytvoří novou identitu v tenantovi Azure Active Directory. Identita je důvěryhodná pro předplatné, které se používá k vytvoření identity.

1. **Konfigurace trezoru klíčů**

   Pak buď importujte existující certifikát, nebo vytvořte nový v trezoru klíčů. Certifikát budou používat aplikace, které běží přes Aplikační bránu. V tomto kroku můžete použít taky Key Vault tajný klíč, který taky umožňuje ukládat soubor PFX s kódováním bez hesla, který je v kódování Base-64. Pro možnost autorenew, která je k dispozici pro tento typ objektů v Key Vault, doporučujeme použít typ certifikát. Po vytvoření certifikátu nebo tajného klíče musíte v Key Vault definovat zásady přístupu, aby bylo možné udělit identitě přístup k tajným klíčům.
   
   > [!IMPORTANT]
   > Od 15. března 2021 Key Vault rozpozná Azure Application Gateway jako jednu z důvěryhodných služeb, což vám umožní vytvořit zabezpečenou hranici sítě v Azure. Díky tomu máte možnost Odepřít přístup k provozu ze všech sítí (včetně internetového provozu), abyste Key Vault, ale pořád ho zpřístupnili pro Application Gateway prostředků v rámci vašeho předplatného. 

   > Následujícím způsobem můžete Application Gateway nakonfigurovat v omezené síti Key Vault. <br />
   > a) v části okno síť Key Vault. <br />
   > b) na kartě Brána firewall a virtuální sítě vyberte možnost privátní koncový bod a vybrané sítě. <br/>
   > c) pak pomocí služby Virtual Networks přidejte virtuální síť a podsíť vaší Application Gateway. Během procesu také nakonfigurujte koncový bod služby Microsoft. klíčů trezoru tak, že vyberete jeho zaškrtávací políčko. <br/>
   > d) nakonec vyberte Ano, pokud chcete, aby důvěryhodné služby mohly obejít bránu firewall Key Vault. <br/>
   > 
   > ![Key Vault firewall](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Pokud službu Application Gateway nasadíte přes šablonu ARM, ať už pomocí Azure CLI nebo PowerShellu, nebo přes aplikaci Azure nasazenou z Azure Portal, certifikát SSL je uložený v trezoru klíčů jako soubor PFX s kódováním base64. Musíte dokončit kroky v části [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > Je zvláště důležité nastavit `enabledForTemplateDeployment` na `true` . Certifikát může být neheslem nebo může obsahovat heslo. V případě certifikátu s heslem následující příklad ukazuje možnou konfiguraci pro `sslCertificates` položku v `properties` konfiguraci šablony ARM pro bránu App Gateway. Hodnoty `appGatewaySSLCertificateData` a `appGatewaySSLCertificatePassword` jsou vyhledány z trezoru klíčů, jak je popsáno v části [referenční tajné klíče s dynamickým ID](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Pokud `parameters('secretName')` chcete zjistit, jak se vyhledávání děje, postupujte podle odkazů zpět z. Pokud certifikát není bez hesla, vynechejte `password` položku.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Nakonfigurujte aplikační bránu**

   Po dokončení obou předchozích kroků můžete nastavit nebo upravit existující Aplikační bránu tak, aby používala uživatelsky přiřazenou spravovanou identitu. Další informace najdete v tématu [set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   Certifikát TLS/SSL naslouchacího procesu HTTP můžete nakonfigurovat také tak, aby odkazoval na úplný identifikátor URI Key Vault certifikátu nebo tajného ID.

   ![Certifikáty trezoru klíčů](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další kroky

[Konfigurace ukončení TLS pomocí Key Vault certifikátů pomocí Azure PowerShell](configure-keyvault-ps.md)
