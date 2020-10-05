---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa certifikátů
description: Naučte se vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488622"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Rychlý Start: Klientská knihovna pro Azure Key Vault certifikáty pro Python

Začněte s knihovnou klienta Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Když použijete Key Vault k ukládání certifikátů, vyhnete se ukládání certifikátů v kódu, což zvyšuje zabezpečení vaší aplikace.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Nastavení místního prostředí

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Nainstalujte knihovnu Key Vaultch certifikátů:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

Spusťte následující příkaz [AZ klíčů set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) k autorizaci instančního objektu pro operace získání, vypsání a vytvoření na certifikátech.

# <a name="cmd"></a>[přepsat](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Tento příkaz spoléhá na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` proměnné prostředí a vytvořené v předchozích krocích.

Další informace najdete v tématu [přiřazení zásad přístupu – CLI](../general/assign-access-policy-cli.md) .

## <a name="create-the-sample-code"></a>Vytvoření ukázkového kódu

Klientská knihovna Azure Key Vault pro Python umožňuje správu certifikátů a souvisejících prostředků, jako jsou tajné klíče a kryptografické klíče. Následující příklad kódu ukazuje, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

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

- Pokud dojde k chybám oprávnění, ujistěte se, že jste spustili [ `az keyvault set-policy` příkaz](#give-the-service-principal-access-to-your-key-vault).
- Opětovné spuštění kódu se stejným názvem klíče může způsobit chybu. certifikát "(konflikt) <name> je momentálně v odstraněném stavu, ale obnovený stav." Použijte jiný název klíče.

## <a name="code-details"></a>Podrobnosti o kódu

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V předchozím kódu [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objekt používá proměnné prostředí, které jste vytvořili pro objekt služby. Toto přihlašovací údaje zadáte vždy, když vytvoříte objekt klienta z knihovny Azure, například [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python) , společně s identifikátorem URI prostředku, se kterým chcete přes tohoto klienta pracovat:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Uložení certifikátu

Po získání objektu klienta pro Trezor klíčů můžete vytvořit certifikát pomocí metody [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

V tomto případě certifikát vyžaduje zásadu získanou pomocí metody [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--) .

Volání `begin_create_certificate` metody generuje asynchronní volání do Azure REST API pro Trezor klíčů. Asynchronní volání vrátí objekt cyklického dotazování. Chcete-li počkat na výsledek operace, zavolejte metodu pro hlasování `result` .

Při zpracování žádosti Azure ověřuje identitu volajícího (instanční objekt) pomocí objektu přihlašovacích údajů, který jste zadali klientovi.

Také kontroluje, zda je volající autorizován k provedení požadované akce. Tato autorizace k instančnímu objektu byla udělena dříve pomocí [ `az keyvault set-policy` příkazu](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Načtení certifikátu

Chcete-li číst certifikát z Key Vault, použijte metodu [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Můžete také ověřit, že se certifikát nastavil pomocí příkazu Azure CLI AZ klíčů [trezor Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Odstranit certifikát

Chcete-li odstranit certifikát, použijte metodu [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Metoda je asynchronní a vrací objekt cyklického dotazování. Volání metody cyklického dotazování `result` čeká na jeho dokončení.

Můžete ověřit, že se certifikát odstranil pomocí příkazu Azure CLI AZ klíčů [trezor Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

Po odstranění zůstane certifikát uložený v odstraněném stavu, ale po dobu nepůjde obnovit. Pokud kód znovu spustíte, použijte jiný název certifikátu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete také experimentovat s [tajnými](../secrets/quick-create-python.md) [kódy a klíči](../keys/quick-create-python.md), můžete znovu použít Key Vault vytvořená v tomto článku.

V opačném případě, až budete hotovi s prostředky vytvořenými v tomto článku, odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](../general/overview.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Azure Key Vault osvědčené postupy](../general/best-practices.md)
- [Ověřování pomocí Key Vault](../general/authentication.md)
