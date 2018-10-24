---
title: Používání Azure CDN pomocí SAS | Dokumentace Microsoftu
description: Azure CDN podporuje použití ze sdíleného přístupového podpisu (SAS) udělit omezený přístup do privátního úložiště kontejnerů.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: 57891bcce289c30d7dce1cd00c301064aa9b97cc
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955231"
---
# <a name="using-azure-cdn-with-sas"></a>Používání Azure CDN pomocí SAS

Při nastavení účtu úložiště pro Azure Content Delivery Network (CDN) použít k ukládání obsahu do mezipaměti, ve výchozím nastavení každý, kdo zná adresy URL pro vaše kontejnery úložiště můžou přistupovat k souborům, které jste odeslali. Při ochraně souborů ve vašem účtu úložiště, můžete nastavit přístup kontejnery úložiště z veřejné na privátní. Nicméně pokud tak učiníte, nikdo bude možné získat přístup k souborům. 

Pokud chcete udělit omezený přístup k privátním kontejnerům úložiště, můžete použít funkci sdíleného přístupového podpisu (SAS) účtu Azure Storage. SAS je identifikátor URI, který uděluje omezená přístupová práva k vašim prostředkům Azure Storage bez odhalení vašeho klíče účtu. SAS můžete poskytnout klientům, kteří nedůvěřujete vaším klíčem účtu úložiště, ale ke kterému chcete delegovat přístup k určitým prostředkům účtu úložiště. Díky distribuci sdíleného přístupového podpisu URI pro tyto klienty jim udělíte přístup k prostředku po zadanou dobu.
 
