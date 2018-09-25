---
title: Služba Azure branou – podpora hlavičky protokolu HTTP | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit podporovaných protokolů záhlaví HTTP pomocí přední dveře
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038846"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Služba Azure branou – podpora hlavičky protokolu HTTP
Tento dokument popisuje, jak protokol, který branou služby Azure podporuje s různými částmi volání cesty, jak je uvedeno v následujícím obrázku. Následující části poskytují lepší přehled o hlavičky protokolu HTTP, který podporuje branou.

![Azure hlavičky protokolu HTTP služby branou][1]

>[!WARNING]
>Služba Azure branou neposkytuje záruky v záhlaví HTTP, které tady nejsou uvedené.

## <a name="1-client-to-front-door"></a>1. Klient branou
Přední dveře přijímá většina hlaviček z příchozího požadavku (beze změny jejich), existují však některé vyhrazené hlavičky, které budou odebrány z příchozího požadavku, pokud jsou odesílány. To zahrnuje záhlaví s následující předpony:
 - X-FD *
 - X-MS *

## <a name="2-front-door-to-backend"></a>2. Přední dveře k back-endu

Přední dveře bude obsahovat záhlaví z příchozího požadavku, pokud byly odebrány z důvodu omezení uvedených výše. Přední dveře taky se přidá následující hlavičky:

| Záhlaví  | Příklad a popis |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Toto je odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou. Je velmi důležité pro řešení potíží, protože se používá pro přístup k protokolům vyhledávání.|
| X-MS-RequestChain |  *X-MS-RequestChain: segmentů směrování = 1* </br> Toto je hlavičku, která branou využívá ke zjištění smyčky žádosti a uživatelé neměla by mít závislost na něm. |
| X-MS-Via |  *X-MS-Via: Azure* </br> To přidává přední dveře k označení, že Azure/branou byl zprostředkující příjemce pro žádost mezi klientem a back-endu. |

## <a name="3-front-door-to-client"></a>3. Přední dveře do klienta

Následující části jsou hlavičky, která odesílají z přední dveře do klientů. Záhlaví odeslaných branou z back-end jsou předány také klienta.

| Záhlaví  | Příklad: |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Toto je odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou. Je velmi důležité pro řešení potíží, protože se používá pro přístup k protokolům vyhledávání.|

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
- Přečtěte si [fungování branou](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png