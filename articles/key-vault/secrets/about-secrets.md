---
title: Informace o Azure Key Vault tajných klíčů – Azure Key Vault
description: Přehled Azure Key Vault tajných klíčů.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dc4368075e1cecb43887c9fab21e55b23208dbab
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814923"
---
# <a name="about-azure-key-vault-secrets"></a>Informace o tajných kódech služby Azure Key Vault

[Key Vault](../general/overview.md) poskytuje zabezpečené úložiště generických tajných klíčů, jako jsou hesla a databázové připojovací řetězce.

Z pohledu vývojáře Key Vault rozhraní API akceptuje a vrací tajné hodnoty jako řetězce. Interně Key Vault ukládá a spravuje tajné klíče jako sekvence oktetů (8bitové bajty) a maximální velikost 25k bajtů. Služba Key Vault neposkytuje sémantiku tajných kódů. Přijímá pouze data, šifruje je, ukládá je a vrací tajný identifikátor (ID). Identifikátor lze použít k pozdějšímu načtení tajného klíče.  

U vysoce citlivých dat by klienti měli zvážit další vrstvy ochrany dat. Příkladem může být šifrování dat pomocí samostatného ochranného klíče před uložením ve službě Key Vault.  

Key Vault také podporuje pole contentType pro tajné klíče. Klienti můžou určit typ obsahu tajného kódu, který pomůže při interpretaci tajných dat při jejich načítání. Maximální délka tohoto pole je 255 znaků. Neexistují žádné předem definované hodnoty. Navrhované použití je jako pomocný parametr pro interpretaci tajných dat. Implementace může například ukládat hesla i certifikáty jako tajné klíče a pak je odlišit pomocí tohoto pole. Nejsou k dispozici žádné předdefinované hodnoty.  

## <a name="encryption"></a>Šifrování

Všechny tajné klíče ve vašem Key Vault jsou uložené zašifrované. Toto šifrování je transparentní a nevyžaduje od uživatele žádnou akci. Služba Azure Key Vault šifrování tajných kódů při jejich přidávání a dešifruje automaticky při jejich čtení. Šifrovací klíč je pro každý Trezor klíčů jedinečný.

## <a name="secret-attributes"></a>Tajné atributy

Kromě tajných dat lze zadat následující atributy:  

