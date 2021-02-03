---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491003"
---
#### <a name="requirements-for-key-vault-firewall"></a>Požadavky na bránu Key Vault firewall

Pokud je v trezoru klíčů zapnutá [Brána firewall Key Vault](../articles/key-vault/general/network-security.md) , jsou zde další požadavky:

* Pro přístup k trezoru klíčů je nutné použít spravovanou identitu **přiřazenou systémem** API Management instance.
* V bráně Key Vault firewall povolte možnost **Povolit používání této brány firewall důvěryhodným službám Microsoftu** .

#### <a name="virtual-network-requirements"></a>Požadavky na virtuální síť

Pokud je instance API Management nasazená ve virtuální síti, nakonfigurujte taky následující nastavení sítě:

* Povolení [koncového bodu služby](../articles/key-vault/general/overview-vnet-service-endpoints.md) Azure Key Vault v podsíti API Management.
* Nakonfigurujte pravidlo skupiny zabezpečení sítě (NSG) tak, aby povolovalo odchozí přenosy do [značek služby](../articles/virtual-network/service-tags-overview.md)AzureKeyVault a azureactivedirectory selhala. 

Podrobnosti najdete v tématu podrobnosti o konfiguraci sítě v tématu [připojení k virtuální síti](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).