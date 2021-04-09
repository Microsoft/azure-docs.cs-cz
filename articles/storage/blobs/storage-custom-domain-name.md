---
title: Mapování vlastní domény na koncový bod Azure Blob Storage
titleSuffix: Azure Storage
description: Namapujte vlastní doménu na Blob Storage nebo webový koncový bod v účtu služby Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 52fc7b9c1229421fd46b8110857a0a7a8a4f916a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100520421"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapování vlastní domény na koncový bod Azure Blob Storage

Můžete namapovat vlastní doménu na koncový bod služby BLOB Service nebo na koncový bod [statického webu](storage-blob-static-website.md) . 

> [!NOTE] 
> Toto mapování funguje pouze pro subdomény (například: `www.contoso.com` ). Pokud chcete, aby byl váš webový koncový bod dostupný v kořenové doméně (například: `contoso.com` ), budete muset použít Azure CDN. Pokyny najdete v části [Mapování vlastní domény s povoleným protokolem HTTPS](#enable-https) v tomto článku. Vzhledem k tomu, že v této části tohoto článku budete moci povolit kořenovou doménu vlastní domény, je krok v této části pro povolení protokolu HTTPS volitelný. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapování vlastní domény s povoleným pouze protokolem HTTP

Tento přístup je jednodušší, ale umožňuje jenom přístup přes protokol HTTP. Pokud je účet úložiště nakonfigurovaný tak, aby [vyžadoval zabezpečený přenos](../common/storage-require-secure-transfer.md) přes protokol HTTPS, musíte povolit přístup HTTPS pro vaši vlastní doménu. 

Pokud chcete povolit přístup pomocí protokolu HTTPS, přečtěte si část [Mapování vlastní domény s povoleným protokolem HTTPS](#enable-https) v tomto článku. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mapování vlastní domény

> [!IMPORTANT]
> Vaše vlastní doména bude krátce k dispozici uživatelům, zatímco jste dokončili konfiguraci. Pokud vaše doména aktuálně podporuje aplikaci se smlouvou o úrovni služeb (SLA), která vyžaduje žádné výpadky, postupujte podle kroků v části [Mapa vlastní domény s nulovými výpadky](#zero-down-time) tohoto článku, abyste zajistili, že uživatelé budou mít k doméně přístup, když dojde k mapování DNS.

Pokud se nerozhodnete, že je doména krátce nedostupná pro vaše uživatele, postupujte podle těchto kroků.

: heavy_check_mark: Krok 1: Získejte název hostitele vašeho koncového bodu úložiště.

: heavy_check_mark: Krok 2: vytvoření záznamu kanonického názvu (CNAME) u vašeho poskytovatele domény.

: heavy_check_mark: Krok 3: Registrace vlastní domény pomocí Azure. 

: heavy_check_mark: Krok 4: Testování vlastní domény.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1: získání názvu hostitele vašeho koncového bodu úložiště 

Název hostitele je adresa URL koncového bodu úložiště bez identifikátoru protokolu a koncového lomítka. 

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

2. V podokně nabídky v části **Nastavení** vyberte **vlastnosti**.  

3. Zkopírujte hodnotu **primárního koncového bodu služby BLOB Service** nebo **primárního koncového bodu statického webu** do textového souboru. 
  
   > [!NOTE]
   > Koncový bod služby Data Lake Storage není podporován (například: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Odeberte identifikátor protokolu (například: `HTTPS` ) a koncové lomítko z daného řetězce. Následující tabulka obsahuje příklady.

   | Typ koncového bodu |  endpoint | název hostitele |
   |------------|-----------------|-------------------|
   |blob Service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statická webová stránka  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Tuto hodnotu nastavte pro pozdější verzi.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2: vytvoření záznamu kanonického názvu (CNAME) u vašeho poskytovatele domény

Vytvořte záznam CNAME, který bude odkazovat na název hostitele. Záznam CNAME je typ záznamu DNS (Domain Name System), který mapuje název zdrojové domény na název cílové domény.

1. Přihlaste se k webu vašeho doménového registrátora a pak na stránce pro správu nastavení DNS.

   Stránku můžete najít v části s názvem **název domény**, **DNS** nebo **Správa názvového serveru**.

2. Vyhledejte část pro správu záznamů CNAME. 

   Možná budete muset přejít na stránku pokročilá nastavení a vyhledat **CNAME**, **alias** nebo **subdomény**.

3. Vytvoří záznam CNAME. Jako součást tohoto záznamu zadejte následující položky: 

   - Alias subdomény, jako je například `www` nebo `photos` . Subdoména je povinná, kořenové domény se nepodporují. 
      
   - Název hostitele, který jste získali v části [získání názvu hostitele vašeho koncového bodu úložiště](#endpoint) výše v tomto článku. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Krok 3: Registrace vlastní domény pomocí Azure

##### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

2. V podokně nabídek v části **BLOB Service** vyberte **vlastní doména**.

   > [!NOTE]
   > Tato možnost se nezobrazí v účtech, které mají povolenou funkci hierarchického oboru názvů. Pro tyto účty použijte PowerShell nebo rozhraní příkazového řádku Azure CLI k dokončení tohoto kroku.

   ![možnost vlastní domény](./media/storage-custom-domain-name/custom-domain-button.png "vlastní doména")

   Otevře se podokno **vlastní doména** .

3. Do textového pole **název domény** zadejte název vlastní domény, včetně subdomény.  
   
   Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte `www.contoso.com` . Pokud je vaše subdoména *fotografie*, zadejte `photos.contoso.com` .

4. Pokud chcete zaregistrovat vlastní doménu, klikněte na tlačítko **Uložit** .

   Po šíření záznamu CNAME prostřednictvím služby DNS (Domain Name Server) a pokud mají vaši uživatelé příslušná oprávnění, mohou zobrazit data objektů BLOB pomocí vlastní domény.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Spusťte následující příkaz PowerShellu

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- `<resource-group-name>`Zástupný symbol nahraďte názvem skupiny prostředků.

- `<storage-account-name>`Zástupný symbol nahraďte názvem účtu úložiště.

- `<custom-domain-name>`Zástupný text nahraďte názvem vlastní domény, včetně subdomény.

  Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte `www.contoso.com` . Pokud je vaše subdoména *fotografie*, zadejte `photos.contoso.com` .

Po šíření záznamu CNAME prostřednictvím služby DNS (Domain Name Server) a pokud mají vaši uživatelé příslušná oprávnění, mohou zobrazit data objektů BLOB pomocí vlastní domény.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Spusťte následující příkaz PowerShellu

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- `<resource-group-name>`Zástupný symbol nahraďte názvem skupiny prostředků.

- `<storage-account-name>`Zástupný symbol nahraďte názvem účtu úložiště.

- `<custom-domain-name>`Zástupný text nahraďte názvem vlastní domény, včetně subdomény.

  Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte `www.contoso.com` . Pokud je vaše subdoména *fotografie*, zadejte `photos.contoso.com` .

Po šíření záznamu CNAME prostřednictvím služby DNS (Domain Name Server) a pokud mají vaši uživatelé příslušná oprávnění, mohou zobrazit data objektů BLOB pomocí vlastní domény.

---

#### <a name="step-4-test-your-custom-domain"></a>Krok 4: Testování vlastní domény

Pokud chcete potvrdit, že je vaše vlastní doména namapovaná na koncový bod služby BLOB Service, vytvořte v rámci svého účtu úložiště objekt BLOB ve veřejném kontejneru. Pak ve webovém prohlížeči přejděte k objektu BLOB pomocí identifikátoru URI v následujícím formátu: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Například pro přístup k webovému formuláři v `myforms` kontejneru ve vlastní subdoméně *photos.contoso.com* můžete použít následující identifikátor URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapování vlastní domény s nulovými výpadky

> [!NOTE]
> Pokud se nerozhodnete, že je doména krátce nedostupná pro vaše uživatele, zvažte použití kroků v části [Mapování vlastní domény](#map-a-domain) v tomto článku. Jednodušším řešením je méně kroků.  

Pokud vaše doména aktuálně podporuje aplikaci se smlouvou o úrovni služeb (SLA), která vyžaduje žádné výpadky, postupujte podle těchto kroků, abyste zajistili, že uživatelé budou mít přístup k vaší doméně, zatímco probíhá mapování DNS. 

: heavy_check_mark: Krok 1: Získejte název hostitele vašeho koncového bodu úložiště.

: heavy_check_mark: Krok 2: vytvořte záznam zprostředkujícího názvu (CNAME) s vaším poskytovatelem domény.

: heavy_check_mark: Krok 3: předem Zaregistrujte vlastní doménu pomocí Azure.

: heavy_check_mark: Krok 4: vytvořte záznam CNAME s vaším poskytovatelem domény.

: heavy_check_mark: Krok 5: Testování vlastní domény.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1: získání názvu hostitele vašeho koncového bodu úložiště 

Název hostitele je adresa URL koncového bodu úložiště bez identifikátoru protokolu a koncového lomítka. 

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

2. V podokně nabídky v části **Nastavení** vyberte **vlastnosti**.  

3. Zkopírujte hodnotu **primárního koncového bodu služby BLOB Service** nebo **primárního koncového bodu statického webu** do textového souboru. 

   > [!NOTE]
   > Koncový bod služby Data Lake Storage není podporován (například: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Odeberte identifikátor protokolu (například: `HTTPS` ) a koncové lomítko z daného řetězce. Následující tabulka obsahuje příklady.

   | Typ koncového bodu |  endpoint | název hostitele |
   |------------|-----------------|-------------------|
   |blob Service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statická webová stránka  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Tuto hodnotu nastavte pro pozdější verzi.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2: vytvoření záznamu zprostředkujícího názvu (CNAME) s vaším poskytovatelem domény

Vytvořte dočasný záznam CNAME, který bude odkazovat na název hostitele. Záznam CNAME je typem záznamu DNS, který mapuje zdrojový název domény na cílový název domény.

1. Přihlaste se k webu vašeho doménového registrátora a pak na stránce pro správu nastavení DNS.

   Stránku můžete najít v části s názvem **název domény**, **DNS** nebo **Správa názvového serveru**.

2. Vyhledejte část pro správu záznamů CNAME. 

   Možná budete muset přejít na stránku pokročilá nastavení a vyhledat **CNAME**, **alias** nebo **subdomény**.

3. Vytvoří záznam CNAME. Jako součást tohoto záznamu zadejte následující položky: 

   - Alias subdomény, jako je například `www` nebo `photos` . Subdoména je povinná, kořenové domény se nepodporují.

     Přidejte `asverify` subdoménu do aliasu. Například: `asverify.www` nebo `asverify.photos` .
       
   - Název hostitele, který jste získali v části [získání názvu hostitele vašeho koncového bodu úložiště](#endpoint) výše v tomto článku. 

     Přidejte subdoménu `asverify` do názvu hostitele. Příklad: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Krok 3: Předregistrace vlastní domény pomocí Azure

Když předem zaregistrujete vlastní doménu pomocí Azure, umožníte Azure rozpoznávat vlastní doménu, aniž byste museli upravovat záznam DNS pro doménu. Tímto způsobem se při úpravě záznamu DNS pro doménu namapuje na koncový bod objektu BLOB bez výpadků.

##### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

2. V podokně nabídek v části **BLOB Service** vyberte **vlastní doména**.

   > [!NOTE]
   > Tato možnost se nezobrazí v účtech, které mají povolenou funkci hierarchického oboru názvů. Pro tyto účty použijte PowerShell nebo rozhraní příkazového řádku Azure CLI k dokončení tohoto kroku.

   ![možnost vlastní domény](./media/storage-custom-domain-name/custom-domain-button.png "vlastní doména")

   Otevře se podokno **vlastní doména** .

3. Do textového pole **název domény** zadejte název vlastní domény, včetně subdomény.  
   
   Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte `www.contoso.com` . Pokud je vaše subdoména *fotografie*, zadejte `photos.contoso.com` .

4. Zaškrtněte políčko **použít nepřímé ověřování CNAME** .

5. Pokud chcete zaregistrovat vlastní doménu, klikněte na tlačítko **Uložit** .
  
   Pokud je registrace úspěšná, portál vás upozorní, že váš účet úložiště se úspěšně aktualizoval. Vaše vlastní doména byla ověřena v Azure, ale provoz do vaší domény ještě není směrován do svého účtu úložiště, dokud nevytvoříte záznam CNAME s vaším poskytovatelem domény. Provedete to v další části.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Spusťte následující příkaz PowerShellu

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- `<resource-group-name>`Zástupný symbol nahraďte názvem skupiny prostředků.

- `<storage-account-name>`Zástupný symbol nahraďte názvem účtu úložiště.

- `<custom-domain-name>`Zástupný text nahraďte názvem vlastní domény, včetně subdomény.

  Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte `www.contoso.com` . Pokud je vaše subdoména *fotografie*, zadejte `photos.contoso.com` .

Provoz do vaší domény ještě není směrován do vašeho účtu úložiště, dokud nevytvoříte záznam CNAME s vaším poskytovatelem domény. Provedete to v další části.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Spusťte následující příkaz PowerShellu

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- `<resource-group-name>`Zástupný symbol nahraďte názvem skupiny prostředků.

- `<storage-account-name>`Zástupný symbol nahraďte názvem účtu úložiště.

- `<custom-domain-name>`Zástupný text nahraďte názvem vlastní domény, včetně subdomény.

  Pokud je vaše doména například *contoso.com* a alias subdomény je *www*, zadejte `www.contoso.com` . Pokud je vaše subdoména *fotografie*, zadejte `photos.contoso.com` .

Provoz do vaší domény ještě není směrován do vašeho účtu úložiště, dokud nevytvoříte záznam CNAME s vaším poskytovatelem domény. Provedete to v další části.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Krok 4: vytvoření záznamu CNAME s vaším poskytovatelem domény

Vytvořte dočasný záznam CNAME, který bude odkazovat na název hostitele.

1. Přihlaste se k webu vašeho doménového registrátora a pak na stránce pro správu nastavení DNS.

   Stránku můžete najít v části s názvem **název domény**, **DNS** nebo **Správa názvového serveru**.

2. Vyhledejte část pro správu záznamů CNAME. 

   Možná budete muset přejít na stránku pokročilá nastavení a vyhledat **CNAME**, **alias** nebo **subdomény**.

3. Vytvoří záznam CNAME. Jako součást tohoto záznamu zadejte následující položky: 

   - Alias subdomény, jako je například `www` nebo `photos` . Subdoména je povinná, kořenové domény se nepodporují.
      
   - Název hostitele, který jste získali v části [získání názvu hostitele vašeho koncového bodu úložiště](#endpoint-2) výše v tomto článku. 

#### <a name="step-5-test-your-custom-domain"></a>Krok 5: Testování vlastní domény

Pokud chcete potvrdit, že je vaše vlastní doména namapovaná na koncový bod služby BLOB Service, vytvořte v rámci svého účtu úložiště objekt BLOB ve veřejném kontejneru. Pak ve webovém prohlížeči přejděte k objektu BLOB pomocí identifikátoru URI v následujícím formátu: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Například pro přístup k webovému formuláři v `myforms` kontejneru ve vlastní subdoméně *photos.contoso.com* můžete použít následující identifikátor URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Odebrání vlastního mapování domény

Chcete-li odebrat vlastní mapování domény, zrušte registraci vlastní domény. Použijte jeden z následujících postupů.

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.

2. V podokně nabídek v části **BLOB Service** vyberte **vlastní doména**.  
   Otevře se podokno **vlastní doména** .

3. Vymažte obsah textového pole, které obsahuje vlastní název domény.

4. Vyberte tlačítko **Uložit**.

Po úspěšném odebrání vlastní domény se zobrazí oznámení na portálu, že váš účet úložiště se úspěšně aktualizoval.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete odebrat vlastní registraci domény, použijte rutinu [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) prostředí PowerShell a pak zadejte prázdný řetězec ( `""` ) pro `-CustomDomainName` hodnotu argumentu.

* Formát příkazu:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Příklad příkazu:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odebrat vlastní registraci domény, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account) CLI a pak zadejte prázdný řetězec ( `""` ) pro `--custom-domain` hodnotu argumentu.

* Formát příkazu:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Příklad příkazu:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapování vlastní domény s povoleným protokolem HTTPS

Tento přístup zahrnuje více kroků, ale umožňuje přístup pomocí protokolu HTTPS. 

Pokud nepotřebujete, aby uživatelé měli přístup k objektu BLOB nebo webovému obsahu pomocí protokolu HTTPS, přečtěte si část [Mapování vlastní domény s povoleným pouze protokolem HTTP](#enable-http) tohoto článku. 

1. Povolte [Azure CDN](../../cdn/cdn-overview.md) v objektu BLOB nebo na koncovém bodu webu. 

   Blob Storage koncový bod najdete v tématu [integrace účtu Azure Storage s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Pro statický koncový bod webu najdete informace v tématu [integrace statického webu s Azure CDN](static-website-content-delivery-network.md).

2. [Namapujte Azure CDN obsahu na vlastní doménu](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Povolte https u Azure CDN vlastní domény](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Když aktualizujete svůj statický web, nezapomeňte vymazat obsah uložený v mezipaměti na hraničních serverech CDN vyprázdněním koncového bodu CDN. Další informace najdete v tématu [Vyprázdnění koncového bodu Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. Volitelné Přečtěte si následující pokyny:

   * [Tokeny sdíleného přístupového podpisu (SAS) s Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Přesměrování HTTP na https s Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Ceny a fakturace při použití BLOB Storage s Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

## <a name="next-steps"></a>Další kroky

* [Seznamte se s hostováním statických webů ve službě Azure Blob Storage.](storage-blob-static-website.md)