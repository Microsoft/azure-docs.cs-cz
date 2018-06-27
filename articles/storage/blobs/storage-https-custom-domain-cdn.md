---
title: Přístup k objektům BLOB pomocí vlastní domény přes HTTPS pomocí Azure CDN
description: Zjistěte, jak integrovat Azure CDN úložiště objektů blob pro přístup k objektům BLOB pomocí vlastní domény přes protokol HTTPS
services: storage
documentationcenter: ''
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.openlocfilehash: b3b1b5064e51b68bb64cb8c4dbec6075705795d6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025696"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Přístup k objektům BLOB pomocí vlastní domény přes HTTPS pomocí Azure CDN
Azure Content Delivery Network (CDN) teď podporuje protokol HTTPS pro vlastní názvy domén. Můžete použít tuto funkci pro přístup k objektům BLOB storage pomocí vaši vlastní doménu přes protokol HTTPS. Uděláte to tak, budete nejprve muset povolení Azure CDN na koncový bod služby objektů blob nebo webu a mapovat je na vlastní název domény CDN. Jakmile je provést tyto kroky, povolení protokolu HTTPS pro vaše vlastní doména je zjednodušený prostřednictvím povolování jedním kliknutím, kompletní certifikát správy a všechny s bez dalších nákladů na normální ceny CDN.

Tato možnost je důležité, protože umožňuje ochrany osobních údajů a integritu dat dat vaše citlivé webové aplikace v průběhu přenosu. Použití protokolu SSL k obsluze přenosy přes HTTPS zajišťuje, data se šifrují, když je posílání přes internet. HTTPS poskytuje vztah důvěryhodnosti a ověřování a chrání vaše webové aplikace před útoky.

> [!NOTE]  
> Kromě zajištění podpory protokolu SSL pro vlastní názvy domén, Azure CDN vám může pomoct škálování aplikace pro doručování širokopásmového obsahu po celém světě. Další informace, podívejte se na [přehled Azure CDN](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Rychlý start
Tyto kroky potřeba povolit HTTPS pro koncový bod služby vlastní objekt blob storage:

1.  [Účet úložiště Azure integrovat Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Tento článek vás provede procesem vytvoření účtu úložiště na portálu Azure, pokud jste tak již neučinili.

    > [!NOTE]  
    > Během preview podporu statických webů ve službě Azure Storage vyberte z rozevírací nabídky přidat koncový bod webové služby storage "typ původu" "vlastní původ". Na portálu Azure musíte to provést z váš profil CDN místo přímo ve vašem účtu úložiště.

2.  [Mapování obsahu Azure CDN k vlastní doméně](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Povolit protokol HTTPS na vlastní domény služby Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy
Pokud koncový bod služby blob storage je nakonfigurovaný tak, aby zakázala anonymní přístup pro čtení, je nutné zadat [sdíleného přístupového podpisu (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) v každé žádosti o token provedete vaši vlastní doménu. Koncové body úložiště objektů blob ve výchozím nastavení zakáže anonymní přístup pro čtení. V tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](storage-manage-access-to-resources.md) Další informace o sdílených přístupových podpisů.

Azure CDN nedodržuje nějaká omezení přidán do tokenu SAS. Například všechny tokeny SAS mít čas vypršení platnosti. To znamená, že obsah můžete dál dostat s vypršenou platností SAS dokud tento obsah se vyprazdňují z hraniční uzlů CDN. Můžete řídit, jak dlouho je uložen v mezipaměti na CDN nastavením hlavičku odpovědi v mezipaměti. V tématu [Správa vypršení platnosti Azure úložiště objektů BLOB v Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md) pokyny.

Pokud vytvoříte více adres URL SAS pro stejný koncový bod objektu blob, doporučujeme zapnout řetězec dotazu ukládání do mezipaměti pro Azure CDN. To je potřeba zajistit, že jednotlivé adresy URL je považován za jedinečné entity. V tématu [řízení Azure CDN ukládání do mezipaměti chování řetězce dotazu](../../cdn/cdn-query-string.md) Další informace.

## <a name="http-to-https-redirection"></a>Protokol HTTP do HTTPS přesměrování
Můžete vybrat, zda a přesměrování provozu HTTP na HTTPS. To vyžaduje použití Azure CDN premium od společnosti Verizon nabídky. Budete muset [chování přepsat HTTP pomocí stroj pravidel Azure CDN](../../cdn/cdn-rules-engine.md) s následující pravidlo:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Cdn-koncový bod name" odkazuje na název, který jste nakonfigurovali pro koncový bod CDN. Tuto hodnotu můžete vybrat z rozevíracího seznamu. "Cesta počátku" odkazuje na cestu v rámci účtu původní úložiště, kde je umístěn statický obsah. Pokud hostujete všechny statického obsahu v jediný kontejner, nahraďte "cesta počátku" název kontejneru.

Podrobnější prohlídku do pravidla, najdete v tématu [Azure CDN pravidla modul funkce](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace
Při přístupu k objektům BLOB pomocí Azure CDN, platíte [objektu Blob úložiště ceny](https://azure.microsoft.com/pricing/details/storage/blobs/) pro provoz mezi uzly okraj a původu (úložiště objektů Blob), a [ceny CDN](https://azure.microsoft.com/pricing/details/cdn/) pro data z uzlu edge.

Řekněme například, že máte účet úložiště na západní USA, který je právě přistupovat pomocí Azure CDN. Pokud někdo ve Spojeném království pokusí o přístup, jeden objektů BLOB v daném účtu úložiště prostřednictvím CDN, Azure nejprve ověří hraniční uzel, který je nejblíže k UK pro tento objekt blob. Pokud najde, se používá tuto kopii objektu blob a použije ceny CDN, protože je přistupuje na CDN. Pokud není nalezen, Azure dojde ke zkopírování objektu blob k uzlu edge, který bude mít za následek poplatky za odchozí data a transakce zadané v cen úložiště Blob a poté přístup k souboru na uzlu edge, což bude mít za následek CDN fakturace.

Při pohledu [CDN stránce s cenami](https://azure.microsoft.com/pricing/details/cdn/), Všimněte si, že podpora HTTPS u vlastních názvů domén je dostupná pouze pro Azure CDN z produktů Verizon (Standard a Premium).

## <a name="next-steps"></a>Další postup
* [Konfigurace vlastního názvu doménu pro koncový bod služby Blob storage](storage-custom-domain-name.md)
* [Hostování statického webu ve službě Azure Storage (Preview)](storage-blob-static-website.md)