- *exp*: IntDate, volitelné, výchozí je **trvale**. Atribut *exp* (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí by se neměla načíst tajná data, s výjimkou [zvláštních situací](#date-time-controlled-operations). Toto pole slouží pouze k **informativním** účelům, protože informuje uživatele služby trezoru klíčů, že není možné použít konkrétní tajný klíč. Jeho hodnota musí být číslo obsahující hodnotu IntDate.   
- *NBF*: IntDate, volitelné, výchozí nastavení je **teď**. Atribut *NBF* (nikoli dřív) určuje čas, před kterým by se neměla načíst tajná data, s výjimkou [zvláštních situací](#date-time-controlled-operations). Toto pole slouží pouze k **informativním** účelům. Jeho hodnota musí být číslo obsahující hodnotu IntDate. 
- *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Tento atribut určuje, zda mohou být načtena tajná data. Atribut enabled se používá ve spojení s *NBF* a *exp* , pokud dojde k operaci mezi *NBF* a *exp*, ale bude povolená jenom v případě, že je povolená možnost nastavená na **hodnotu true**. Operace mimo okno *NBF* a *exp* se automaticky nepovolují, s výjimkou [zvláštních situací](#date-time-controlled-operations).  

Existují další atributy jen pro čtení, které jsou zahrnuty v jakékoli odpovědi, která obsahuje tajné atributy:  

- *Vytvořeno*: IntDate, volitelné. Vytvořený atribut označuje, kdy byla tato verze tajného kódu vytvořena. Tato hodnota je null pro tajné klíče vytvořené před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizováno*: IntDate, volitelné. Aktualizovaný atribut určuje, kdy byla tato verze tajného kódu aktualizována. Tato hodnota je null pro tajné kódy, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.

Informace o běžných atributech pro každý typ objektu trezoru klíčů najdete v článku [přehled Azure Key Vault klíčů, tajných klíčů a certifikátů](../general/about-keys-secrets-certificates.md) .

### <a name="date-time-controlled-operations"></a>Operace kontrolovaného data a času

Operace **Get** tajného klíče bude fungovat pro tajné klíče, které ještě nejsou platné a jejichž platnost vypršela, a to mimo okno exp pro *NBF*  /   . Volání operace **Get** tajného klíče, pro neplatný tajný klíč, lze použít pro testovací účely. Načtení (**získání** nocení) tajného kódu, jehož platnost vypršela, lze použít pro operace obnovení.

## <a name="secret-access-control"></a>Řízení přístupu k tajným klíčům

Access Control pro tajné klíče spravované v Key Vault je k dispozici na úrovni Key Vault, které tyto tajné kódy obsahují. Zásada řízení přístupu tajných klíčů je odlišná od zásad řízení přístupu pro klíče ve stejné Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro blokování tajných kódů a jsou vyžadovány pro udržení vhodného segmentace a správy tajných kódů.   

Následující oprávnění se dají použít, pro jednotlivé hlavní objekty, v položce řízení přístupu tajných klíčů v trezoru a při tom pozorně zrcadlit operace povolené u tajného objektu:  

- Oprávnění pro operace správy tajných klíčů
  - *získat*: čtení tajného klíče  
  - *seznam*: výpis tajných kódů nebo verzí tajného kódu uloženého v Key Vault  
  - *nastavit*: vytvořit tajný kód  
  - *Odstranit*: odstranění tajného klíče  
  - *obnovení*: obnovení odstraněných tajných klíčů
  - *zálohování*: zálohování tajného klíče v trezoru klíčů
  - *obnovení*: obnovení zálohovaného tajného klíče do trezoru klíčů

- Oprávnění pro privilegované operace
  - *vyprázdnit*: vyprázdnit (trvale odstranit) odstraněný tajný kód

Další informace o práci s tajnými kódy naleznete [v tématu tajné operace v odkazu na Key Vault REST API](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy). 

Návody k řízení přístupu v Key Vault:
- [Přiřazení zásad přístupu Key Vault pomocí rozhraní příkazového řádku](../general/assign-access-policy-cli.md)
- [Přiřazení zásad přístupu Key Vault pomocí PowerShellu](../general/assign-access-policy-powershell.md)
- [Přiřazení zásady přístupu Key Vault pomocí Azure Portal](../general/assign-access-policy-portal.md)
- [Poskytnutí přístupu k klíčům Key Vault, certifikátům a tajným klíčům pomocí řízení přístupu na základě role Azure](../general/rbac-guide.md)

## <a name="secret-tags"></a>Tajné značky  
Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 název znaku a 256 znaková hodnota.  

>[!Note]
>Značky mohou číst volající, pokud mají oprávnění *list* nebo *Get* .

## <a name="azure-storage-account-key-management"></a>Správa klíčů účtu Azure Storage

Key Vault můžou spravovat klíče [účtu úložiště Azure](../../storage/common/storage-account-overview.md) :

- Interně Key Vault možné vypsat (synchronizovat) klíče s účtem služby Azure Storage. 
- Key Vault znovu vygeneruje (otáčí) klíče pravidelně.
- Hodnoty klíče se nikdy nevrátí v reakci na volajícího.
- Key Vault spravuje klíče účtů úložiště i klasických účtů úložiště.

Další informace naleznete v tématu:
- [Přístupové klíče účtu úložiště](../../storage/common/storage-account-keys-manage.md)
- [Správa klíčů účtu úložiště v Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Řízení přístupu k účtu úložiště

Následující oprávnění se dají použít při autorizaci objektu zabezpečení uživatele nebo aplikace k provádění operací na spravovaném účtu úložiště:  

- Oprávnění pro spravovaný účet úložiště a operace definice SaS
  - *Get*: načte informace o účtu úložiště. 
  - *seznam*: výpis účtů úložiště spravovaných Key Vault
  - *aktualizace*: aktualizace účtu úložiště
  - *Odstranit*: odstranění účtu úložiště  
  - *obnovení*: obnovení odstraněného účtu úložiště
  - *zálohování*: zálohování účtu úložiště
  - *obnovení*: obnovení účtu zálohovaného úložiště do Key Vault
  - *Nastavení*: vytvoření nebo aktualizace účtu úložiště
  - *RegenerateKey*: znovu vygenerujte zadanou hodnotu klíče pro účet úložiště.
  - *getsas*: Získejte informace o definici SAS pro účet úložiště.
  - *listsas*: Seznamte se s definicemi SAS úložiště pro účet úložiště.
  - *deletesas*: odstranění definice SAS z účtu úložiště
  - *setsas*: vytvoření nebo aktualizace nové definice nebo atributů SAS pro účet úložiště

- Oprávnění pro privilegované operace
  - *vyprázdnit*: vyprázdnit (trvale odstranit) spravovaný účet úložiště

Další informace najdete v referenčních informacích o [operacích účtu úložiště v Key Vault REST API](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

Návody k řízení přístupu v Key Vault:
- [Přiřazení zásad přístupu Key Vault pomocí rozhraní příkazového řádku](../general/assign-access-policy-cli.md)
- [Přiřazení zásad přístupu Key Vault pomocí PowerShellu](../general/assign-access-policy-powershell.md)
- [Přiřazení zásady přístupu Key Vault pomocí Azure Portal](../general/assign-access-policy-portal.md)
- [Poskytnutí přístupu k klíčům Key Vault, certifikátům a tajným klíčům pomocí řízení přístupu na základě role Azure](../general/rbac-guide.md)


## <a name="next-steps"></a>Další kroky

- [Informace o službě Key Vault](../general/overview.md)
- [Informace o klíčích, tajných kódech a certifikátech](../general/about-keys-secrets-certificates.md)
- [Informace o klíčích](../keys/about-keys.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Zabezpečení přístupu k trezoru klíčů](../general/security-features.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)