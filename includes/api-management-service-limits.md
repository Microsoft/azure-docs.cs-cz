---
title: zahrnout soubor
description: zahrnout soubor
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158888"
---
| Prostředek | škálování |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximální počet jednotek škálování | 10 na oblast<sup>1</sup> |
| Velikost mezipaměti | 5 GiB na jednotku<sup>2</sup> |
| Souběžná připojení back-end<sup>3</sup> na AUTORITu http | 2 048 na jednotku<sup>4</sup> |
| Maximální velikost odpovědi v mezipaměti | 2 MiB |
| Maximální velikost dokumentu zásad | 256 KiB<sup>5</sup> |
| Maximální počet domén vlastní brány na instanci služby<sup>6</sup> | 20 |
| Maximální počet certifikátů certifikační autority na instanci služby | 10 |
| Maximální počet instancí služby na předplatné<sup>7</sup> | 20 |
| Maximální počet předplatných na instanci služby<sup>7</sup> | 500 |
| Maximální počet klientských certifikátů na instanci služby<sup>7</sup> | 50 |
| Maximální počet rozhraní API na instanci služby<sup>7</sup> | 50 |
| Maximální počet operací rozhraní API na instanci služby<sup>7</sup> | 1 000 |
| Maximální celková doba trvání žádosti<sup>7</sup> | 30 sekund |
| Maximální velikost datové části vyrovnávací paměti<sup>7</sup> | 2 MiB |
| Maximální velikost adresy URL požadavku<sup>8</sup> | 4096 bajtů |

<sup>1</sup> Omezení škálování závisí na cenové úrovni. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování, přečtěte si téma [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> . Velikost mezipaměti pro jednotky závisí na cenové úrovni. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování, přečtěte si téma [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> . Připojení se ve fondu a znovu používají, pokud není explicitně Uzavřeno pomocí back-endu.<br/>
<sup>4</sup> . Toto omezení je na jednotku úrovně Basic, Standard a Premium. Úroveň pro vývojáře je omezená na 1 024. Toto omezení se nevztahuje na úroveň spotřeby.<br/>
<sup>5</sup> . Toto omezení se vztahuje na úrovně Basic, Standard a Premium. V úrovni spotřeby je velikost dokumentu zásad omezená na 4 KiB.<br/>
<sup>6</sup> Tento prostředek je k dispozici pouze v úrovni Premium.<br/>
<sup>7</sup> Tento prostředek se vztahuje pouze na úroveň spotřeby.<br/>
<sup>8</sup> Platí jenom pro úroveň spotřeby. Zahrnuje až 2048 bajtů dlouhého řetězce dotazu.<br/>
