---
title: Používání Azure CDN s SAS | Dokumenty společnosti Microsoft
description: Azure CDN podporuje použití sdíleného přístupového podpisu (SAS) k udělení omezeného přístupu k soukromým kontejnerům úložiště.
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
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: c2580aa4ee22996c1bf0fe5c86064a6543450071
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260169"
---
# <a name="using-azure-cdn-with-sas"></a>Používání Azure CDN s SAS

Když nastavíte účet úložiště pro Síť pro doručování obsahu Azure (CDN), který se bude používat k ukládání obsahu do mezipaměti, má ve výchozím nastavení k souborům, které jste nahráli, kdokoli, kdo zná adresy URL pro vaše kontejnery úložiště. Chcete-li chránit soubory v účtu úložiště, můžete nastavit přístup kontejnerů úložiště z veřejného na soukromé. Pokud tak však učiníte, nikdo nebude mít přístup k vašim souborům. 

Pokud chcete udělit omezený přístup k privátním kontejnerům úložiště, můžete použít funkci sdíleného přístupového podpisu (SAS) účtu Azure Storage. SAS je identifikátor URI, který uděluje omezená přístupová práva k vašim prostředkům Azure Storage bez odhalení vašeho klíče účtu. SAS můžete poskytnout klientům, kterým nedůvěřujete s klíčem účtu úložiště, ale kterým chcete delegovat přístup k určitým prostředkům účtu úložiště. Distribucí identifikátoru URI sdíleného přístupového podpisu těmto klientům jim udělíte přístup k prostředku po určitou dobu.
 
