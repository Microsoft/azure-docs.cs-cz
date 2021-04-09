---
title: Použití Azure CDN s SAS | Microsoft Docs
description: Azure CDN podporuje použití sdíleného přístupového podpisu (SAS) pro udělení omezeného přístupu k soukromým kontejnerům úložiště.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725318"
---
# <a name="using-azure-cdn-with-sas"></a>Použití Azure CDN s SAS

Při nastavování účtu úložiště pro Azure Content Delivery Network (CDN), který se použije k ukládání obsahu do mezipaměti, má standardně kdokoli, kdo zná adresy URL vašich kontejnerů úložiště, přístup k souborům, které jste nahráli. Pokud chcete chránit soubory ve vašem účtu úložiště, můžete nastavit přístup k kontejnerům úložiště z veřejných na soukromou. Pokud to ale uděláte, nebude mít nikdo přístup k vašim souborům. 

Pokud chcete udělit omezený přístup k privátním kontejnerům úložiště, můžete použít funkci sdíleného přístupového podpisu (SAS) účtu Azure Storage. SAS je identifikátor URI, který uděluje omezená přístupová práva k vašim prostředkům Azure Storage bez odhalení vašeho klíče účtu. Pro klienty, kterým nedůvěřujete pomocí klíče účtu úložiště, můžete poskytnout SAS, ale u kterých chcete delegovat přístup k určitým prostředkům účtu úložiště. Distribucí identifikátoru URI sdíleného přístupového podpisu těmto klientům udělíte přístup k prostředku po určenou dobu.
 
