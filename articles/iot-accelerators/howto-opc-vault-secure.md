---
title: Zabezpečte komunikaci zařízení OPC UA s OPC Vault - Azure | Dokumenty společnosti Microsoft
description: Jak zaregistrovat aplikace OPC UA a jak vydávat podepsané certifikáty žádostí pro vaše zařízení OPC UA s OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454197"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Použití služby správy certifikátů OPC Vault

Tento článek vysvětluje, jak zaregistrovat aplikace a jak vydávat podepsané certifikáty aplikací pro vaše zařízení OPC UA.

## <a name="prerequisites"></a>Požadavky

### <a name="deploy-the-certificate-management-service"></a>Nasazení služby správy certifikátů

Nejprve nasadit službu do cloudu Azure. Podrobnosti naleznete [v tématu Deploy the OPC Vault certificate management service](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Vytvoření certifikátu certifikační autority vystavitra

Pokud jste tak dosud neučinili, vytvořte certifikát certifikační autority vystavithona. Podrobnosti naleznete v [tématu Vytvoření a správa certifikátu vystavittele pro OPC Vault](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Zabezpečené aplikace OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Krok 1: Registrace aplikace OPC UA 

> [!IMPORTANT]
> Role zapisovače je vyžadována k registraci aplikace.

1. Otevřete certifikační `https://myResourceGroup-app.azurewebsites.net`službu na vysmívejte a přihlaste se.
2. Přejděte na **Zaregistrovat nový**. Pro registraci aplikace musí mít uživatel přiřazenalespoň roli zapisovače.
2. Vstupní formulář se řídí konvencemi pojmenování v OPC UA. Například na následujícím snímku obrazovky je zobrazeno nastavení ukázky [referenčního serveru OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) v zásobníku OPC UA .NET Standard:

   ![Snímek obrazovky s registrací referenčního serveru UA](media/howto-opc-vault-secure/reference-server-registration.png "Registrace referenčního serveru UA")

5. Výběrem **možnosti Registrovat** zaregistrujete aplikaci v databázi aplikace certifikační služby. Pracovní postup přímo vede uživatele k dalšímu kroku a požádá o podepsaný certifikát pro aplikaci.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Krok 2: Zabezpečení aplikace pomocí certifikátu žádosti podepsaného certifikační autoritou

Zabezpečte aplikaci OPC UA vydáním podepsaného certifikátu na základě žádosti o podpis certifikátu (CSR). Případně můžete požádat o nový pár klíčů, který obsahuje nový soukromý klíč ve formátu PFX nebo PEM. Informace o tom, která metoda je podporována pro vaši aplikaci, naleznete v dokumentaci k zařízení OPC UA. Obecně se doporučuje metoda CSR, protože nevyžaduje přenos soukromého klíče přes drát.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Žádost o nový certifikát s novým párem klíčů

1. Přejděte na **aplikace**.
3. Vyberte **Nový požadavek** pro uvedenou aplikaci.

   ![Snímek obrazovky s žádostí o nový certifikát](media/howto-opc-vault-secure/request-new-certificate.png "Požádat o nový certifikát")

3. Vyberte **Požádat o nový klíčPair a certifikát** požádat o soukromý klíč a nový podepsaný certifikát s veřejným klíčem pro vaši aplikaci.

   ![Snímek obrazovky s generováním nového páru klíčů a certifikátu](media/howto-opc-vault-secure/generate-new-key-pair.png "Generovat nový pár klíčů")

4. Vyplňte formulář s předmětem a doménovými jmény. Pro soukromý klíč zvolte PEM nebo PFX s heslem. Chcete-li vytvořit žádost o certifikát, vyberte **generovat novou dvojici** klíčů.

   ![Snímek obrazovky s podrobnostmi o žádosti o certifikát zobrazení](media/howto-opc-vault-secure/approve-reject.png "Schválit certifikát")

5. Schválení vyžaduje uživatele s rolí Schvalovatel a s oprávněními k podpisu v trezoru klíčů Azure. V typickém pracovním postupu by měly být role Schvalovatel a žadatel přiřazeny různým uživatelům. Chcete-li spustit nebo zrušit skutečné vytvoření dvojice klíčů a operace podepisování, vyberte **možnost Schválit** nebo **Odmítnout.** Nový pár klíčů se vytvoří a bezpečně uloží v úložišti klíčů Azure, dokud ji žadatel o certifikát nestáhne. Výsledný certifikát s veřejným klíčem je podepsán certifikačníautoritou. Tyto operace může trvat několik sekund k dokončení.

   ![Snímek obrazovky se zobrazením podrobností žádosti o certifikát se zprávou schválení v dolní části](media/howto-opc-vault-secure/view-key-pair.png "Zobrazit dvojici klíčů")

7. Výsledný soukromý klíč (PFX nebo PEM) a certifikát (DER) lze stáhnout zde ve formátu vybraném jako binární soubor ke stažení. Verze s kódováním base64 je také k dispozici, například pro kopírování a vkládání certifikátu do příkazového řádku nebo textové položky. 
8. Po bezpečném stažení a uložení soukromého klíče můžete vybrat **možnost Odstranit soukromý klíč**. Certifikát s veřejným klíčem zůstává k dispozici pro budoucí použití.
9. Z důvodu použití certifikátu podepsaného certifikační autoritou je třeba zde stáhnout také certifikát certifikační autority a seznam odvolaných certifikátů (CRL).

Nyní záleží na zařízení OPC UA, jak použít nový pár klíčů. Certifikát certifikační autority a seznam crl jsou `trusted` obvykle zkopírovány do složky, zatímco `own` veřejné a soukromé klíče certifikátu aplikace jsou použity pro složku v úložišti certifikátů. Některá zařízení již mohou podporovat aktualizace serveru pro nabízený certifikát. Podívejte se do dokumentace vašeho Zařízení OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Žádost o nový certifikát s zástupcem 

1. Přejděte na **aplikace**.
3. Vyberte **Nový požadavek** pro uvedenou aplikaci.

   ![Snímek obrazovky s žádostí o nový certifikát](media/howto-opc-vault-secure/request-new-certificate.png "Požádat o nový certifikát")

3. Chcete-li požádat o nový podepsaný certifikát pro vaši aplikaci, vyberte **vyberte požádat o nový certifikát s žádostí o podpis.**

   ![Snímek obrazovky s vytvořením nového certifikátu](media/howto-opc-vault-secure/generate-new-certificate.png "Generovat nový certifikát")

4. Nahrajte zástupce namísto výběrem místního souboru nebo vložením csr kódovaného base64 do formuláře. Vyberte **generovat nový certifikát**.

   ![Snímek obrazovky s podrobnostmi o žádosti o certifikát zobrazení](media/howto-opc-vault-secure/approve-reject-csr.png "Schválit zástupce odpovědnosti za jisíčnou odpovědnost")

5. Schválení vyžaduje uživatele s rolí Schvalovatel a s oprávněními k podpisu v trezoru klíčů Azure. Chcete-li spustit nebo zrušit skutečnou operaci podepisování, vyberte **možnost Schválit** nebo **Odmítnout.** Výsledný certifikát s veřejným klíčem je podepsán certifikačníautoritou. Dokončení této operace může trvat několik sekund.

   ![Snímek obrazovky se zobrazením podrobností žádosti o certifikát se zprávou schválení v dolní části](media/howto-opc-vault-secure/view-cert-csr.png "Zobrazit certifikát")

6. Výsledný certifikát (DER) lze stáhnout zde jako binární soubor. Verze s kódováním base64 je také k dispozici, například pro kopírování a vkládání certifikátu do příkazového řádku nebo textové položky. 
10. Po bezpečném stažení a uložení certifikátu můžete vybrat **možnost Odstranit certifikát**.
11. Z důvodu použití certifikátu podepsaného certifikační autoritou je třeba zde stáhnout také certifikát certifikační autority a seznam odvolaných certifikátů.

Nyní záleží na zařízení OPC UA, jak použít nový certifikát. Certifikát certifikační autority a seznam crl se `trusted` obvykle zkopírují do složky, zatímco certifikát aplikace je použit pro `own` složku v úložišti certifikátů. Některá zařízení již mohou podporovat aktualizace serveru pro nabízený certifikát. Podívejte se do dokumentace vašeho Zařízení OPC UA.

### <a name="step-3-device-secured"></a>Krok 3: Zařízení zabezpečeno

Zařízení OPC UA je nyní připraveno ke komunikaci s ostatními zařízeními OPC UA zabezpečenými certifikáty podepsanými certifikační autoritou bez další konfigurace.

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili, jak zabezpečit zařízení OPC UA, můžete:

> [!div class="nextstepaction"]
> [Spuštění zabezpečené služby správy certifikátů](howto-opc-vault-secure-ca.md)
