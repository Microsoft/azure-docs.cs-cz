---
title: Kurz – aktualizace frekvence automatického střídání certifikátů v Key Vault | Microsoft Docs
description: Kurz ukazující, jak aktualizovat frekvenci automatického otočení certifikátu v Azure Key Vault pomocí Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106801"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Kurz: Konfigurace automatického rotace certifikátu v Key Vault

Azure Key Vault umožňuje snadno zřídit, spravovat a nasazovat digitální certifikáty. Můžou to být veřejné a privátní certifikáty SSL/TLS podepsané certifikační autoritou nebo certifikátem podepsaným svým držitelem. Key Vault taky může vyžádat a obnovovat certifikáty prostřednictvím partnerství s certifikačními autoritami a zajistit tak robustní řešení pro správu životního cyklu certifikátů. V tomto kurzu aktualizujete atributy certifikátu – období platnosti, četnost automatického otočení, certifikační autorita. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Správa certifikátu pomocí Azure Portal
> * Přidat účet poskytovatele certifikační autority
> * Doba platnosti aktualizace certifikátu
> * Interval automatické rotace aktualizace certifikátu
> * Aktualizace atributů certifikátu pomocí Azure PowerShellu


Než začnete, přečtěte si téma [Key Vault Basic koncepty](../general/basic-concepts.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

Vytvořte nebo vyberte existující Key Vault k provádění operací. [(Kroky k vytvoření trezoru klíčů).](../quick-create-portal.md) V tomto příkladu je název trezoru **příklad – trezor**. 

![Výstup po dokončení vytváření služby Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Vytvoření certifikátu v Key Vault

Vytvoří nebo naimportuje certifikát do trezoru. [(Postup vytvoření certifikátu v trezoru klíčů).](../quick-create-portal.md) V tomto případě budeme pracovat na certifikátu s názvem **ExampleCertificate**.

> [!NOTE]
> V Azure Key Vault lze atributy životního cyklu certifikátu aktualizovat jak v době vytváření certifikátu, tak i po jeho vytvoření. 
## <a name="updating-certificates-life-cycle-attributes"></a>Aktualizují se atributy životního cyklu certifikátu.

Certifikát vytvořený v Key Vault může být 
- certifikát podepsaný svým držitelem
- certifikát vytvořený pomocí certifikační autority (CA), která je partnerkou Key Vault
- certifikát s certifikační autoritou, která není partnerkou Key Vault

Následující certifikační autority jsou aktuálně partnerskými poskytovateli s Key Vault:
- DigiCert-Key Vault nabízí OV certifikáty TLS/SSL s DigiCert.
- GlobalSign-Key Vault nabízí OV certifikáty TLS/SSL s GlobalSign.

Azure Key Vault automaticky otočí certifikáty prostřednictvím partnerství s certifikačními autoritami. Prostřednictvím tohoto zavedeného partnerství Key Vault automaticky požadovat a obnovovat certifikáty. Proto **se schopnost automatického otočení nedá použít pro certifikáty vytvořené pomocí certifikačních autorit, které nejsou partnerské Key Vault.** 

> [!NOTE]
> Správce účtu pro poskytovatele certifikační autority vytvoří přihlašovací údaje, které Key Vault použít k vytváření, obnovování a používání certifikátů TLS/SSL prostřednictvím Key Vault.
![Certifikační autorita](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Aktualizují se atributy životního cyklu certifikátu v době vytváření certifikátu.

1. Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **vytvořit certifikát** aktualizujte následující hodnoty:
    

    - **Doba platnosti**: zadejte hodnotu (v měsících). Vytváření krátkodobých certifikátů je doporučeným postupem zabezpečení. Výchozí hodnota platnosti nově vytvořeného certifikátu je 12 měsíců.
    - **Typ akce životního cyklu**: vyberte Automatické obnovení a akci při upozorňování certifikátu. Podle výběru, aktualizujte ' procento životnosti ' nebo ' počet dnů před vypršením platnosti '. Automatické obnovování certifikátu je standardně nastavené na 80% své životnosti.<br> V rozevírací nabídce vyberte možnost:

    |  Automaticky obnovit v daném čase| Poslat všem kontaktům e-mail v daném čase |
    |-----------|------|
    |Výběrem této možnosti se zapne automatické otočení. | Vyberete-li tuto možnost, nebude automaticky otočena, bude upozorněna pouze na kontakty|
        


4. Klikněte na **Vytvořit**.

![Životní cyklus certifikátu](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Aktualizují se atributy životního cyklu uloženého certifikátu.

1. Vyberte Key Vault.
2. Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
3. Vyberte certifikát, který chcete aktualizovat. V tomto případě budeme pracovat s certifikátem s názvem **ExampleCertificate**.
4. V horním řádku nabídek vyberte **zásady vystavování** .

![Vlastnosti certifikátu](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. Na obrazovce **zásady vystavování** aktualizujte následující hodnoty:
- **Doba platnosti**: aktualizace hodnoty (v měsících)
- **Typ akce životního cyklu**: vyberte Automatické obnovení a akci při upozorňování certifikátu. Podle výběru aktualizujte ' procento životnosti ' nebo ' počet dnů před vypršením platnosti '. 

![Vlastnosti certifikátu](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Klikněte na **Uložit**.

> [!IMPORTANT]
> Změna typu akce životního cyklu pro certifikát bude okamžitě zaznamenávat změny stávajících certifikátů.


### <a name="updating-certificates-attributes-using-powershell"></a>Aktualizace atributů certifikátu pomocí PowerShellu

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Úprava zásad obnovení pro seznam certifikátů, vstupní soubor. csv obsahující trezor, CERT <br/>
>  vault1,Cert1 <br/>
>  vault2, Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Další informace o [těchto parametrech](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste aktualizovali životní cyklus certifikátu. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

Přečtěte si další informace o [správě vytváření certifikátů v Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Přečtěte si [přehled Key Vault](../general/overview.md)