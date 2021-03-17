---
title: zahrnout soubor
description: zahrnout soubor
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 02/19/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: ebaca1f39b16e4a06b5dcaa4e5f1de07122c6c89
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622279"
---
| Prostředek | Omezení |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximální počet jednotek škálování | 10 na oblast<sup>1</sup> |
| Velikost mezipaměti | 5 GiB na jednotku<sup>2</sup> |
| Souběžná připojení back-end<sup>3</sup> na AUTORITu http | 2 048 na jednotku<sup>4</sup> |
| Maximální velikost odpovědi v mezipaměti | 2 MiB |
| Maximální velikost dokumentu zásad | 256 KiB<sup>5</sup> |
| Maximální počet domén vlastní brány na instanci služby<sup>6</sup> | 20 |
| Maximální počet certifikátů certifikační autority na instanci služby<sup>7</sup> | 10 |
| Maximální počet instancí služby na předplatné<sup>8</sup> | 20 |
| Maximální počet předplatných na instanci služby<sup>8</sup> | 500 |
| Maximální počet klientských certifikátů na instanci služby<sup>8</sup> | 50 |
| Maximální počet rozhraní API na instanci služby<sup>8</sup> | 50 |
| Maximální počet operací rozhraní API na instanci služby<sup>8</sup> | 1 000 |
| Maximální celková doba trvání žádosti<sup>8</sup> | 30 sekund |
| Maximální velikost datové části vyrovnávací paměti<sup>8</sup> | 2 MiB |
| Maximální velikost adresy URL požadavku<sup>9</sup> | 4096 bajtů |
| Maximální délka segmentu cesty k adrese URL<sup>10</sup> | 260 znaků |
| Maximální velikost schématu rozhraní API používaného [zásadami ověřování](../articles/api-management/validation-policies.md)<sup>10</sup> | 4 MB |
| Maximální velikost textu žádosti nebo odpovědi v [zásadách ověřování – obsah](../articles/api-management/validation-policies.md#validate-content) | 100 KB |
| Maximální počet bran pro samoobslužné hostování<sup>11</sup> | 25 |

<sup>1</sup> Omezení škálování závisí na cenové úrovni. Podrobnosti o cenových úrovních a jejich omezeních škálování najdete v tématu [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> . Velikost mezipaměti pro jednotky závisí na cenové úrovni. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování, přečtěte si téma [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> . Připojení se ve fondu a znovu používají, pokud není explicitně Uzavřeno pomocí back-endu.<br/>
<sup>4</sup> . Toto omezení je na jednotku úrovně Basic, Standard a Premium. Úroveň pro vývojáře je omezená na 1 024. Toto omezení se nevztahuje na úroveň spotřeby.<br/>
<sup>5</sup> . Toto omezení se vztahuje na úrovně Basic, Standard a Premium. V úrovni spotřeby je velikost dokumentu zásad omezená na 16 KiB.<br/>
<sup>6</sup> Více vlastních domén je podporováno pouze v úrovni pro vývojáře a úrovně Premium.<br/>
<sup>7</sup> Certifikáty certifikační autority se v úrovni spotřeby nepodporují.<br/>
<sup>8</sup> Toto omezení se vztahuje pouze na úroveň spotřeby. V těchto kategoriích neexistují žádná omezení pro jiné úrovně.<br/>
<sup>9</sup> Platí jenom pro úroveň spotřeby. Zahrnuje až 2048 bajtů dlouhého řetězce dotazu.<br/>
<sup>10</sup> Pokud chcete tento limit zvýšit, obraťte se prosím na [podporu](https://azure.microsoft.com/support/options/).<br/>
<sup>11</sup> Samoobslužné brány se podporují jenom v úrovních pro vývojáře a úrovně Premium. Omezení platí pro počet [prostředků samoobslužné brány](/rest/api/apimanagement/2019-12-01/gateway). Pokud chcete tento limit zvýšit, obraťte se prosím na [podporu](https://azure.microsoft.com/support/options/). Všimněte si, že počet uzlů (nebo replik) přidružených k prostředku samoobslužné brány je neomezený na úrovni Premium a omezené v jednom uzlu ve vrstvě pro vývojáře.