Pomocí SAS můžete definovat různé parametry přístupu k objektu blob, například časy zahájení a vypršení platnosti, oprávnění (čtení a zápis) a rozsahy IP adres. Tento článek popisuje, jak používat SAS ve spojení s Azure CDN. Další informace o SAS, včetně jak ji vytvořit a jeho možnosti parametru, naleznete [v tématu Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Nastavení Azure CDN pro práci s úložištěm SAS
Následující tři možnosti se doporučují pro použití SAS s Azure CDN. Všechny možnosti předpokládají, že jste již vytvořili funkční SAS (viz požadavky). 
 
### <a name="prerequisites"></a>Požadavky
Chcete-li začít, vytvořte účet úložiště a pak vygenerujte SAS pro váš prostředek. Můžete vygenerovat dva typy uložených přístupových podpisů: službu SAS nebo účet SAS. Další informace naleznete [v tématu Typy sdílených přístupových podpisů](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Po vygenerování tokenu SAS můžete přistupovat k souboru `?sv=<SAS token>` úložiště objektů blob připojením k adrese URL. Tato adresa URL má následující formát: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Příklad:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Další informace o nastavení parametrů naleznete v [tématu ASPEKTY parametrů SAS](#sas-parameter-considerations) a [parametry podpisu sdíleného přístupu](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).

![Nastavení CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Možnost 1: Použití SAS s předávací do úložiště objektů blob z Azure CDN

Tato možnost je nejjednodušší a používá jeden token SAS, který se předává z Azure CDN na zdrojový server.
 
1. Vyberte koncový bod, vyberte **pravidla ukládání do mezipaměti**a vyberte **uložit do mezipaměti každou jedinečnou adresu URL** ze seznamu ukládání řetězců dotazu do **mezipaměti.**

    ![Pravidla ukládání do mezipaměti CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po nastavení SAS na účtu úložiště, musíte použít token SAS s koncovým bodem CDN a zdrojový server adresy URL pro přístup k souboru. 
   
   Výsledná adresa URL koncového bodu CDN má následující formát:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Příklad:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Dolaďte dobu trvání mezipaměti pomocí pravidel ukládání `Cache-Control` do mezipaměti nebo přidáním záhlaví na zdrojový server. Vzhledem k tomu, že Azure CDN považuje token SAS jako prostý řetězec dotazu, jako osvědčený postup byste měli nastavit dobu ukládání do mezipaměti, která vyprší v době vypršení platnosti SAS nebo před ní. V opačném případě pokud je soubor uložen do mezipaměti po delší dobu, než je aktivní SAS, soubor může být přístupný ze zdrojového serveru Azure CDN po uplynutí doby vypršení platnosti SAS. Pokud nastane tato situace a chcete, aby soubor uložený v mezipaměti nepřístupný, je nutné provést operaci vymazání souboru vymazat z mezipaměti. Informace o nastavení doby trvání mezipaměti v Azure CDN najdete [v tématu Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Možnost 2: Skrytý token CDN SAS pomocí pravidla přepisu
 
Tato možnost je dostupná jenom pro **Azure CDN Premium od verizonských** profilů. Pomocí této možnosti můžete zabezpečit úložiště objektů blob na původníserver. Tuto možnost můžete použít, pokud nepotřebujete konkrétní omezení přístupu k souboru, ale chcete zabránit uživatelům v přístupu k původu úložiště přímo ke zlepšení doby offload Azure CDN. Token SAS, který není pro uživatele znám, je vyžadován pro každého, kdo přistupuje k souborům v zadaném kontejneru původního serveru. Z důvodu pravidla přepisu adresy URL však není v koncovém bodě CDN vyžadován token SAS.
 
1. Pomocí [modulu pravidel](cdn-rules-engine.md) vytvořte pravidlo přepisování adres URL. Šíření nových pravidel trvá až 4 hodiny.

   ![Tlačítko Správa CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Tlačítko motoru pravidel CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Následující vzorové pravidlo přepisu adresy URL používá vzor regulárního výrazu se zachytávající skupinou a koncový bod s názvem *sasstoragedemo*:
   
   Zdroj:   
   `(container1\/.*)`
   
   Cíl:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Pravidlo přepisu - levé](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![pravidlo přepisu ADRESY CDN - vpravo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Jakmile se nové pravidlo stane aktivním, může kdokoli získat přístup k souborům v zadaném kontejneru v koncovém bodě CDN bez ohledu na to, zda v adrese URL používá token SAS. Zde je formát:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Příklad:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Dolaďte dobu trvání mezipaměti pomocí pravidel ukládání `Cache-Control` do mezipaměti nebo přidáním záhlaví na zdrojový server. Vzhledem k tomu, že Azure CDN považuje token SAS jako prostý řetězec dotazu, jako osvědčený postup byste měli nastavit dobu ukládání do mezipaměti, která vyprší v době vypršení platnosti SAS nebo před ní. V opačném případě pokud je soubor uložen do mezipaměti po delší dobu, než je aktivní SAS, soubor může být přístupný ze zdrojového serveru Azure CDN po uplynutí doby vypršení platnosti SAS. Pokud nastane tato situace a chcete, aby soubor uložený v mezipaměti nepřístupný, je nutné provést operaci vymazání souboru vymazat z mezipaměti. Informace o nastavení doby trvání mezipaměti v Azure CDN najdete [v tématu Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Možnost 3: Použití ověřování tokenu zabezpečení CDN s pravidlem přepisu

Chcete-li použít ověřování tokenů zabezpečení Azure CDN, musíte mít profil **Azure CDN Premium od společnosti Verizon.** Tato možnost je nejbezpečnější a přizpůsobitelná. Přístup klienta je založen na parametrech zabezpečení, které jste nastavili na tokenu zabezpečení. Jakmile vytvoříte a nastavíte token zabezpečení, bude vyžadován na všech adresách URL koncových bodů CDN. Z důvodu pravidla přepisu adresy URL však není v koncovém bodě CDN vyžadován token SAS. Pokud se token SAS později stane neplatným, Azure CDN už nebude moct znovu ověřit obsah ze zdrojového serveru.

1. [Vytvořte token zabezpečení Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) a aktivujte jej pomocí modulu pravidel pro koncový bod CDN a cestu, kde mohou uživatelé získat přístup k souboru.

   Adresa URL koncového bodu tokenu zabezpečení má následující formát:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Příklad:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Možnosti parametrů pro ověřování tokenu zabezpečení se liší od možností parametru pro token SAS. Pokud se rozhodnete použít čas vypršení platnosti při vytváření tokenu zabezpečení, měli byste jej nastavit na stejnou hodnotu jako čas vypršení platnosti tokenu SAS. Tím zajistíte, že doba vypršení platnosti je předvídatelná. 
 
2. Pomocí [modulu pravidel](cdn-rules-engine.md) vytvořte pravidlo přepisování adres URL, které povolí přístup tokenu SAS ke všem objektům BLOB v kontejneru. Šíření nových pravidel trvá až 4 hodiny.

   Následující vzorové pravidlo přepisu adresy URL používá vzor regulárního výrazu se zachytávající skupinou a koncový bod s názvem *sasstoragedemo*:
   
   Zdroj:   
   `(container1\/.*)`
   
   Cíl:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Pravidlo přepisu - levé](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![pravidlo přepisu ADRESY CDN - vpravo](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Pokud obnovíte SAS, ujistěte se, že aktualizujete pravidlo přepisování adresy URL novým tokenem SAS. 

## <a name="sas-parameter-considerations"></a>Důležité informace o parametrech SAS

Vzhledem k tomu, že parametry SAS nejsou viditelné pro Azure CDN, Azure CDN nelze změnit jeho chování doručování na základě nich. Definovaná omezení parametrů platí jenom pro požadavky, které Azure CDN provádí na zdrojový server, nikoli pro požadavky z klienta na Azure CDN. Tento rozdíl je důležité vzít v úvahu při nastavování parametrů SAS. Pokud jsou tyto pokročilé funkce povinné a používáte [možnost 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), nastavte příslušná omezení na tokenu zabezpečení Azure CDN.

| Název parametru SAS | Popis |
| --- | --- |
| Spustit | Čas, který Azure CDN můžete začít přistupovat k souboru objektů blob. Vzhledem k zkosení hodin (když signál hodin dorazí v různých časech pro různé součásti), zvolte čas o 15 minut dříve, pokud chcete, aby byl datový zdroj k dispozici okamžitě. |
| End | Po dobu, po které Azure CDN již přístup k souboru objektů blob. Dříve uložené soubory v Azure CDN jsou stále přístupné. Chcete-li řídit čas vypršení platnosti souboru, nastavte příslušný čas vypršení platnosti na tokenu zabezpečení Azure CDN nebo vymazání prostředku. |
| Povolené IP adresy | Nepovinný parametr. Pokud používáte **Azure CDN od společnosti Verizon**, můžete tento parametr nastavit na rozsahy definované v Azure [CDN z rozsahů IP serveru Verizon Edge](/azure/cdn/cdn-pop-list-api). Pokud používáte **Azure CDN z Akamai**, nelze nastavit parametr rozsahy IP, protože IP adresy nejsou statické.|
| Povolené protokoly | Protokol (protokoly) povoleno pro požadavek s účtem SAS. Doporučujeme nastavení HTTPS.|

## <a name="next-steps"></a>Další kroky

Další informace o SAS naleznete v následujících článcích:
- [Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Další informace o nastavení ověřování tokenů najdete [v tématu Zabezpečení prostředků sítě doručování obsahu Azure pomocí ověřování tokenů](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
