---
title: Pomocí Azure CDN SAS | Microsoft Docs
description: Azure CDN podporuje použití z sdíleného přístupového podpisu (SAS) udělit omezený přístup k privátním úložných kontejnerů.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: v-deasim
ms.openlocfilehash: 09efd5cd54fbd05d85939b3ae08bfbb37e91058d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-cdn-with-sas"></a>Pomocí SAS Azure CDN

Když nastavíte účet úložiště pro obsah doručování sítě (CDN) Azure ve výchozím nastavení sloužící k obsahu z mezipaměti, každý uživatel, který zná adresy URL pro vaše kontejnery úložiště přístup k souborům, které jste odeslali. K ochraně souborů ve vašem účtu úložiště, můžete nastavit přístup vaše kontejnery úložiště z veřejné do privátního. Nicméně pokud tak učiníte, nikdo bude možné získat přístup k souborům. 

Pokud chcete udělit omezený přístup k privátním úložných kontejnerů, můžete použít funkci sdíleného přístupového podpisu (SAS) vašeho účtu úložiště Azure. SAS je identifikátor URI, udělí omezený přístupová práva ke svým prostředkům úložiště Azure bez vystavení klíč účtu. Klienti, kteří si nejste jisti, nahraďte svým klíčem účtu úložiště, ale kterým chcete delegovat přístup k určitým prostředkům účet úložiště, můžete zadat SAS. Distribucí sdílený přístupový podpis identifikátor URI pro tyto klienty jim udělíte přístup k prostředku pro zadaném časovém období.
 