Pomocí SAS můžete definovat různé parametry přístupu k objektu blob, jako je doba zahájení a vypršení platnosti, oprávnění (čtení a zápis) a rozsahy IP adres. Tento článek popisuje, jak používat SAS ve spojení s Azure CDN. Další informace o SAS, včetně způsobu jejího vytvoření a možností parametrů, najdete v tématu [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Nastavení Azure CDN pro práci s SAS úložiště
Pro použití SAS s Azure CDN se doporučuje použít následující tři možnosti. U všech možností se předpokládá, že jste už vytvořili pracovní SAS (viz požadavky). 
 
### <a name="prerequisites"></a>Požadavky
Začněte tím, že vytvoříte účet úložiště a potom pro svůj Asset vygenerujete SAS. Můžete vygenerovat dva typy uložených přístupových podpisů: SAS služby nebo SAS účtu. Další informace najdete v tématu [typy podpisů sdíleného přístupu](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures).

Po vygenerování tokenu SAS můžete k souboru BLOB Storage získat přístup připojením `?sv=<SAS token>` k adrese URL. Tato adresa URL má následující formát: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Například:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Další informace o nastavení parametrů najdete v tématu [informace o parametrech SAS](#sas-parameter-considerations) a [parametry signatury sdíleného přístupu](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![Nastavení SAS pro CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Možnost 1: použití SAS s předávacím úložištěm pro úložiště objektů BLOB z Azure CDN

Tato možnost je nejjednodušší a používá jediný token SAS, který se předává z Azure CDN na zdrojový server.
 
1. Vyberte koncový bod, vyberte **pravidla ukládání do** mezipaměti a potom v seznamu **mezipaměť řetězců dotazu** vyberte **mezipaměť každou jedinečnou adresu URL** .

    ![Pravidla ukládání do mezipaměti CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po nastavení SAS v účtu úložiště je nutné použít token SAS s koncovým bodem CDN a adresou URL zdrojového serveru pro přístup k souboru. 
   
   Výsledná adresa URL koncového bodu CDN má následující formát: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Například:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Doladit dobu trvání mezipaměti buď pomocí pravidel ukládání do mezipaměti, nebo přidáním `Cache-Control` hlaviček na zdrojovém serveru. Vzhledem k tomu, že Azure CDN považuje token SAS za řetězec jednoduchého dotazu, jako osvědčený postup byste měli nastavit dobu ukládání do mezipaměti, která vyprší v nebo před časem vypršení platnosti SAS. V opačném případě, pokud je soubor uložen do mezipaměti po delší dobu, než je aktivní SAS, může být soubor přístupný z Azure CDNho zdrojového serveru po uplynutí doby vypršení platnosti SAS. Pokud k této situaci dojde a chcete, aby byl soubor v mezipaměti nepřístupný, je nutné provést operaci vyprázdnění souboru, aby byl vymazán z mezipaměti. Informace o nastavení doby trvání mezipaměti v Azure CDN najdete v tématu [Azure CDN řízení chování při ukládání do mezipaměti pomocí pravidel ukládání do](cdn-caching-rules.md)mezipaměti.

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Možnost 2: skrytý token SAS CDN pomocí pravidla přepsání
 
Tato možnost je dostupná jenom pro **Azure CDN Premium ze profilů Verizon** . Pomocí této možnosti můžete zabezpečit úložiště objektů blob na zdrojovém serveru. Tuto možnost můžete chtít použít, pokud pro tento soubor nepotřebujete specifická omezení přístupu, ale chcete uživatelům zabránit v přístupu ke zdroji úložiště přímo za účelem zvýšení Azure CDN doby snižování zátěže. Token SAS, který je pro uživatele neznámý, je vyžadován pro kohokoli, kdo přistupuje k souborům v zadaném kontejneru zdrojového serveru. Vzhledem k pravidlu přepsání adresy URL ale není token SAS v koncovém bodu CDN vyžadován.
 
1. Pomocí [modulu pravidel](./cdn-verizon-premium-rules-engine.md) vytvořte pravidlo pro přepsání adresy URL. Rozšiřování nových pravidel trvá až 4 hodiny.

   ![Tlačítko Spravovat CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Modul pravidel CDN – tlačítko](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Následující vzorové pravidlo přepsání adresy URL používá vzor regulárního výrazu se zachytávající skupinou a koncovým bodem s názvem *sasstoragedemo*:
   
   Zdroj:   
   `(container1/.*)`


   Cíl:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Pravidlo pro přepsání adresy URL CDN – levé ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ pravidlo pro přepsání adresy URL pro CDN – vpravo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Jakmile se nové pravidlo stane aktivním, může kdokoli získat přístup k souborům v zadaném kontejneru na koncovém bodu CDN bez ohledu na to, jestli v adrese URL používá token SAS. Tady je formát: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Například:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Doladit dobu trvání mezipaměti buď pomocí pravidel ukládání do mezipaměti, nebo přidáním `Cache-Control` hlaviček na zdrojovém serveru. Vzhledem k tomu, že Azure CDN považuje token SAS za řetězec jednoduchého dotazu, jako osvědčený postup byste měli nastavit dobu ukládání do mezipaměti, která vyprší v nebo před časem vypršení platnosti SAS. V opačném případě, pokud je soubor uložen do mezipaměti po delší dobu, než je aktivní SAS, může být soubor přístupný z Azure CDN koncového bodu po uplynutí doby vypršení platnosti SAS. Pokud k této situaci dojde a chcete, aby byl soubor v mezipaměti nepřístupný, je nutné provést operaci vyprázdnění souboru, aby byl vymazán z mezipaměti. Informace o nastavení doby trvání mezipaměti v Azure CDN najdete v tématu [Azure CDN řízení chování při ukládání do mezipaměti pomocí pravidel ukládání do](cdn-caching-rules.md)mezipaměti.

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Možnost 3: ověřování pomocí tokenu zabezpečení CDN s pravidlem přepsání

Pokud chcete použít Azure CDN ověřování pomocí tokenu zabezpečení, musíte mít **Azure CDN Premium z profilu Verizon** . Tato možnost je nejbezpečnější a přizpůsobitelná. Klientský přístup je založen na parametrech zabezpečení, které jste nastavili v tokenu zabezpečení. Po vytvoření a nastavení tokenu zabezpečení se bude vyžadovat pro všechny adresy URL koncového bodu CDN. Vzhledem k pravidlu přepsání adresy URL ale není token SAS v koncovém bodu CDN vyžadován. Pokud se token SAS později stane neplatný, Azure CDN už nebude moct znovu ověřit obsah ze zdrojového serveru.

1. [Vytvořte Azure CDN token zabezpečení](./cdn-token-auth.md#setting-up-token-authentication) a aktivujte ho pomocí modulu pravidel pro koncový bod CDN a cestu, kde mají vaši uživatelé k souboru přístup.

   Adresa URL koncového bodu tokenu zabezpečení má následující formát:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Například:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Možnosti parametrů pro ověřování tokenu zabezpečení se liší od možností parametrů pro token SAS. Pokud se rozhodnete použít čas vypršení platnosti při vytváření tokenu zabezpečení, měli byste ho nastavit na stejnou hodnotu jako čas vypršení platnosti tokenu SAS. Tím zajistíte předvídatelný čas vypršení platnosti. 
 
2. Pomocí [modulu pravidel](./cdn-verizon-premium-rules-engine.md) vytvořte pravidlo pro přepsání adresy URL, které povolí přístup k tokenům SAS všem objektům blob v kontejneru. Rozšiřování nových pravidel trvá až 4 hodiny.

   Následující vzorové pravidlo přepsání adresy URL používá vzor regulárního výrazu se zachytávající skupinou a koncovým bodem s názvem *sasstoragedemo*:
   
   Zdroj:   
   `(container1/.*)`
   
   Cíl:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Pravidlo pro přepsání adresy URL CDN – levé ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ pravidlo pro přepsání adresy URL pro CDN – vpravo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Pokud provedete obnovení SAS, ujistěte se, že aktualizujete pravidlo pro přepsání adresy URL novým tokenem SAS. 

## <a name="sas-parameter-considerations"></a>Požadavky na parametry SAS

Vzhledem k tomu, že parametry SAS nejsou Azure CDN k dispozici, Azure CDN nemohou změnit jejich chování při doručování na základě nich. Omezení definovaných parametrů se vztahují pouze na požadavky, které Azure CDN provede na zdrojovém serveru, a ne pro žádosti od klienta do Azure CDN. Toto rozlišení je důležité vzít v úvahu při nastavování parametrů SAS. Pokud jsou tyto rozšířené možnosti požadovány a používáte [možnost 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), nastavte příslušná omezení pro Azure CDN token zabezpečení.

| Název parametru SAS | Description |
| --- | --- |
| Spustit | Čas, kdy Azure CDN může začít přistupovat k souboru objektu BLOB. Pokud chcete, aby byl Asset k dispozici okamžitě, vyberte čas od času naklonění (když se hodinový signál dorazí v různou dobu pro různé komponenty). |
| End | Čas, po jehož uplynutí Azure CDN nemůže získat přístup k souboru objektu BLOB. Soubory dříve uložených v mezipaměti v Azure CDN jsou stále přístupné. Chcete-li řídit čas vypršení platnosti souboru, buď nastavte vhodný čas vypršení platnosti v Azure CDN tokenu zabezpečení, nebo vyprázdnte Asset. |
| Povolené IP adresy | Nepovinný parametr. Pokud používáte **Azure CDN z Verizon**, můžete tento parametr nastavit na rozsahy definované v [Azure CDN z rozsahů IP adres serveru Verizon Edge](./cdn-pop-list-api.md). Pokud používáte **Azure CDN z Akamai**, nemůžete nastavit parametr rozsahy IP adres, protože IP adresy nejsou statické.|
| Povolené protokoly | Protokol povolený pro žádost vytvořenou s podpisem SAS účtu. Doporučuje se nastavení HTTPS.|

## <a name="next-steps"></a>Další kroky

Další informace o SAS najdete v následujících článcích:
- [Použití sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md)
- [Signatury sdíleného přístupu, část 2: vytvoření a použití SAS s úložištěm objektů BLOB](../storage/common/storage-sas-overview.md)

Další informace o nastavení ověřování tokenu najdete v tématu [zabezpečení prostředků Azure Content Delivery Network s ověřováním tokenu](./cdn-token-auth.md).