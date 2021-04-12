---
title: Integrace služby Key Vault s certifikační autoritou DigiCert
description: Jak integrovat Key Vault s certifikační autoritou DigiCert
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: c36353448c140450044f352062c3349939e3f7b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789006"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrace služby Key Vault s certifikační autoritou DigiCert

Azure Key Vault umožňuje snadno zřídit, spravovat a nasazovat digitální certifikáty pro vaši síť a povolit zabezpečenou komunikaci pro aplikace. Digitální certifikát je elektronické přihlašovací údaje, které slouží k vytvoření důkazu identity v elektronické transakci. 

Uživatelé služby Azure Key Recovery můžou certifikáty DigiCert vygenerovat přímo z jejich Key Vault. Key Vault by zajistilo ucelenou správu životního cyklu certifikátů pro certifikáty vydávané DigiCert prostřednictvím důvěryhodného partnerství Key Vault s certifikační autoritou DigiCert.

Obecnější informace o certifikátech najdete v tématu [Azure Key Vault certifikátů](./about-certificates.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

K dokončení této příručky musíte mít následující prostředky.
* Trezor klíčů. Můžete použít existující Trezor klíčů nebo vytvořit nový pomocí následujících kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí Azure CLI](../general/quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShell](../general/quick-create-powershell.md)
   - [Vytvořte Trezor klíčů pomocí Azure Portal](../general/quick-create-portal.md).
*   Musíte aktivovat účet DigiCert CertCentral. [Zaregistrujte](https://www.digicert.com/account/signup/) si účet CertCentral.
*   Oprávnění na úrovni správce v účtech.


### <a name="before-you-begin"></a>Než začnete

Ujistěte se, že máte následující informace užitečné z účtu DigiCert CertCentral:
-   ID účtu CertCentral
-   ID organizace
-   Klíč rozhraní API

## <a name="adding-certificate-authority-in-key-vault"></a>Přidání certifikační autority v Key Vault 
Po shromáždění výše uvedených informací z účtu DigiCert CertCentral teď můžete přidat DigiCert do seznamu certifikačních autorit v trezoru klíčů.

### <a name="azure-portal"></a>portál Azure

1.  Pokud chcete přidat certifikační autoritu DigiCert, přejděte do trezoru klíčů, do kterého chcete přidat DigiCert. 
2.  Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
3.  Vyberte kartu **certifikační autority** . ![ Vybrat certifikační autority](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Vyberte **Přidat** možnost.
 ![Přidat certifikační autority](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Na obrazovce **Vytvoření certifikační autority** vyberte následující hodnoty:
    -   **Název**: Přidejte název vystavitele, který chcete identifikovat. Příklad DigicertCA
    -   **Zprostředkovatel**: z nabídky vyberte DigiCert.
    -   **ID účtu**: Zadejte ID účtu DigiCert CertCentral.
    -   **Heslo účtu**: Zadejte klíč rozhraní API, který jste vygenerovali v účtu DigiCert CertCentral.
    -   **ID organizace**: zadejte OrgID shromážděné z účtu DigiCert CertCentral 
    -   Klikněte na **Vytvořit**.
   
6.  Uvidíte, že v seznamu certifikačních autorit se teď přidalo DigicertCA.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Hostitelé Azure Azure Cloud Shell interaktivní prostředí prostředí, které můžete použít v Azure Portal v samotném prohlížeči.

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell verze modulu 1.0.0 nebo novější. `$PSVersionTable.PSVersion`Pro nalezení verze zadejte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Vytvoření **skupiny prostředků**

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Vytvoření **Key Vault**

Pro váš Trezor klíčů je nutné použít jedinečný název. Tady je název "contoso-trezor", Key Vault v rámci této příručky.

- **Název trezoru** Contoso-trezor.
- **Název skupiny prostředků** ContosoResourceGroup.
- **Umístění** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definujte proměnné pro informace shromážděné z účtu DigiCert CertCentral.

- Definovat proměnnou **ID účtu**
- Definovat proměnnou **ID organizace**
- Definovat **klíčovou proměnnou rozhraní API**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Nastavit **vystavitele** Tím se do trezoru klíčů přidá DigiCert jako certifikační autorita. Další informace o parametrech najdete [tady](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer) .
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Nastavení zásad pro certifikát a vystavení certifikátu** z DigiCert přímo v Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Certifikát byl teď úspěšně vydaný certifikační autoritou DigiCert v zadaném Key Vault prostřednictvím této integrace.

## <a name="troubleshoot"></a>Řešení potíží

Pokud je certifikát vystavený ve Azure Portal stav zakázáno, přejděte k části zobrazení **certifikátu** a zkontrolujte, jestli je v něm zobrazená chybová zpráva DigiCert.

 ![Operace certifikátu](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Chybová zpráva: pro dokončení této žádosti o certifikát prosím proveďte sloučení.
Aby bylo možné dokončit tuto žádost, bude nutné sloučit CSR podepsané certifikační autoritou. [Další informace](./create-certificate-signing-request.md)

Další informace najdete v referenčních informacích o [operacích certifikátu v REST API Key Vault](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

- Můžu vytvořit certifikát se zástupným znakem DigiCert prostřednictvím trezoru klíčů? 
   Ano. Bude záviset na tom, jak jste nakonfigurovali účet DigiCert.
- Jak můžu vytvořit certifikát **ov-SSL nebo EV-SSL** s DigiCert? 
   Trezor klíčů podporuje vytváření certifikátů SSL OV a EV. Při vytváření certifikátu klikněte na Pokročilá konfigurace zásad a pak zadejte typ certifikátu. Podporované hodnoty: OV-SSL, EV-SSL
   
   Tento typ certifikátu byste mohli vytvořit v trezoru klíčů, pokud to váš účet DigiCert povoluje. Pro tento typ certifikátu provádí ověření DigiCert a jejich tým podpory by vám mohl při ověřování probíhat, pokud se ověření nepodaří. Při vytváření certifikátu můžete přidat další informace jejich definováním v tématu Subject.

Příklad
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Je při vytváření certifikátu DigiCert prostřednictvím integrace, která přímo získává certifikát prostřednictvím DigiCert, k dispozici časová prodleva?
   No. Při vytváření certifikátu se jedná o proces ověřování, který může chvíli trvat a toto ověření závisí na procesu, který DigiCert sleduje.


## <a name="next-steps"></a>Další kroky

- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)