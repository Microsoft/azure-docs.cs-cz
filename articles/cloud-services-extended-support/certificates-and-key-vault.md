---
title: Ukládání a používání certifikátů ve službách Azure Cloud Services (s rozšířenou podporou)
description: Procesy pro ukládání a používání certifikátů v Azure Cloud Services (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 7357703af41afc913ef63dff6ecae3d230c9eca0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583288"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Použití certifikátů s Azure Cloud Services (Rozšířená podpora)

Key Vault slouží k ukládání certifikátů, které jsou přidruženy k Cloud Services (Rozšířená podpora). Trezory klíčů je možné vytvořit prostřednictvím [Azure Portal](../key-vault/general/quick-create-portal.md) a [PowerShellu](../key-vault/general/quick-create-powershell.md). Přidejte certifikáty do Key Vault a pak na ně nastavte odkazy na kryptografické otisky certifikátů v konfiguračním souboru služby. Také je nutné povolit Key Vault pro příslušná oprávnění, aby prostředek Cloud Services (Rozšířená podpora) mohl získat certifikát uložený jako tajné klíče z Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Nahrajte certifikát do Key Vault 

1.  Přihlaste se k Azure Portal a přejděte do Key Vault. Pokud nemáte nastaven Key Vault, můžete ho vytvořit v tomto stejném okně.

2. Vybrat **zásady přístupu**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Image zobrazuje výběr zásad přístupu v okně trezoru klíčů.":::

3. Zajistěte, aby zásady přístupu zahrnovaly následující vlastnosti:
    - **Povolit přístup k Azure Virtual Machines pro nasazení**
    - **Povolit přístup k Azure Resource Manager pro nasazení šablony** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Image zobrazuje okno zásady přístupu v Azure Portal.":::
 
4.  Vybrat **certifikáty** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Image zobrazuje výběr možnosti certifikáty z okna zásady trezoru klíčů v Azure Portal.":::

5. Vybrat **vygenerovat/importovat**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Image zobrazuje výběr možnosti generovat/importovat.":::

4.  Dokončete odeslání certifikátu z požadovaných informací. Certifikát musí být v **. Formát PFX** .

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Obrázek ukazuje import okna v Azure Portal.":::

5.  Přidejte podrobnosti o certifikátu do vaší role v souboru konfigurace služby (. cscfg). Zajistěte, aby kryptografický otisk certifikátu v Azure Portal odpovídal kryptografickému otisku v souboru konfigurace služby (. cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
