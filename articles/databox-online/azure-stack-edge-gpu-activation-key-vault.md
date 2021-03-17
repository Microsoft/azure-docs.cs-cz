---
title: Integrace Azure Key Vault s využitím prostředků Azure Stack Edge a aktivace zařízení
description: Popisuje způsob, jakým je při aktivaci zařízení Azure Stack Edge pro Azure Key Vault spojená se správou tajných kódů.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c18f875dde43ab6df512375152d76a31c30a9e77
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439863"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Key Vault integrace s Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault je integrován s Azure Stackm hraničním prostředkem pro správu tajných kódů. Tento článek poskytuje podrobné informace o tom, jak je vytvořený Azure Key Vault pro Azure Stack hraniční prostředek během aktivace zařízení a který se pak používá ke správě tajných kódů. 


## <a name="about-key-vault-and-azure-stack-edge"></a>O trezoru klíčů a Azure Stack Edge

Cloudová služba Azure Key Vault slouží k bezpečnému ukládání a řízení přístupu k tokenům, heslům, certifikátům, klíčům rozhraní API a dalším tajným klíčům. Key Vault také usnadňuje vytváření a řízení šifrovacích klíčů používaných k šifrování vašich dat. Další informace o povolených transakcích a odpovídajících nákladech najdete v tématu [ceny za Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

V případě služby Azure Stack Edge je jedním z používaných tajných kódů klíč integrity kanálu (CIK). Tento klíč umožňuje šifrování tajných klíčů. Díky integraci trezoru klíčů je CIK bezpečně uložený v trezoru klíčů. Další informace najdete v tématu [bezpečné ukládání tajných kódů a klíčů](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Vytvoření trezoru klíčů

Pro Azure Stack hraničního prostředku se během procesu generování aktivačního klíče vytvoří Trezor klíčů. 

- Když vytvoříte prostředek Azure Stack Edge, budete muset zaregistrovat poskytovatele prostředků *Microsoft.* klíčů. Poskytovatel prostředků se automaticky zaregistruje, pokud máte k předplatnému oprávnění vlastníka nebo přispěvatele. Trezor klíčů se vytvoří ve stejném předplatném a skupině prostředků jako prostředek Azure Stack Edge. 

- Když vytvoříte prostředek Azure Stack Edge, vytvoří se také Identita spravované služby (MSI), který bude nadále trvat po dobu životnosti prostředku a komunikuje s poskytovatelem prostředků v cloudu. 

    Když je soubor MSI povolený, vytvoří Azure důvěryhodnou identitu pro prostředek Azure Stack Edge.

- Po vytvoření Azure Stack hraničního prostředku a vygenerování aktivačního klíče z Azure Portal se vytvoří Trezor klíčů. Tento Trezor klíčů se používá ke správě tajných kódů a trvá po dobu, kdy existuje Azure Stack hraniční prostředek. 

    ![Key Vault vytvořena během generování aktivačního klíče.](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Můžete zvolit, že chcete přijmout výchozí název klíče nebo zadat vlastní název trezoru klíčů. Název trezoru klíčů musí být dlouhý 3 až 24 znaků. Nemůžete použít Trezor klíčů, který se už používá. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Služba MSI vytvořená během vytváření Azure Stackch prostředků Edge](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Pokud chcete přejít do trezoru klíčů Azure, přejděte na **vlastnosti** v prostředku Azure Stack Edge a vyberte název trezoru klíčů. 

- Aby se zabránilo nechtěnému odstranění, je v trezoru klíčů povolen zámek prostředků. V trezoru klíčů je taky povolené obnovitelné odstranění, které umožňuje obnovení trezoru klíčů během 90 dnů, pokud dojde k náhodnému odstranění. Další informace najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](../key-vault/general/soft-delete-overview.md) .

    Pokud se Trezor klíčů omylem odstraní a doba trvání ochrany vyprázdnění 90 dnů uplynula, postupujte podle těchto kroků a [obnovte Trezor klíčů](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). 

- Pokud jste už existující prostředek Azure Stack Edge předtím, než se Azure Key Vault integruje s Azure Stackm hraničním prostředkem, nebudete mít vliv na. Stávající Azure Stack hraničního prostředku můžete dál používat. 

- Po odstranění Azure Stackho hraničního prostředku se Azure Key Vault odstraní také pomocí prostředku. Zobrazí se výzva k potvrzení. Pokud tento trezor klíčů nechcete odstranit, můžete se rozhodnout, že nechcete vyjádřit souhlas. Odstraní se jenom zdroj Azure Stack Edge, který ponechá Trezor klíčů beze změn. 

- Pokud se tento trezor klíčů použil k uložení jiných klíčů, můžete ho i nadále obnovit do 90 dnů od odstranění. Během této doby ochrany se název trezoru klíčů nedá použít k vytvoření nového trezoru klíčů.

Pokud narazíte na problémy související s trezorem klíčů a aktivací zařízení, přečtěte si téma [řešení potíží s aktivací zařízení](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [vygenerovat aktivační klíč](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).