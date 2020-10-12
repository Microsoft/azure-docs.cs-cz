---
title: QnA Maker šifrování dat v klidovém umístění
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém umístění pro QnA Maker a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: e744423e00377ef763824f6e39865e6b3e8ee475
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073535"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker šifrování dat v klidovém umístění

QnA Maker automaticky šifruje vaše data, když se trvale uloží do cloudu, což pomáhá splnit cíle vaší organizace na zabezpečení a dodržování předpisů.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. K dispozici je také možnost spravovat vaše předplatné s vlastními klíči nazvanými klíče spravované zákazníkem (CMK). CMK nabízí větší flexibilitu při vytváření, rotaci, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat. Pokud je pro vaše předplatné nakonfigurované CMK, je k dispozici dvojité šifrování, které nabízí druhou vrstvu ochrany, a přitom vám umožní řídit šifrovací klíč pomocí Azure Key Vault.

QnA Maker používá podporu CMK z Azure Search. V Azure Search je potřeba vytvořit [CMK pomocí Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Tato instance Azure by měla být přidružená k QnA Maker službě, aby byla povolená CMK.

> [!IMPORTANT]
> Prostředek služby Azure Search se musí vytvořit po lednu 2019 a nemůže být ve volné (sdílené) úrovni. V Azure Portal neexistuje žádná podpora pro konfiguraci klíčů spravovaných zákazníkem.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Služba QnA Maker používá CMK ze služby Azure Search. Pomocí těchto kroků povolíte CMKs:

1. Vytvořte novou instanci Azure Search a povolte požadavky uvedené v [požadavcích na klíč spravovaný zákazníkem pro Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Zobrazit nastavení šifrování 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Když vytvoříte prostředek QnA Maker, je automaticky přidružen k instanci Azure Search. Toto nelze použít s CMK. Chcete-li použít CMK, je třeba přidružit nově vytvořenou instanci Azure Search vytvořenou v kroku 1. Konkrétně je potřeba aktualizovat `AzureSearchAdminKey` a `AzureSearchName` v prostředku QnA maker.

   ![Zobrazit nastavení šifrování 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Dále vytvořte nové nastavení aplikace:
   * **Název**: nastavte tuto hodnotu na `CustomerManagedEncryptionKeyUrl`
   * **Hodnota**: Jedná se o hodnotu, kterou jste získali v kroku 1 při vytváření instance Azure Search.

   ![Zobrazit nastavení šifrování 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Až se dokončí, restartujte modul runtime. Služba QnA Maker je teď povolená CMK.

## <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou k dispozici ve všech oblastech Azure Search.

## <a name="encryption-of-data-in-transit"></a>Šifrování dat při přenosu

Portál QnA Maker běží v prohlížeči uživatele. Každá akce aktivuje přímé volání na příslušné rozhraní API služby rozpoznávání. Proto je QnA Maker kompatibilní s přenosem dat.
Vzhledem k tomu, že je služba QnA Maker Portal hostovaná v oblasti Západ USA, není stále ideální pro zákazníky, kteří nejsou US. 

## <a name="next-steps"></a>Další kroky

* [Šifrování v Azure Search pomocí CMKs v Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Šifrování v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)