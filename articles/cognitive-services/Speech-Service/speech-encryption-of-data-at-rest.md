---
title: Šifrování dat v klidovém stavu služby Řeči
titleSuffix: Azure Cognitive Services
description: Šifrování dat v klidovém stavu služby řeči.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372356"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Šifrování dat v klidovém stavu služby Řeči

Služba Speech Service automaticky šifruje vaše data, když je trvala do cloudu. Šifrování služby Speech Service chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů.

## <a name="about-cognitive-services-encryption"></a>Šifrování služeb Cognitive Services

Data jsou šifrována a dešifrována pomocí [256bitového](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) šifrování AES kompatibilního se [standardem FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Šifrování a dešifrování jsou transparentní, což znamená, že šifrování a přístup jsou spravovány za vás. Vaše data jsou ve výchozím nastavení zabezpečená a nemusíte upravovat kód nebo aplikace, abyste využili výhod šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Při použití vlastní řeči a vlastní hlas, řeči služba může ukládat následující data v cloudu:  

* Data trasování řeči – pouze v případě, že trasování zapnete pro vlastní koncový bod
* Nahraná data školení a testů

Ve výchozím nastavení jsou vaše data uložena v úložišti společnosti Microsoft a vaše předplatné používá šifrovací klíče spravované společností Microsoft. Máte také možnost připravit si vlastní účet úložiště. Přístup k úložišti spravuje spravovaná identita a služba Rozpoznávání řeči nemůže mít přímý přístup k vašim vlastním datům, jako jsou data trasování řeči, trénovací data vlastního nastavení a vlastní modely.

Další informace o spravované identitě naleznete v tématu [Co jsou spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Přineste si vlastní úložiště (BYOS) pro přizpůsobení a protokolování

Chcete-li požádat o přístup k vlastnímu úložišti, vyplňte a [odešlete službu Speech - přineste si vlastní formulář žádosti o úložiště (BYOS).](https://aka.ms/cogsvc-cmk) Po schválení si budete muset vytvořit vlastní účet úložiště, abyste ukládali data potřebná pro přizpůsobení a protokolování. Při přidávání účtu úložiště prostředek služby Rozpoznávání řeči povolí systém přiřazenou spravovanou identitu. Po povolení systému přiřazené spravované identity bude tento prostředek zaregistrován ve službě Azure Active Directory (AAD). Po registraci bude spravovaná identita mít přístup k účtu úložiště. Další informace o spravovaných identitách najdete tady. Další informace o spravované identitě naleznete v tématu [Co jsou spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Pokud zakážete systém přiřazené spravované identity, přístup k účtu úložiště bude odebrán. To způsobí, že části služby Řeči, které vyžadují přístup k účtu úložiště přestat fungovat.  

## <a name="regional-availability"></a>Regionální dostupnost

BYOS je v současné době k dispozici v těchto oblastech:

* USA (střed) – jih
* USA – západ 2
* USA – východ

## <a name="next-steps"></a>Další kroky

* [Služba řeči - přineste si vlastní formulář žádosti o úložiště (BYOS)](https://aka.ms/cogsvc-cmk)
* [Co jsou spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
