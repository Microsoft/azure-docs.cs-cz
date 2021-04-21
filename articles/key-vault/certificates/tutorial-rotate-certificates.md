---
title: Kurz – aktualizace frekvence automatického otočení certifikátů v Key Vault | Microsoft Docs
description: Kurz ukazující, jak aktualizovat frekvenci automatického otočení certifikátu v Azure Key Vault pomocí Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 48a531e054bf92c8ddc7761689b8fdf1df8a28a7
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750009"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Kurz: Konfigurace automatického rotace certifikátů v Key Vault

Pomocí Azure Key Vault můžete snadno zřizovat, spravovat a nasazovat digitální certifikáty. Certifikáty mohou být veřejné a privátní SSL (Secure Sockets Layer) (SSL) certifikáty zabezpečení TLS (/Transport Layer Security) podepsané certifikační autoritou (CA) nebo certifikátem podepsaným svým držitelem. Key Vault taky může vyžádat a obnovovat certifikáty prostřednictvím partnerství s CAs a zajistit tak robustní řešení pro správu životního cyklu certifikátů. V tomto kurzu aktualizujete období platnosti certifikátu, frekvenci automatického střídání a atributy CA.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Spravujte certifikát pomocí Azure Portal.
> * Přidejte účet poskytovatele certifikační autority.
> * Aktualizujte období platnosti certifikátu.
> * Aktualizujte frekvenci automatického otočení certifikátu.
> * Aktualizujte atributy certifikátu pomocí Azure PowerShell.

Než začnete, přečtěte si téma [Key Vault Basic koncepty](../general/basic-concepts.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

Vytvořte Azure Key Vault pomocí [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md). V tomto příkladu je název trezoru klíčů **příkladem trezoru**.

![Výstup po dokončení vytvoření trezoru klíčů](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Vytvoření certifikátu v Key Vault

Vytvořte certifikát nebo importujte certifikát do trezoru klíčů (viz [Postup vytvoření certifikátu v Key Vault](../secrets/quick-create-portal.md)). V takovém případě budete pracovat s certifikátem s názvem **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Aktualizace atributů životního cyklu certifikátu

V Azure Key Vault můžete aktualizovat atributy životního cyklu certifikátu v době vytváření certifikátu nebo po něm.

Certifikát vytvořený v Key Vault může být:

- Certifikát podepsaný svým držitelem.
- Certifikát vytvořený pomocí certifikační autority, která je partnerkou Key Vault.
- Certifikát s certifikační autoritou, která není partnerkou Key Vault.

Následující certifikační autority jsou aktuálně partnerskými poskytovateli s Key Vault:

- DigiCert: Key Vault nabízí OV certifikáty TLS/SSL.
- GlobalSign: Key Vault nabízí OV certifikáty TLS/SSL.

Key Vault automaticky otočí certifikáty prostřednictvím zavedených partnerství s certifikačními autoritami. Vzhledem k tomu, že Key Vault automaticky žádá a obnoví certifikáty prostřednictvím partnerství, možnosti automatického otočení se nevztahují na certifikáty vytvořené pomocí certifikačních autorit, které nejsou partnerské s Key Vault.

> [!NOTE]
> Správce účtu pro poskytovatele certifikační autority vytvoří přihlašovací údaje, které Key Vault používá k vytváření, obnovování a používání certifikátů TLS/SSL.
![Certifikační autorita](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Aktualizace atributů životního cyklu certifikátu v době vytváření

1. Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
1. Vyberte **Generovat/importovat**.
1. Na obrazovce **vytvořit certifikát** aktualizujte následující hodnoty:

   - **Doba platnosti**: zadejte hodnotu (v měsících). Vytváření krátkodobých certifikátů je doporučeným postupem zabezpečení. Ve výchozím nastavení je hodnota platnosti nově vytvořeného certifikátu 12 měsíců.
   - **Typ akce životního cyklu**: vyberte akci automatického obnovení a upozornění certifikátu a pak aktualizujte **procentuální hodnotu životnosti** nebo **počet dní před vypršením platnosti**. Ve výchozím nastavení je automatické obnovení certifikátu nastavené na 80% své životnosti. V rozevírací nabídce vyberte jednu z následujících možností.

      |  Automaticky obnovit v daném čase| Poslat všem kontaktům e-mail v daném čase |
      |-----------|------|
      |Výběrem této možnosti se *zapne* automatické otočení. | Vyberete-li tuto možnost, *nedojde* k automatickému otočení, ale budete upozorněni pouze na kontakty.|
      
      Informace o [nastavení kontaktu e-mailu najdete tady](https://docs.microsoft.com/azure/key-vault/certificates/overview-renew-certificate#get-notified-about-certificate-expiration) .

1. Vyberte **Vytvořit**.

![Životní cyklus certifikátů](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Aktualizovat atributy životního cyklu uloženého certifikátu

1. Vyberte Trezor klíčů.
1. Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
1. Vyberte certifikát, který chcete aktualizovat. V takovém případě budete pracovat s certifikátem s názvem **ExampleCertificate**.
1. V horním řádku nabídek vyberte **zásady vystavování** .

   ![Snímek obrazovky, který zvýrazní tlačítko Zásady vystavování.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Na obrazovce **zásady vystavování** aktualizujte následující hodnoty:

   - **Doba platnosti**: Aktualizujte hodnotu (v měsících).
   - **Typ akce životního cyklu**: vyberte akci automatického obnovení a upozorňování certifikátu a potom aktualizujte **procentuální hodnotu životnosti** nebo **počet dnů před vypršením platnosti**.

   ![Vlastnosti certifikátu](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Vyberte **Uložit**.

> [!IMPORTANT]
> Změna typu akce životního cyklu pro certifikát bude okamžitě zaznamenávat změny stávajících certifikátů.


### <a name="update-certificate-attributes-by-using-powershell"></a>Aktualizace atributů certifikátu pomocí PowerShellu

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Chcete-li upravit zásady obnovování pro seznam certifikátů, zadejte, který `File.csv` obsahuje, `VaultName,CertName` jako v následujícím příkladu:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Další informace o parametrech najdete v tématu [AZ datatrezor Certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se sestavují další kurzy Key Vault. Pokud plánujete, že budete s těmito kurzy pracovat, můžete chtít ponechat tyto existující prostředky na místě.
Pokud je už nepotřebujete, odstraňte skupinu prostředků, která odstraní Trezor klíčů a související prostředky.

Odstranění skupiny prostředků pomocí portálu:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků. Pokud se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
1. Vyberte **Odstranit skupinu prostředků**.
1. Do pole **Zadejte název skupiny prostředků:** zadejte název skupiny prostředků a pak vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste aktualizovali atributy životního cyklu certifikátu. Pokud chcete získat další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, pokračujte v následujících článcích:

- Přečtěte si další informace o [správě vytváření certifikátů v Azure Key Vault](./create-certificate-scenarios.md).
- Přečtěte si [přehled Key Vault](../general/overview.md).