Pomocí SAS můžete definovat různé parametry přístupu do objektu blob, jako je například spuštění a vypršení platnosti, oprávnění (čtení a zápis) a rozsahy IP adres. Tento článek popisuje postup použití SAS ve spojení s Azure CDN. Další informace o tokenu SAS, včetně toho, jak vytvořit ji a její parametr možnosti najdete v části [pomocí sdílené přístupové podpisy (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Nastavení Azure CDN pro práci s úložiště SAS
Následující tři možnosti – se doporučují pro použití SAS s Azure CDN. Všechny možnosti předpokládají, že jste již vytvořili funkční SAS (viz požadavky). 
 
### <a name="prerequisites"></a>Požadavky
Pokud chcete spustit, vytvoření účtu úložiště a pak vytvořte SAS pro asset. Můžete vygenerovat dva typy uložené přístupových podpisů: SAS služby nebo SAS účtu. Další informace najdete v tématu [druhy sdílených přístupových podpisů](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Po vygenerování tokenu SAS, můžete přístup k souboru úložiště objektů blob, připojením `?sv=<SAS token>` na adresu URL. Tato adresa URL má následující formát: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Příklad:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Další informace o nastavení parametrů najdete v tématu [SAS parametr aspekty](#sas-parameter-considerations) a [parametry podpis sdíleného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Nastavení CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Možnost 1: Použití SAS s průchozí do úložiště objektů blob z Azure CDN

Tato možnost je nejjednodušší a používá jeden token SAS, která je předat z Azure CDN na původním serveru. Je podporován **Azure CDN společnosti Verizon** a **Azure CDN společnosti Akamai**. 
 
1. Vyberte koncový bod, vyberte **ukládání do mezipaměti pravidla**, pak vyberte **do mezipaměti každou jedinečnou adresu URL** z **ukládání do mezipaměti řetězce dotazu** seznamu.

    ![Ukládání do mezipaměti pravidla CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po nastavení SAS na vašem účtu úložiště, musíte použít tokenu SAS s CDN koncový bod a původní server adresy URL pro přístup k souboru. 
   
   Výsledný adresu URL koncového bodu CDN má následující formát: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Příklad:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Upřesnit dobu uložení do mezipaměti pomocí pravidel pro ukládání do mezipaměti nebo přidáním `Cache-Control` hlavičky na původním serveru. Protože Azure CDN považuje za řetězec dotazu prostý tokenu SAS, jako osvědčený postup měli byste nastavit ukládání do mezipaměti Doba trvání, kdy vyprší platnost času vypršení platnosti SAS nebo před ním. Jinak soubor je uložen v mezipaměti delší dobu, než je aktivní SAS, soubor může být přístupný ze serveru původu Azure CDN po uplynutí doby vypršení platnosti SAS. Pokud k této situaci dochází, a chcete si být nedostupné vaší souborů uložených v mezipaměti, je nutné provést operaci vyprázdnění souboru vymazat z mezipaměti. Informace o nastavení dobu uložení do mezipaměti na Azure CDN najdete v tématu [Azure CDN ovládacího prvku s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Možnost 2: Skryté tokenu CDN SAS pomocí přepsání pravidla
 
Tato možnost je dostupná pouze pro **Azure CDN Premium od společnosti Verizon** profily. Pomocí této možnosti můžete zabezpečit úložiště objektů blob na původním serveru. Chcete tuto možnost použijte, pokud nepotřebujete omezení přístupu konkrétním souboru, ale chcete zabránit uživatelům v přístupu k počátku úložiště přímo na vylepšovat dobu snižování zátěže Azure CDN. Token SAS, který neznámý uživateli, je vyžadována pro každý, kdo přístup k souborům v zadaném kontejneru na zdrojový server. Z důvodu pravidlo přepisování adres URL, ale tokenu SAS není vyžadován na koncový bod CDN.
 
1. Použití [stroj pravidel](cdn-rules-engine.md) k vytvoření pravidla přepisování adres URL. Nová pravidla trvat asi 90 minut rozšíření.

   ![Tlačítko Spravovat CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Tlačítko modul pravidla CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Následující ukázka pravidla přepisování adres URL používá vzor regulárního výrazu s skupinu zachycení a koncový bod s názvem *storagedemo*:
   
   Zdroj:   
   `(/test/.*)`
   
   Cíl:   
   ```
   $1?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![Přepisování adres URL CDN pravidlo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-2.png)

2. Po aktivaci nové pravidlo, každý, kdo přístup k souborům v zadaném kontejneru na koncový bod CDN bez ohledu na to, jestli používají SAS token v adrese URL. Tady je ve formátu: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Příklad:   
   `https://demoendpoint.azureedge.net/container1/demo.jpg`
       

3. Upřesnit dobu uložení do mezipaměti pomocí pravidel pro ukládání do mezipaměti nebo přidáním `Cache-Control` hlavičky na původním serveru. Protože Azure CDN považuje za řetězec dotazu prostý tokenu SAS, jako osvědčený postup měli byste nastavit ukládání do mezipaměti Doba trvání, kdy vyprší platnost času vypršení platnosti SAS nebo před ním. Jinak soubor je uložen v mezipaměti delší dobu, než je aktivní SAS, soubor může být přístupný ze serveru původu Azure CDN po uplynutí doby vypršení platnosti SAS. Pokud k této situaci dochází, a chcete si být nedostupné vaší souborů uložených v mezipaměti, je nutné provést operaci vyprázdnění souboru vymazat z mezipaměti. Informace o nastavení dobu uložení do mezipaměti na Azure CDN najdete v tématu [Azure CDN ovládacího prvku s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Možnost 3: Pomocí pravidel přepisování ověření tokenu zabezpečení CDN

Chcete-li použít ověření tokenu zabezpečení Azure CDN, musíte mít **Azure CDN Premium od společnosti Verizon** profilu. Tato možnost je zabezpečení a přizpůsobit. Klientský přístup je založenou na parametrech zabezpečení, které můžete nastavit v tokenu zabezpečení. Jakmile jste vytvořili a nastavit token zabezpečení, bude nutné u všech adres URL koncového bodu CDN. Z důvodu pravidlo přepisování adres URL, ale tokenu SAS není vyžadován na koncový bod CDN. Pokud je token SAS později stává neplatným, Azure CDN už nebude moci znovu ověřit obsah ze zdrojového serveru.

1. [Vytvořit token zabezpečení Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) a aktivujte ji pomocí stroj pravidel pro koncový bod CDN a cestu, kde uživatelé přístup k souboru.

   Adresu URL pro koncový bod tokenu zabezpečení má následující formát:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Příklad:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parametr možnosti pro ověření tokenu zabezpečení jsou jiné než parametr možnosti pro SAS token. Pokud chcete použít při vytváření tokenu zabezpečení čas vypršení platnosti, byste měli nastavit na stejnou hodnotu jako dobu vypršení platnosti tokenu SAS. Tím zajistíte, že doba vypršení platnosti je předvídatelný. 
 
2. Použití [stroj pravidel](cdn-rules-engine.md) k vytvoření pravidla přepisování adres URL pro povolení SAS tokenu přístupu pro všechny objekty BLOB v kontejneru. Nová pravidla trvat asi 90 minut rozšíření.

   Následující ukázka pravidla přepisování adres URL používá vzor regulárního výrazu s skupinu zachycení a koncový bod s názvem *storagedemo*:
   
   Zdroj:   
   `(/test/.*)`
   
   Cíl:   
   ```
   $1&sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![Přepisování adres URL CDN pravidlo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-3.png)

3. Pokud obnovíte SAS, ujistěte se aktualizovat pravidlo přepisování adres Url s nový token SAS. 

## <a name="sas-parameter-considerations"></a>Aspekty parametr SAS

Protože SAS parametry nejsou viditelné pro Azure CDN, Azure CDN nelze změnit její chování doručování na jejich základě. Omezení definovaný parametr se použijí jenom u požadavků, které Azure CDN umožňuje na zdrojový server, ne pro požadavky od klienta k Azure CDN. Tento rozdíl je důležité vzít v úvahu při nastavení parametrů SAS. Pokud toto rozšířené možnosti jsou požadované a používáte [možnost 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), nastavte příslušná omezení na token zabezpečení Azure CDN.

| Název parametru SAS | Popis |
| --- | --- |
| Start | Čas, Azure CDN můžete začít přistupovat k objektu blob souboru. Z důvodu hodin zkreslit (Pokud signál hodiny dorazí na různé časy pro různé součásti), vyberte dobu 15 minut dříve, pokud chcete asset být okamžitě k dispozici. |
| Konec | Doba, po jejímž uplynutí Azure CDN už přístup k objektu blob souboru. Soubory uložené v mezipaměti na Azure CDN dřív jsou stále dostupné. Chcete-li řídit čas vypršení platnosti souboru, nastavte čas vypršení platnosti příslušné na token zabezpečení Azure CDN nebo mazání asset. |
| Povolené IP adresy | Volitelné. Pokud používáte **Azure CDN společnosti Verizon**, tento parametr můžete nastavit na rozsahy definované v [Azure CDN společnosti Verizon hraniční Server rozsahy IP adres](https://msdn.microsoft.com/library/mt757330.aspx). Pokud používáte **Azure CDN společnosti Akamai**, nelze nastavit parametr rozsahy IP, protože nejsou statické IP adresy.|
| Povolené protokoly | Protokol povolenou pro požadavek pomocí SAS účtu. Nastavení protokolu HTTPS se doporučuje.|

## <a name="see-also"></a>Další informace najdete v tématech
- [Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm Blob](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Zabezpečení prostředků Azure Content Delivery Network pomocí tokenu ověřování](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
