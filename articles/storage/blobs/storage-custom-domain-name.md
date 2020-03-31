---
title: Mapování vlastní domény na koncový bod úložiště objektů blob Azure
titleSuffix: Azure Storage
description: Namapujte vlastní doménu na úložiště objektů blob nebo webový koncový bod v účtu úložiště Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370470"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapování vlastní domény na koncový bod úložiště objektů blob Azure

Vlastní doménu můžete namapovat na koncový bod služby blob nebo na statický koncový bod [webu.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Toto mapování funguje pouze pro subdomény (například: `www.contoso.com`). Pokud chcete, aby byl váš webový koncový bod dostupný `contoso.com`v kořenové doméně (například: ), budete muset použít Azure CDN. Pokyny naleznete v části [Mapování vlastní domény s povoleným protokolem HTTPS](#enable-https) v tomto článku. Vzhledem k tomu, že vaše přejde do této části tohoto článku povolit kořenovou doménu vlastní domény, krok v rámci této části pro povolení HTTPS je volitelné. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapování vlastní domény s povoleným pouze protokolem HTTP

Tento přístup je jednodušší, ale umožňuje pouze přístup HTTP. Pokud je účet úložiště nakonfigurován tak, aby [vyžadoval zabezpečený přenos](../common/storage-require-secure-transfer.md) přes protokol HTTPS, musíte povolit přístup HTTPS pro vlastní doménu. 

Pokud chcete povolit přístup https, přečtěte si v tomto článku část [Mapa vlastní domény s povoleným protokolem HTTPS.](#enable-https) 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Mapování vlastní domény

> [!IMPORTANT]
> Vaše vlastní doména bude krátce nedostupná uživatelům při dokončení konfigurace. Pokud vaše doména aktuálně podporuje aplikaci se smlouvou o úrovni služeb (SLA), která vyžaduje nulové prostoje, postupujte podle pokynů v části [Mapovat vlastní doménu s nulovými prostoji](#zero-down-time) v tomto článku, abyste zajistili, že uživatelé budou mít přístup k vaší doméně v době, kdy probíhá mapování DNS.

Pokud se neobáváte, že doména je uživatelům krátce nedostupná, postupujte takto.

:heavy_check_mark: Krok 1: Získejte název hostitele koncového bodu úložiště.

:heavy_check_mark: Krok 2: Vytvořte záznam kanonického názvu (CNAME) u poskytovatele domény.

:heavy_check_mark: Krok 3: Registrace vlastní domény v Azure. 

:heavy_check_mark: Krok 4: Otestujte vlastní doménu.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1: Získání názvu hostitele koncového bodu úložiště 

Název hostitele je adresa URL koncového bodu úložiště bez identifikátoru protokolu a koncovéloho lomítka. 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště.

2. V podokně nabídek vyberte v části **Nastavení** **položku Vlastnosti**.  

3. Zkopírujte hodnotu **koncového bodu primární služby blob nebo** **koncového bodu primárního statického webu** do textového souboru. 

4. Z tohoto řetězce odeberte identifikátor protokolu *(např.* HTTPS) a koncové lomítko. Následující tabulka obsahuje příklady.

   | Typ koncového bodu |  endpoint | název hostitele |
   |------------|-----------------|-------------------|
   |služba objektu blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statická webová stránka  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Nastavte tuto hodnotu stranou na později.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2: Vytvoření záznamu kanonického názvu (CNAME) u poskytovatele domény

Vytvořte záznam CNAME, který bude ukazovat na název hostitele. Záznam CNAME je typem záznamu DNS, který mapuje zdrojový název domény na cílový název domény.

1. Přihlaste se na web svého doménového registrátora a přejděte na stránku pro správu nastavení DNS.

   Stránku můžete najít v části s názvem **Název domény**, **DNS**nebo **Správa názvového serveru**.

2. Vyhledejte oddíl pro správu záznamů CNAME. 

   Možná budete muset přejít na stránku s upřesňujícím nastavením a vyhledat **cname**, **alias**nebo **subdomény**.

3. Vytvořte záznam CNAME. Jako součást tohoto záznamu uveďte následující položky: 

   - Alias subdomény, `www` například nebo `photos`. Subdoména je povinná, kořenové domény nejsou podporovány. 
      
   - Název hostitele, který jste získali v [nabýt název hostitele koncového bodu úložiště](#endpoint) části dříve v tomto článku. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Krok 3: Registrace vlastní domény v Azure

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště.

2. V podokně nabídek vyberte v části **Blob Service**položku **Vlastní doména**.  

   ![možnost vlastní domény](./media/storage-custom-domain-name/custom-domain-button.png "vlastní doména")

   Otevře se podokno **Vlastní doména.**

3. Do textového pole **Název domény** zadejte název vlastní domény, včetně subdomény.  
   
   Pokud je například vaše doména *contoso.com* a alias `www.contoso.com`subdomény *www*, zadejte . Pokud jsou vaší subdoménou *fotografie*, zadejte `photos.contoso.com`.

4. Chcete-li zaregistrovat vlastní doménu, zvolte tlačítko **Uložit.**

   Poté, co se záznam CNAME rozšíří prostřednictvím serverů DNS (Domain Name Servers) a pokud mají uživatelé příslušná oprávnění, mohou data objektů blob zobrazit pomocí vlastní domény.

#### <a name="step-4-test-your-custom-domain"></a>Krok 4: Testování vlastní domény

Pokud chcete potvrdit, že vaše vlastní doména je namapovaná na koncový bod služby blob, vytvořte objekt blob ve veřejném kontejneru v rámci vašeho účtu úložiště. Potom ve webovém prohlížeči přistupovat k objektu blob pomocí identifikátoru URI v následujícím formátu:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Chcete-li například získat přístup k webovému formuláři v kontejneru *myforms* v *photos.contoso.com* vlastní subdoméně, můžete použít následující identifikátor URI:`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapování vlastní domény s nulovými prostoji

> [!NOTE]
> Pokud se neobáváte, že doména je uživatelům krátce nedostupná, zvažte následující kroky v části [Mapovat vlastní doménu](#map-a-domain) v tomto článku. Je to jednodušší přístup s menším počtem kroků.  

Pokud vaše doména aktuálně podporuje aplikaci se smlouvou o úrovni služeb (SLA), která vyžaduje nulové prostoje, postupujte takto, abyste zajistili, že uživatelé budou mít přístup k vaší doméně během mapování DNS. 

:heavy_check_mark: Krok 1: Získejte název hostitele koncového bodu úložiště.

:heavy_check_mark: Krok 2: Vytvořte u svého poskytovatele domény zprostředkující kanonický název (CNAME).

:heavy_check_mark: Krok 3: Předběžná registrace vlastní domény pomocí Azure.

:heavy_check_mark: Krok 4: Vytvořte záznam CNAME u svého poskytovatele domény.

:heavy_check_mark: Krok 5: Otestujte vlastní doménu.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1: Získání názvu hostitele koncového bodu úložiště 

Název hostitele je adresa URL koncového bodu úložiště bez identifikátoru protokolu a koncovéloho lomítka. 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště.

2. V podokně nabídek vyberte v části **Nastavení** **položku Vlastnosti**.  

3. Zkopírujte hodnotu **koncového bodu primární služby blob nebo** **koncového bodu primárního statického webu** do textového souboru. 

4. Z tohoto řetězce odeberte identifikátor protokolu *(např.* HTTPS) a koncové lomítko. Následující tabulka obsahuje příklady.

   | Typ koncového bodu |  endpoint | název hostitele |
   |------------|-----------------|-------------------|
   |služba objektu blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statická webová stránka  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Nastavte tuto hodnotu stranou na později.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2: Vytvoření zprostředkujícího kanonického názvu (CNAME) u poskytovatele domény

Vytvořte dočasný záznam CNAME, který bude ukazovat na název hostitele. Záznam CNAME je typem záznamu DNS, který mapuje zdrojový název domény na cílový název domény.

1. Přihlaste se na web svého doménového registrátora a přejděte na stránku pro správu nastavení DNS.

   Stránku můžete najít v části s názvem **Název domény**, **DNS**nebo **Správa názvového serveru**.

2. Vyhledejte oddíl pro správu záznamů CNAME. 

   Možná budete muset přejít na stránku s upřesňujícím nastavením a vyhledat **cname**, **alias**nebo **subdomény**.

3. Vytvořte záznam CNAME. Jako součást tohoto záznamu uveďte následující položky: 

   - Alias subdomény, `www` například nebo `photos`. Subdoména je povinná, kořenové domény nejsou podporovány.

     Přidejte `asverify` subdoménu do aliasu. Například: `asverify.www` `asverify.photos`nebo .
       
   - Název hostitele, který jste získali v [nabýt název hostitele koncového bodu úložiště](#endpoint) části dříve v tomto článku. 

     Přidejte subdoménu `asverify` do názvu hostitele. Například: `asverify.mystorageaccount.blob.core.windows.net`.

4. Chcete-li zaregistrovat vlastní doménu, zvolte tlačítko **Uložit.**

   Pokud je registrace úspěšná, portál vás upozorní, že váš účet úložiště byl úspěšně aktualizován. Vaše vlastní doména byla ověřena Azure, ale provoz do vaší domény ještě není směrován do vašeho účtu úložiště.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Krok 3: Předběžná registrace vlastní domény pomocí Azure

Když předběžně zaregistrujete vlastní doménu v Azure, povolíte Azure rozpoznat vaši vlastní doménu bez nutnosti úpravy záznamu DNS pro doménu. Tímto způsobem při úpravě záznamu DNS pro doménu, bude namapován na koncový bod objektu blob bez prostojů.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště.

2. V podokně nabídek vyberte v části **Blob Service**položku **Vlastní doména**.  

   ![možnost vlastní domény](./media/storage-custom-domain-name/custom-domain-button.png "vlastní doména")

   Otevře se podokno **Vlastní doména.**

3. Do textového pole **Název domény** zadejte název vlastní domény, včetně subdomény.  
   
   Pokud je například vaše doména *contoso.com* a alias `www.contoso.com`subdomény *www*, zadejte . Pokud jsou vaší subdoménou *fotografie*, zadejte `photos.contoso.com`.

4. Zaškrtněte políčko **Použít nepřímé ověření CNAME.**

5. Chcete-li zaregistrovat vlastní doménu, zvolte tlačítko **Uložit.**
  
   Poté, co se záznam CNAME rozšíří prostřednictvím serverů DNS (Domain Name Servers) a pokud mají uživatelé příslušná oprávnění, mohou data objektů blob zobrazit pomocí vlastní domény.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Krok 4: Vytvoření záznamu CNAME u poskytovatele domény

Vytvořte dočasný záznam CNAME, který bude ukazovat na název hostitele.

1. Přihlaste se na web svého doménového registrátora a přejděte na stránku pro správu nastavení DNS.

   Stránku můžete najít v části s názvem **Název domény**, **DNS**nebo **Správa názvového serveru**.

2. Vyhledejte oddíl pro správu záznamů CNAME. 

   Možná budete muset přejít na stránku s upřesňujícím nastavením a vyhledat **cname**, **alias**nebo **subdomény**.

3. Vytvořte záznam CNAME. Jako součást tohoto záznamu uveďte následující položky: 

   - Alias subdomény, `www` například nebo `photos`. Subdoména je povinná, kořenové domény nejsou podporovány.
      
   - Název hostitele, který jste získali v [nabýt název hostitele koncového bodu úložiště](#endpoint-2) části dříve v tomto článku. 

#### <a name="step-5-test-your-custom-domain"></a>Krok 5: Testování vlastní domény

Pokud chcete potvrdit, že vaše vlastní doména je namapovaná na koncový bod služby blob, vytvořte objekt blob ve veřejném kontejneru v rámci vašeho účtu úložiště. Potom ve webovém prohlížeči přistupovat k objektu blob pomocí identifikátoru URI v následujícím formátu:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Chcete-li například získat přístup k webovému formuláři v kontejneru *myforms* v *photos.contoso.com* vlastní subdoméně, můžete použít následující identifikátor URI:`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Odebrání mapování vlastní domény

Chcete-li odebrat vlastní mapování domény, zrušíte registraci vlastní domény. Použijte jeden z následujících postupů.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

Chcete-li odebrat vlastní nastavení domény, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště.

2. V podokně nabídek vyberte v části **Blob Service**položku **Vlastní doména**.  
   Otevře se podokno **Vlastní doména.**

3. Zrušte zaškrtnutí obsahu textového pole, které obsahuje vlastní název domény.

4. Vyberte tlačítko **Uložit**.

Po úspěšném odebrání vlastní domény se zobrazí oznámení portálu, že váš účet úložiště byl úspěšně aktualizován.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li odebrat vlastní registraci domény, použijte příkaz CLI [pro aktualizaci účtu úložiště a](https://docs.microsoft.com/cli/azure/storage/account) zadejte prázdný řetězec (`""`) pro hodnotu argumentu. `--custom-domain`

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

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li odebrat vlastní registraci domény, použijte rutinu [Nastavení-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell a `-CustomDomainName` pro hodnotu argumentu zadejte prázdný řetězec (`""`).

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
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapování vlastní domény s povoleným protokolem HTTPS

Tento přístup zahrnuje další kroky, ale umožňuje přístup https. 

Pokud nepotřebujete, aby uživatelé přistupovali k objektu blob nebo webovému obsahu pomocí protokolu HTTPS, přečtěte si téma Mapování vlastní domény s pouze povoleným [protokolem HTTP](#enable-http) v tomto článku. 

Chcete-li namapovat vlastní doménu a povolit přístup https, postupujte takto:

1. Povolte [Azure CDN](../../cdn/cdn-overview.md) na objektu blob nebo webového koncového bodu. 

   Koncový bod úložiště objektů blob najdete v [tématu Integrace účtu úložiště Azure s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Koncový bod statického webu najdete [v tématu Integrace statického webu s Azure CDN](static-website-content-delivery-network.md).

2. [Mapujte obsah Azure CDN na vlastní doménu](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Povolte protokol HTTPS na vlastní doméně Azure CDN](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Při aktualizaci statického webu nezapomeňte vymazat obsah uložený v mezipaměti na okrajových serverech CDN vymazáním koncového bodu CDN. Další informace najdete v tématu [Vyprázdnění koncového bodu Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. (Nepovinné) Přečtěte si následující pokyny:

   * [Tokeny sdíleného přístupového podpisu (SAS) s Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Přesměrování HTTP-HTTPS pomocí azure cdn](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Ceny a fakturace při použití úložiště objektů blob s Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Další kroky

* [Informace o statickém hostování webů v úložišti objektů Blob Azure](storage-blob-static-website.md)
