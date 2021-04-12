---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa certifikátů
description: Naučte se vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2026b1721dbe05af1ecc52324d1dd0ebf2ec3d08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802654"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Rychlý Start: Azure Key Vault klientské knihovny certifikátů pro Python

Začněte s klientskou knihovnou certifikátů Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Když použijete Key Vault k ukládání certifikátů, vyhnete se ukládání certifikátů v kódu, což zvyšuje zabezpečení vaší aplikace.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-certificates-readme)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + nebo 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

V tomto rychlém startu se předpokládá, že používáte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) v okně terminálu Linux.

## <a name="set-up-your-local-environment"></a>Nastavení místního prostředí

Tento rychlý Start používá Azure identity Library s Azure CLI k ověřování uživatele ve službách Azure. Vývojáři můžou k ověřování hovorů použít taky Visual Studio nebo Visual Studio Code, další informace najdete v tématu [ověření klienta pomocí klientské knihovny Azure identity](/python/api/overview/azure/identity-readme) .

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

### <a name="install-the-packages"></a>Nainstalovat balíčky

1. V terminálu nebo na příkazovém řádku vytvořte vhodnou složku projektu a pak vytvořte a aktivujte virtuální prostředí Pythonu, jak je popsáno v tématu [použití virtuálních prostředí Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) .

1. Nainstalujte Azure Active Directory knihovnu identit:

    ```terminal
    pip install azure.identity
    ```


1. Instalace klientské knihovny Key Vault certifikátů:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění certifikátu vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Tato aplikace používá název trezoru klíčů jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS nebo Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Vytvoření ukázkového kódu

Klientská knihovna certifikátů Azure Key Vault pro Python umožňuje správu certifikátů. Následující příklad kódu ukazuje, jak vytvořit klienta, nastavit certifikát, načíst certifikát a odstranit certifikát.

Vytvořte soubor s názvem *kv_certificates. py* , který obsahuje tento kód.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Spuštění kódu

Ujistěte se, že kód v předchozí části je v souboru s názvem *kv_certificates. py*. Pak spusťte kód pomocí následujícího příkazu:

```terminal
python kv_certificates.py
```

- Pokud dojde k chybám oprávnění, ujistěte se, že jste spustili [ `az keyvault set-policy` příkaz](#grant-access-to-your-key-vault).
- Opětovné spuštění kódu se stejným názvem klíče může způsobit chybu. certifikát "(konflikt) <name> je momentálně v odstraněném stavu, ale obnovený stav." Použijte jiný název klíče.

## <a name="code-details"></a>Podrobnosti o kódu

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu  [' DefaultAzureCredential () '](/python/api/azure-identity/azure.identity.defaultazurecredential) , která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace najdete v tématu [výchozí ověřování pověření Azure](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Uložení certifikátu

Po získání objektu klienta pro Trezor klíčů můžete vytvořit certifikát pomocí metody [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

V tomto případě certifikát vyžaduje zásadu získanou pomocí metody [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) .

Volání `begin_create_certificate` metody generuje asynchronní volání do Azure REST API pro Trezor klíčů. Asynchronní volání vrátí objekt cyklického dotazování. Chcete-li počkat na výsledek operace, zavolejte metodu pro hlasování `result` .

Při zpracování žádosti Azure ověřuje identitu volajícího (instanční objekt) pomocí objektu přihlašovacích údajů, který jste zadali klientovi.


### <a name="retrieve-a-certificate"></a>Načtení certifikátu

Chcete-li číst certifikát z Key Vault, použijte metodu [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Můžete také ověřit, že se certifikát nastavil pomocí příkazu Azure CLI AZ klíčů [trezor Certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Odstranit certifikát

Chcete-li odstranit certifikát, použijte metodu [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Metoda je asynchronní a vrací objekt cyklického dotazování. Volání metody cyklického dotazování `result` čeká na jeho dokončení.

Můžete ověřit, že se certifikát odstranil pomocí příkazu Azure CLI AZ klíčů [trezor Certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

Po odstranění zůstane certifikát uložený v odstraněném stavu, ale po dobu nepůjde obnovit. Pokud kód znovu spustíte, použijte jiný název certifikátu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete také experimentovat s [tajnými](../secrets/quick-create-python.md) [kódy a klíči](../keys/quick-create-python.md), můžete znovu použít Key Vault vytvořená v tomto článku.

V opačném případě, až budete hotovi s prostředky vytvořenými v tomto článku, odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](../general/overview.md)
- [Zabezpečení přístupu k trezoru klíčů](../general/secure-your-key-vault.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Přehled zabezpečení Key Vault](../general/security-overview.md)
- [Ověřování pomocí Key Vault](../general/authentication.md)
