---
title: QnA Maker šifrování dat v klidovém umístění
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém umístění pro QnA Maker a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 1ee3c3942ee7d01fa174947f5d9c278cddaf0424
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376909"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker šifrování dat v klidovém umístění

QnA Maker automaticky šifruje vaše data, když se trvale uloží do cloudu, což pomáhá splnit cíle vaší organizace na zabezpečení a dodržování předpisů.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. K dispozici je také možnost spravovat vaše předplatné s vlastními klíči nazvanými klíče spravované zákazníkem (CMK). CMK nabízí větší flexibilitu při vytváření, rotaci, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat. Pokud je pro vaše předplatné nakonfigurované CMK, je k dispozici dvojité šifrování, které nabízí druhou vrstvu ochrany, a přitom vám umožní řídit šifrovací klíč pomocí Azure Key Vault.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

QnA Maker používá podporu CMK z Azure Search. Nakonfigurujte [CMK v Azure Search pomocí Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Tato instance Azure by měla být přidružená k QnA Maker službě, aby byla povolená CMK.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

QnA Maker používá [podporu CMK z Azure Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)a automaticky přidružuje poskytnutou CMK k šifrování dat uložených v indexu Azure Search.

---

> [!IMPORTANT]
> Prostředek služby Azure Search se musí vytvořit po lednu 2019 a nemůže být ve volné (sdílené) úrovni. V Azure Portal neexistuje žádná podpora pro konfiguraci klíčů spravovaných zákazníkem.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Služba QnA Maker používá CMK ze služby Azure Search. Pomocí těchto kroků povolíte CMKs:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

1. Vytvořte novou instanci Azure Search a povolte požadavky uvedené v [požadavcích na klíč spravovaný zákazníkem pro Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Zobrazit nastavení šifrování 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Když vytvoříte prostředek QnA Maker, je automaticky přidružen k instanci Azure Search. Tuto instanci nelze použít s CMK. Chcete-li použít CMK, je třeba přidružit nově vytvořenou instanci Azure Search vytvořenou v kroku 1. Konkrétně je potřeba aktualizovat `AzureSearchAdminKey` a `AzureSearchName` v prostředku QnA maker.

   ![Zobrazit nastavení šifrování 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Dále vytvořte nové nastavení aplikace:
   * **Název** : nastavte na `CustomerManagedEncryptionKeyUrl`
   * **Hodnota** : použijte hodnotu, kterou jste získali v kroku 1 při vytváření instance Azure Search.

   ![Zobrazit nastavení šifrování 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Až se dokončí, restartujte modul runtime. Služba QnA Maker je teď povolená CMK.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

1.  Přejdete na kartu **šifrování** služby QnA maker spravovaná (Preview).
2.  Vyberte možnost **Customer Managed Keys** . Zadejte podrobnosti o [klíčích spravovaných zákazníkem](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal) a klikněte na **Uložit**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="Nastavení CMK spravovaného (Preview) QnA Maker" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Po úspěšném uložení se CMK použije k zašifrování dat uložených v indexu Azure Search.

> [!IMPORTANT]
> Doporučuje se nastavit CMK v nové službě Azure Kognitivní hledání před tím, než se vytvoří jakékoli znalostní báze. Pokud jste nastavili CMK ve službě QnA Maker se stávajícími znalostními bázemi, můžete k nim ztratit přístup. Přečtěte si další informace o [práci s šifrovaným obsahem](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#work-with-encrypted-content) v Azure rozpoznávání vyhledávání.

> [!NOTE]
> Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o Cognitive Services Customer-Managed klíč](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou k dispozici ve všech oblastech Azure Search.

## <a name="encryption-of-data-in-transit"></a>Šifrování dat při přenosu

Portál QnA Maker běží v prohlížeči uživatele. Každá akce aktivuje přímé volání do příslušného rozhraní API služby pro rozpoznávání. Proto je QnA Maker kompatibilní s přenosem dat.
Vzhledem k tomu, že je služba QnA Maker Portal hostovaná v oblasti Západ USA, není stále ideální pro zákazníky, kteří nejsou US. 

## <a name="next-steps"></a>Další kroky

* [Šifrování v Azure Search pomocí CMKs v Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Šifrování v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)