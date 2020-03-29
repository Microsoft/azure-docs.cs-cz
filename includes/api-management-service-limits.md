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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76158888"
---
| Prostředek | Omezení |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximální počet jednotek stupnice | 10 na region<sup>1</sup> |
| Velikost mezipaměti | 5 GiB na jednotku<sup>2</sup> |
| Souběžná back-endová připojení<sup>3</sup> na autoritu HTTP | 2 048 na jednotku<sup>4</sup> |
| Maximální velikost odpovědi uložené v mezipaměti | 2 MiB |
| Maximální velikost dokumentu zásad | 256 KiB<sup>5</sup> |
| Maximální počet vlastních domén brány na instanci služby<sup>6</sup> | 20 |
| Maximální počet certifikátů certifikační autority na instanci služby | 10 |
| Maximální počet instancí služeb na jedno předplatné<sup>7</sup> | 20 |
| Maximální počet předplatných na instanci služby<sup>7</sup> | 500 |
| Maximální počet klientských certifikátů na instanci služby<sup>7</sup> | 50 |
| Maximální počet api na instanci služby<sup>7</sup> | 50 |
| Maximální počet operací rozhraní API na instanci služby<sup>7</sup> | 1 000 |
| Maximální celková doba trvání požadavku<sup>7</sup> | 30 sekund |
| Maximální velikost datové části ve vyrovnávací paměti<sup>7</sup> | 2 MiB |
| Maximální velikost adresy URL požadavku<sup>8</sup> | 4096 bajtů |

<sup>1.</sup> Omezení škálování závisí na cenové úrovni. Informace o cenových úrovních a jejich omezeních škálování najdete v tématu [Ceny správy rozhraní API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2.</sup> Velikost mezipaměti jednotky závisí na cenové úrovni. Informace o cenových úrovních a jejich omezeních škálování najdete v tématu [Ceny správy rozhraní API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3.</sup> Připojení jsou sdružené a znovu použít, pokud explicitně uzavřenback-end.<br/>
<sup>4.</sup> Tento limit je na jednotku úrovní Basic, Standard a Premium. Úroveň Developer je omezena na 1 024. Toto omezení se nevztahuje na úroveň Spotřeba.<br/>
<sup>5.</sup> Toto omezení platí pro úrovně Basic, Standard a Premium. Ve vrstvě Spotřebu je velikost dokumentu zásad omezena na 4 KiB.<br/>
<sup>6.</sup> Tento prostředek je k dispozici pouze na úrovni Premium.<br/>
<sup>7.</sup> Tento prostředek se vztahuje pouze na úroveň Spotřeba.<br/>
<sup>8.</sup> Platí pouze pro úroveň Spotřeba. Obsahuje řetězec dotazu s dlouhým 2048 bajty.<br/>
