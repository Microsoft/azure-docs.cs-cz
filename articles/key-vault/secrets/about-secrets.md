---
title: O tajných klíčech služby Azure Key Vault – Azure Key Vault
description: Přehled rozhraní Azure Key Vault REST a podrobnosti o vývojáři tajných kódů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2578f48ce218a0feaa5fb515ebc5d0e7154802ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424262"
---
# <a name="about-azure-key-vault-secrets"></a>O tajných klíči služby Azure Key Vault

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat několik typů tajných dat:

- Tajné klíče: Poskytuje zabezpečené ukládání tajných kódů, jako jsou hesla a připojovací řetězce databáze.

- Azure Storage: Můžete spravovat klíče účtu Azure Storage za vás. Trezor klíčů může interně vypsat (synchronizovat) klíče pomocí účtu úložiště Azure a pravidelně je regenerovat (otáčet). 

Obecnější informace o trezoru klíčů najdete v tématu [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Následující části nabízejí obecné informace platné v rámci implementace služby Trezor klíčů. 

### <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v trezoru klíčů jsou verzí při každém vytvoření nové instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu je mu přidělen jedinečný identifikátor verze a je označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu dává novému objektu jedinečný identifikátor verze, což způsobuje, že se stane aktuální verzí.  

Objekty v trezoru klíčů lze adresovat pomocí aktuálního identifikátoru nebo identifikátoru specifického pro verzi. Například vzhledem ke klíči s názvem `MasterKey`, provádění operací s aktuálním identifikátorem způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije tuto konkrétní verzi objektu.  

Objekty jsou jednoznačně identifikovány v trezoru klíčů pomocí adresy URL. Žádné dva objekty v systému mají stejnou adresu URL, bez ohledu na geografické umístění. Úplná adresa URL objektu se nazývá Identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje trezor klíčů, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu je malá a velká písmena a neměnné. Identifikátory, které neobsahují verzi objektu, se označují jako základní identifikátory.  

Další informace naleznete v [tématu Ověřování, požadavky a odpovědi](../general/authentication-requests-and-responses.md)

Identifikátor objektu má následující obecný formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Názvy trezorů klíčů jsou vybrány uživatelem a jsou globálně jedinečné.<br /><br /> Název trezoru klíčů musí být řetězec 3-24 znaků, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-type`|Typ objektu, "klíče" nebo "tajné klíče".|  
|`object-name`|A `object-name` je uživatel zadaný název pro a musí být jedinečný v trezoru klíčů. Název musí být řetězec znaků 1-127, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-version`|A `object-version` je systémem generovaný, 32 znakový řetězec identifikátor, který se volitelně používá k adresování jedinečnou verzi objektu.|  

## <a name="key-vault-secrets"></a>Tajné klíče trezoru klíčů 

### <a name="working-with-secrets"></a>Práce s tajemstvími

Z pohledu vývojáře, key vault API přijmout a vrátit tajné hodnoty jako řetězce. Trezor klíčů interně ukládá a spravuje tajné klíče jako sekvence oktetů (8bitových bajtů) s maximální velikostí 25 kB. Služba Trezor klíčů neposkytuje sémantiku pro tajné klíče. Pouze přijímá data, šifruje je, ukládá a vrací tajný identifikátor ("id"). Identifikátor lze použít k načtení tajného klíče později.  

U vysoce citlivých dat by klienti měli zvážit další vrstvy ochrany dat. Příkladem může být šifrování dat pomocí samostatného ochranného klíče před uložením ve službě Key Vault.  

Trezor klíčů také podporuje pole contentType pro tajné klíče. Klienti mohou určit typ obsahu tajného klíče, který vám pomůže při interpretaci tajných dat při jejich načtení. Maximální délka tohoto pole je 255 znaků. Neexistují žádné předem definované hodnoty. Navrhované použití je jako nápověda pro interpretaci tajných dat. Například implementace může ukládat hesla a certifikáty jako tajné klíče, pak použijte toto pole k rozlišení. Neexistují žádné předdefinované hodnoty.  

### <a name="secret-attributes"></a>Tajné atributy

Kromě tajných dat mohou být specifikovány následující atributy:  

- *exp*: IntDate, volitelné, výchozí hodnota je **navždy**. *Exp* (expirační čas) atribut identifikuje čas vypršení platnosti na nebo po kterém by neměla být načtena tajná data, s výjimkou [konkrétních situací](#date-time-controlled-operations). Toto pole slouží pouze pro **informační** účely, protože informuje uživatele služby trezoru klíčů, že konkrétní tajný klíč nesmí být použit. Jeho hodnota musí být číslo obsahující hodnotu IntDate.   
- *nbf*: IntDate, volitelné, výchozí je **nyní**. Atribut *nbf* (ne dříve) určuje dobu, než by tajná data neměla být načtena, s výjimkou [konkrétních situací](#date-time-controlled-operations). Toto pole je pouze pro **informační** účely. Jeho hodnota musí být číslo obsahující hodnotu IntDate. 
- *povoleno*: logická, volitelná, výchozí hodnota **je true**. Tento atribut určuje, zda lze načíst tajná data. Povolený atribut se používá ve spojení s *nbf* a *exp,* když dojde k operaci mezi *nbf* a *exp*, bude povolen pouze v případě, že je povoleno na **hodnotu true**. Operace mimo okno *nbf* a *exp* jsou automaticky zakázány, s výjimkou [konkrétních situací](#date-time-controlled-operations).  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která obsahuje tajné atributy:  

- *vytvořeno*: IntDate, volitelné. Vytvořený atribut označuje, kdy byla tato verze tajného klíče vytvořena. Tato hodnota je null pro tajné klíče vytvořené před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *aktualizováno*: IntDate, volitelné. Aktualizovaný atribut označuje, kdy byla tato verze tajného klíče aktualizována. Tato hodnota je null pro tajné klíče, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.

#### <a name="date-time-controlled-operations"></a>Řízené operace podle data a času

Tajná operace **get** bude fungovat pro dosud neplatné a prošlé tajné kódy, mimo okno *nbf* / *exp.* Volání tajné **ho získat** operaci, pro dosud neplatné tajné, lze použít pro testovací účely. Načítání (**získání**ting) vypršela tajný klíč, lze použít pro operace obnovení.

### <a name="secret-access-control"></a>Řízení přístupu k tajným klíčům

Řízení přístupu pro tajné klíče spravované v trezoru klíčů, je k dispozici na úrovni trezoru klíčů, který obsahuje tyto tajné klíče. Zásady řízení přístupu pro tajné klíče se liší od zásady řízení přístupu pro klíče ve stejném trezoru klíčů. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení tajných kódů a jsou povinni udržovat scénář odpovídající segmentace a správa tajných kódů.   

Následující oprávnění lze použít na základě hlavního povinného v položka řízení přístupu k tajným kódům v úložišti a úzce zrcadlit operace povolené na tajný objekt:  

- Oprávnění pro operace správy tajných správě
  - *get*: Přečtěte si tajemství  
  - *Seznam*: Seznam tajných kódů nebo verzí tajného klíče uloženého v trezoru klíčů  
  - *set*: Vytvoření tajného klíče  
  - *odstranit*: Odstranění tajného klíče  
  - *recover*: Obnovení odstraněného tajného klíče
  - *Zálohování*: Zálohování tajného klíče v trezoru klíčů
  - *Obnovení*: Obnovení zálohovaného tajného klíče do trezoru klíčů

- Oprávnění pro privilegované operace
  - *vymazání*: Vymazání (trvalé odstranění) odstraněného tajného klíče

Další informace o práci s tajnými kódy naleznete [v tématu Tajné operace v odkazu rozhraní REST ROZHRANÍ KEY Vault .](/rest/api/keyvault) Informace o vytváření oprávnění naleznete v [tématu Trezory – Vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a Úložiště – Aktualizovat [zásady přístupu](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tajné značky  
Můžete zadat další metadata specifická pro aplikaci ve formě značek. Trezor klíčů podporuje až 15 značek, z nichž každá může mít název 256 znaků a hodnotu 256 znaků.  

>[!Note]
>Značky jsou čitelné volajícím, pokud mají *seznam* nebo *získat* oprávnění.

## <a name="azure-storage-account-key-management"></a>Správa klíčů účtu Azure Storage

Trezor klíčů může spravovat klíče účtů úložiště Azure:

- Trezor klíčů může interně uvádět (synchronizovat) klíče pomocí účtu úložiště Azure. 
- Trezor klíčů pravidelně generuje (otáčí) klíče.
- Hodnoty klíče jsou nikdy vráceny v reakci na volajícího.
- Trezor klíčů spravuje klíče účtů úložiště i klasických účtů úložiště.

Další informace naleznete v tématu [Klíče účtů úložiště úložiště azure](../secrets/overview-storage-keys.md)key vault )

### <a name="storage-account-access-control"></a>Řízení přístupu k účtu úložiště

Následující oprávnění lze použít při autorizaci uživatele nebo objektu zabezpečení aplikace k provádění operací s účtem spravovaného úložiště:  

- Oprávnění pro účet spravovaného úložiště a operace s definicí SaS
  - *získat*: Získá informace o účtu úložiště 
  - *Seznam*: Seznam účtů úložiště spravovaných trezorem klíčů
  - *aktualizace*: Aktualizace účtu úložiště
  - *odstranění*: Odstranění účtu úložiště  
  - *obnovení*: Obnovení odstraněného účtu úložiště
  - *zálohování*: Zálohování účtu úložiště
  - *obnovení*: Obnovení účtu zálohovaného úložiště do trezoru klíčů
  - *set*: Vytvoření nebo aktualizace účtu úložiště
  - *regeneratekey*: Regenerate a specified key value for a storage account Regeneratekey : Regenerate a specified key value for a storage account Regeneratekey : Regenerate a specified key value for a storage account Regenerate
  - *getsas*: Získat informace o definici SAS pro účet úložiště
  - *listsas*: Seznam definic SAS úložiště pro účet úložiště
  - *deletesas*: Odstranění definice SAS z účtu úložiště
  - *setsas*: Vytvoření nebo aktualizace nové definice/atributů SAS pro účet úložiště

- Oprávnění pro privilegované operace
  - *vymazání*: Vymazání (trvalé odstranění) účtu spravovaného úložiště

Další informace naleznete [v operacích účtu úložiště v odkazu rozhraní REST ROZHRANÍ KEY Vault REST .](/rest/api/keyvault) Informace o vytváření oprávnění naleznete v [tématu Trezory – Vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a Úložiště – Aktualizovat [zásady přístupu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Viz také

- [Ověřování, požadavky a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro Key Vault](../general/developers-guide.md)
