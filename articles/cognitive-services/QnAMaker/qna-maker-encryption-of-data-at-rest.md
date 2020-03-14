---
title: QnA Maker šifrování dat v klidovém umístění
titleSuffix: Azure Cognitive Services
description: QnA Maker šifrování dat v klidovém umístění.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372092"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker šifrování dat v klidovém umístění

QnA Maker automaticky šifruje vaše data, když se trvale uloží do cloudu, což pomáhá splnit cíle vaší organizace na zabezpečení a dodržování předpisů.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení používá vaše předplatné šifrovací klíče spravované Microsoftem. K dispozici je také možnost Spravovat předplatné s vlastními klíči. Klíče spravované zákazníkem (CMK) nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.

QnA Maker používá podporu CMK z Azure Search. V Azure Search je potřeba vytvořit [CMK pomocí Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Tato instance Azure by měla být přidružená k QnA Maker službě, aby byla povolená CMK.

> [!IMPORTANT]
> Prostředek služby Azure Search se musí vytvořit po lednu 2019 a nemůže být ve volné (sdílené) úrovni. V Azure Portal neexistuje žádná podpora pro konfiguraci klíčů spravovaných zákazníkem.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Služba QnA Maker používá CMK ze služby Azure Search. Pomocí těchto kroků povolíte CMKs:

1. Vytvořte novou instanci Azure Search a povolte požadavky uvedené v [požadavcích na klíč spravovaný zákazníkem pro Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Když vytvoříte prostředek QnA Maker, je automaticky přidružen k instanci Azure Search. Toto nelze použít s CMK. Chcete-li použít CMK, je třeba přidružit nově vytvořenou instanci Azure Search vytvořenou v kroku 1. Konkrétně je potřeba aktualizovat `AzureSearchAdminKey` a `AzureSearchName` v prostředku QnA Maker.

   ![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Dále vytvořte nové nastavení aplikace:
   * **Název**: nastavte tuto hodnotu na `CustomerManagedEncryptionKeyUrl`
   * **Hodnota**: Jedná se o hodnotu, kterou jste získali v kroku 1 při vytváření instance Azure Search.

   ![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Až se dokončí, restartujte modul runtime. Služba QnA Maker je teď povolená CMK.

## <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou k dispozici ve všech oblastech Azure Search.

## <a name="next-steps"></a>Další kroky

* [Šifrování v Azure Search pomocí CMKs v Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Šifrování dat v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