Pomocí SAS můžete definovat různé parametry přístupu pro objekt blob, jako je čas začátku a konce platnosti, oprávnění (čtení a zápis) a rozsahy IP adres. Tento článek popisuje, jak pomocí SAS ve spojení s Azure CDN. Další informace o tokenu SAS, jak vytvořit a jeho parametrech najdete v části [použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Nastavení Azure CDN pro práci s úložištěm SAS
Následující tři možnosti jsou doporučené pro použití SAS s Azure CDN. Všechny možnosti předpokládají, že jste již vytvořili funkční SAS (viz požadavky). 
 
### <a name="prerequisites"></a>Požadavky
Pokud chcete začít, vytvořit účet úložiště a pak vygenerovat SAS pro váš prostředek. Můžete vygenerovat dva typy uložené přístupových podpisů: SAS služby nebo SAS účtu. Další informace najdete v tématu [druhy sdílených přístupových podpisů](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Až si necháte vygenerovat SAS token, můžete přístup k souboru úložiště objektů blob, přidáním `?sv=<SAS token>` k adrese URL. Tato adresa URL má následující formát: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Příklad:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Další informace o nastavení parametrů najdete v tématu [SAS parametr aspekty](#sas-parameter-considerations) a [parametry podpis sdíleného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Nastavení CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Možnost 1: Použití SAS se předávací do úložiště objektů blob v Azure CDN

Tato možnost je nejjednodušší a používá jeden token SAS, který je předán z Azure CDN na zdrojový server. Je podporován **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profily. 
 
1. Vyberte koncový bod, vyberte **pravidla ukládání do mezipaměti**a pak vyberte **ukládat do mezipaměti každou jedinečnou adresu URL** z **ukládání do mezipaměti řetězce dotazu** seznamu.

    ![Pravidla ukládání do mezipaměti CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Jakmile nastavíte SAS na vašem účtu úložiště, musíte použít SAS token s URL serverů koncový bod a původu CDN pro přístup k souboru. 
   
   Výsledná adresa URL koncového bodu CDN má následující formát: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Příklad:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Vyladění dobu uložení do mezipaměti s využitím pravidel ukládání do mezipaměti nebo přidáním `Cache-Control` záhlaví na původním serveru. Protože Azure CDN zpracovává SAS token jako řetězec prostého dotazu, jako osvědčený postup byste měli nastavit nahoru ukládání do mezipaměti Doba trvání, jejíž platnost vyprší při nebo před časem vypršení platnosti SAS. V opačném případě souboru je v mezipaměti uložené delší dobu, než je aktivní SAS, soubor může být přístupný ze serveru původu Azure CDN po uplynutí doby vypršení platnosti SAS. Pokud k této situaci dochází, a mají být váš soubor v mezipaměti nepřístupný, je třeba provést operaci vyprázdnění souboru vymazat z mezipaměti. Informace o nastavení dobu uložení do mezipaměti ve službě Azure CDN najdete v tématu [řízení Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Možnost 2: Skrytý tokenu CDN SAS pomocí pravidlo pro přepis adres
 
Tato možnost je dostupná jenom pro **Azure CDN Premium od Verizonu** profily. Pomocí této možnosti můžete svázat s blob storage na původním serveru. Chcete tuto možnost použijte, pokud nepotřebujete omezení specifický přístup k souboru, ale chcete zabránit uživatelům v přístupu k původu úložiště přímo umožňují zrychlit snižování zátěže Azure CDN. Token SAS, který neznámý pro uživatele, se vyžaduje pro každého, kdo přistupuje k souborům v zadaném kontejneru původním serveru. Z důvodu pravidel přepisu adres URL však SAS token není vyžadován na koncový bod CDN.
 
1. Použití [stroj pravidel](cdn-rules-engine.md) k vytvoření pravidla přepisování adres URL. Nová pravidla trvat až 4 hodinách.

   ![Tlačítko Spravovat CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Tlačítko ke stroji pravidel CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Následující ukázka přepisu adresy URL pravidla používá vzor regulárního výrazu s zachytávající skupinu a koncový bod s názvem *sasstoragedemo*:
   
   Zdroj:   
   `(container1\/.*)`
   
   Cíl:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Přepisování adres URL CDN pravidlo – vlevo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![pravidlo přepisování adres URL CDN – vpravo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Po aktivaci nové pravidlo, všem uživatelům přístup k souborům v zadaném kontejneru na koncový bod CDN bez ohledu na to, jestli používá SAS token v adrese URL. Tady má tento formát: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Příklad:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Vyladění dobu uložení do mezipaměti s využitím pravidel ukládání do mezipaměti nebo přidáním `Cache-Control` záhlaví na původním serveru. Protože Azure CDN zpracovává SAS token jako řetězec prostého dotazu, jako osvědčený postup byste měli nastavit nahoru ukládání do mezipaměti Doba trvání, jejíž platnost vyprší při nebo před časem vypršení platnosti SAS. V opačném případě souboru je v mezipaměti uložené delší dobu, než je aktivní SAS, soubor může být přístupný ze serveru původu Azure CDN po uplynutí doby vypršení platnosti SAS. Pokud k této situaci dochází, a mají být váš soubor v mezipaměti nepřístupný, je třeba provést operaci vyprázdnění souboru vymazat z mezipaměti. Informace o nastavení dobu uložení do mezipaměti ve službě Azure CDN najdete v tématu [řízení Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Možnost 3: Použití ověřování pomocí tokenu zabezpečení CDN s pravidlo pro přepis adres

Pokud chcete použít ověřování pomocí tokenu zabezpečení Azure CDN, musíte mít **Azure CDN Premium od Verizonu** profilu. Tato možnost je nejbezpečnější a přizpůsobitelné. Klientský přístup je na základě zabezpečení parametrů, které jste nastavili v tokenu zabezpečení. Jakmile máte vytvořený a nastavit token zabezpečení, bude potřeba na všechny adresy URL koncového bodu CDN. Z důvodu pravidel přepisu adres URL však SAS token není vyžadován na koncový bod CDN. Pokud SAS token později níže uvedených situací, Azure CDN už nebude moci znovu ověřit obsah ze zdrojového serveru.

1. [Vytvoření tokenu zabezpečení Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) a aktivujte ji pomocí stroj pravidel pro koncový bod CDN a cesta kde vaši uživatelé přístup k souboru.

   Adresu URL koncového bodu tokenu zabezpečení má následující formát:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Příklad:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parametr možnosti pro ověření tokenu zabezpečení se liší od možností parametr pro SAS token. Pokud se rozhodnete použít čas vypršení platnosti při vytváření tokenu zabezpečení, byste měli nastavit na stejnou hodnotu jako čas vypršení platnosti tokenu SAS. Tím se zajistí, že doba vypršení platnosti je předvídatelné. 
 
2. Použití [stroj pravidel](cdn-rules-engine.md) k vytvoření pravidla přepisování adres URL umožňuje přístup pomocí tokenu SAS pro všechny objekty BLOB v kontejneru. Nová pravidla trvat až 4 hodinách.

   Následující ukázka přepisu adresy URL pravidla používá vzor regulárního výrazu s zachytávající skupinu a koncový bod s názvem *sasstoragedemo*:
   
   Zdroj:   
   `(container1\/.*)`
   
   Cíl:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Přepisování adres URL CDN pravidlo – vlevo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![pravidlo přepisování adres URL CDN – vpravo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Pokud obnovíte SAS, ujistěte se aktualizovat pravidlo přepisování adres Url pomocí nového tokenu SAS. 

## <a name="sas-parameter-considerations"></a>Důležité informace o parametru SAS

Protože SAS parametry nejsou viditelné pro Azure CDN, Azure CDN nelze změnit její chování doručování na jejich základě. Definovaný parametr omezení se vztahuje pouze na požadavky, které Azure CDN umožňuje na zdrojový server, nikoli pro žádosti od klienta k Azure CDN. Tento rozdíl je důležité vzít v úvahu při nastavování parametrů SAS. Pokud tyto rozšířené možnosti jsou povinné a používáte [možnost 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), nastavte příslušná omezení na token zabezpečení Azure CDN.

| Název parametru SAS | Popis |
| --- | --- |
| Start | Čas, který Azure CDN získáte přístup k souboru objektu blob. Z důvodu hodin zkreslit (dorazí signál hodiny v různou dobu pro různé součásti), zvolte čas o 15 minut dřívější, pokud chcete asset bude okamžitě k dispozici. |
| End | Doba, po jejímž uplynutí Azure CDN už mít přístup k souboru objektu blob. Dříve soubory uložené do mezipaměti ve službě Azure CDN, jsou stále přístupné. Pokud chcete řídit dobu vypršení platnosti souboru, nastavte čas odpovídající vypršení platnosti tokenu zabezpečení Azure CDN nebo odstranit i asset. |
| Povolené IP adresy | Volitelné. Pokud používáte **Azure CDN od Verizonu**, tento parametr lze nastavit na rozsahy definované v [Azure CDN od Verizonu rozšíření rozsahy IP adres serveru Edge](https://msdn.microsoft.com/library/mt757330.aspx). Pokud používáte **Azure CDN od Akamai**, nelze nastavit parametr rozsahy IP, protože nejsou statické IP adresy.|
| Povolené protokoly | Protokol povolen pro žádost vytvořenou s podpisem SAS účtu. Nastavení protokolu HTTPS se doporučuje.|

## <a name="next-steps"></a>Další postup

Další informace o tokenu SAS najdete v následujících článcích:
- [Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Další informace o nastavení ověřování pomocí tokenu najdete v tématu [zabezpečení Azure Content Delivery Network prostředky pomocí ověřování tokenu](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
