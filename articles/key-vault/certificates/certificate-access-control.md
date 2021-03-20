---
title: O řízení přístupu k certifikátům Azure Key Vault
description: Přehled řízení přístupu k certifikátům Azure Key Vault
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92128634"
---
# <a name="certificate-access-control"></a>Access Control certifikátu

 Řízení přístupu k certifikátům zajišťuje služba Key Vault, ve které se certifikáty nacházejí. Zásady řízení přístupu pro certifikáty se liší od zásad řízení přístupu pro klíče a tajné klíče ve stejné Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro ukládání certifikátů, aby bylo možné zachovat vhodné segmentaci a správu certifikátů.  

 Následující oprávnění se dají použít, pro jednotlivé hlavní objekty, v položce řízení přístupu tajných klíčů v trezoru klíčů a pozorně zrcadlí operace povolené u tajného objektu:  

- Oprávnění pro operace správy certifikátů
  - **získání**: získání aktuální verze certifikátu nebo libovolné verze certifikátu
  - **seznam**: vypíše aktuální certifikáty nebo verze certifikátu.  
  - **aktualizace**: aktualizace certifikátu
  - **vytvořit**: vytvoření certifikátu Key Vault
  - **Import**: Import materiálu certifikátů do certifikátu Key Vault
  - **Odstranit**: odstranit certifikát, jeho zásadu a všechny jeho verze  
  - **obnovení**: obnovit odstraněný certifikát
  - **zálohování**: zálohování certifikátu v trezoru klíčů
  - **obnovení**: obnovení zálohovaného certifikátu do trezoru klíčů
  - **managecontacts**: Správa kontaktů certifikátů Key Vault  
  - **manageissuers**: Správa certifikačních autorit/vystavitelů Key Vault
  - **getissuer**: získání autority nebo vystavitelů certifikátu
  - **listissuers**: výpis autorit/vystavitelů certifikátů  
  - **setissuers**: vytvoření nebo aktualizace autorit/vystavitelů certifikátu Key Vault  
  - **deleteissuers**: odstranění autorit/vystavitelů certifikátu Key Vault  
 
- Oprávnění pro privilegované operace
  - **vyprázdnit**: vyprázdnit (trvale odstranit) odstraněný certifikát

Další informace najdete v referenčních informacích o [operacích certifikátu v REST API Key Vault](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – zásady přístupu k aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Řešení potíží
Kvůli chybějícím zásadám přístupu se může zobrazit chyba. ```Error type : Access denied or user is unauthorized to create certificate```Chcete-li například vyřešit tuto chybu, budete muset přidat certifikáty/vytvořit oprávnění.

## <a name="next-steps"></a>Další kroky

- [Informace o službě Key Vault](../general/overview.md)
- [Informace o klíčích, tajných kódech a certifikátech](../general/about-keys-secrets-certificates.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
