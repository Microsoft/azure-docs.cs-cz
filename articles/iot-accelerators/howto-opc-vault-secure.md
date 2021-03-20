---
title: Zabezpečení komunikace zařízení OPC UA pomocí trezoru OPC – Azure | Microsoft Docs
description: Jak registrovat aplikace OPC UA a jak vydávat podepsané aplikace certifikáty pro zařízení OPC UA s úložištěm OPC
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92926406"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Použití služby správy certifikátů trezoru OPC

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek vysvětluje, jak registrovat aplikace a jak vydávat podepsané certifikáty aplikací pro zařízení OPC UA.

## <a name="prerequisites"></a>Předpoklady

### <a name="deploy-the-certificate-management-service"></a>Nasazení služby správy certifikátů

Nejdřív nasaďte službu do cloudu Azure. Podrobnosti najdete v tématu [nasazení služby správy certifikátů trezoru OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Vytvoření certifikátu certifikační autority vystavitele

Pokud jste to ještě neudělali, vytvořte certifikát certifikační autority vystavitele. Podrobnosti najdete v tématu [Vytvoření a Správa certifikátu vystavitele pro OPC trezor](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Zabezpečení aplikací OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Krok 1: registrace aplikace OPC UA 

> [!IMPORTANT]
> K registraci aplikace je nutná role zapisovače.

1. Otevřete svou službu Certificate Service na adrese `https://myResourceGroup-app.azurewebsites.net` a přihlaste se.
2. Přejít na **zaregistrovat nové**. Pro registraci aplikace musí mít uživatel alespoň přiřazenou roli zapisovače.
2. Formulář pro zadávání názvů řídí konvence pojmenování v OPC UA. Například na následujícím snímku obrazovky se zobrazí nastavení pro ukázku [referenčního serveru OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) v OPC UA .NET Standard stack:

   ![Snímek obrazovky s registrací referenčního serveru UA](media/howto-opc-vault-secure/reference-server-registration.png "Registrace referenčního serveru UA")

5. Vyberte **Registrovat** pro registraci aplikace v databázi aplikace služby Certificate Service. Pracovní postup přímo provede uživatele k dalšímu kroku pro vyžádání podepsaného certifikátu pro aplikaci.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Krok 2: zabezpečení aplikace pomocí certifikátu aplikace podepsaného certifikační autoritou

Zabezpečte svou aplikaci OPC UA vyvoláním podepsaného certifikátu na základě žádosti o podepsání certifikátu (CSR). Alternativně můžete požádat o novou dvojici klíčů, která zahrnuje nový privátní klíč ve formátu PFX nebo PEM. Informace o tom, která metoda je pro vaši aplikaci podporovaná, najdete v dokumentaci zařízení OPC UA. Obecně platí, že je doporučována metoda CSR, protože nevyžaduje přenos privátního klíče přes kabel.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Požádat o nový certifikát s novým souboru KeyPair

1. Přejít na **aplikace**.
3. Vyberte **nový požadavek** na uvedenou aplikaci.

   ![Snímek obrazovky žádosti o nový certifikát](media/howto-opc-vault-secure/request-new-certificate.png "Požádat o nový certifikát")

3. Vyberte **požádat o nový souboru KeyPair a certifikát** pro vyžádání privátního klíče a nového podepsaného certifikátu s veřejným klíčem pro vaši aplikaci.

   ![Snímek obrazovky s vygenerováním nového souboru KeyPair a certifikátu](media/howto-opc-vault-secure/generate-new-key-pair.png "Vygenerovat nový pár klíčů")

4. Do formuláře zadejte předmět a názvy domén. Pro privátní klíč vyberte PEM nebo PFX s heslem. Vyberte vytvořit **novou souboru KeyPair** a vytvořte žádost o certifikát.

   ![Snímek obrazovky zobrazující obrazovku podrobností žádosti o certifikát a tlačítko generovat nové souboru KeyPair](media/howto-opc-vault-secure/approve-reject.png "Schválit certifikát")

5. Schválení vyžaduje, aby uživatel s rolí schvalovatele a s oprávněním pro podepisování v Azure Key Vault. V typickém pracovním postupu by měl být schvalovatel a role žadatele přiřazeni různým uživatelům. Vyberte **schválit** nebo **odmítnout** , pokud chcete spustit nebo zrušit aktuální vytvoření páru klíčů a operaci podepisování. Nový pár klíčů se vytvoří a bezpečně uloží do Azure Key Vault, dokud ho nestáhnou žadatel o certifikát. Výsledný certifikát s veřejným klíčem je podepsaný certifikační autoritou. Dokončení těchto operací může trvat několik sekund.

   ![Snímek obrazovky s podrobnostmi o zobrazení žádosti o certifikát se zprávou o schválení ve spodní části](media/howto-opc-vault-secure/view-key-pair.png "Zobrazit pár klíčů")

7. Výsledný privátní klíč (PFX nebo PEM) a certifikát (DER) si můžete stáhnout odsud ve formátu vybraném jako stažení binárního souboru. K dispozici je také verze kódovaná ve formátu base64, například pro zkopírování a vložení certifikátu do příkazového řádku nebo zadání textu. 
8. Po stažení a bezpečném uložení privátního klíče můžete vybrat **Odstranit privátní klíč**. Certifikát s veřejným klíčem zůstane k dispozici pro budoucí použití.
9. Vzhledem k použití certifikátu podepsaného certifikační autoritou se tady musí stáhnout certifikát certifikační autority a seznam odvolaných certifikátů (CRL).

Nyní závisí na zařízení OPC UA, jak použít nový pár klíčů. Certifikát certifikační autority a seznam CRL se většinou zkopírují do `trusted` složky, zatímco veřejné a privátní klíče certifikátu aplikace se aplikují na `own` složku v úložišti certifikátů. Některá zařízení už můžou podporovat nabízení serveru pro aktualizace certifikátů. Přečtěte si dokumentaci zařízení OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Vyžádání nového certifikátu pomocí CSR 

1. Přejít na **aplikace**.
3. Vyberte **nový požadavek** na uvedenou aplikaci.

   ![Snímek obrazovky žádosti o nový certifikát](media/howto-opc-vault-secure/request-new-certificate.png "Požádat o nový certifikát")

3. Pokud chcete požádat o nový podepsaný certifikát pro vaši aplikaci, vyberte **požádat o nový certifikát s žádostí o podepsání** .

   ![Snímek obrazovky s vygenerováním nového certifikátu](media/howto-opc-vault-secure/generate-new-certificate.png "Vygenerovat nový certifikát")

4. Odešlete CSR tak, že vyberete místní soubor nebo ve formuláři vložíte do formuláře zástupce s kódováním base64. Vyberte možnost **generovat nový certifikát**.

   ![Snímek obrazovky s podrobnostmi o zobrazení žádosti o certifikát](media/howto-opc-vault-secure/approve-reject-csr.png "Schválit CSR")

5. Schválení vyžaduje, aby uživatel s rolí schvalovatele a s oprávněním pro podepisování v Azure Key Vault. Vyberte **schválit** nebo **odmítnout** , pokud chcete spustit nebo zrušit skutečnou operaci podepisování. Výsledný certifikát s veřejným klíčem je podepsaný certifikační autoritou. Dokončení této operace může trvat několik sekund.

   ![Snímek obrazovky, který zobrazuje podrobnosti žádosti o certifikát a obsahuje zprávu o schválení ve spodní části.](media/howto-opc-vault-secure/view-cert-csr.png "Zobrazit certifikát")

6. Výsledný certifikát (DER) je možné stáhnout odsud jako binární soubor. K dispozici je také verze kódovaná ve formátu base64, například pro zkopírování a vložení certifikátu do příkazového řádku nebo zadání textu. 
10. Po stažení a bezpečném uložení certifikátu můžete vybrat **Odstranit certifikát**.
11. Vzhledem k použití certifikátu podepsaného certifikační autority by se tady měl také stáhnout certifikát certifikační autority a seznam CRL.

Nyní závisí na zařízení OPC UA, jak použít nový certifikát. Certifikát certifikační autority a seznam CRL se většinou zkopírují do `trusted` složky, zatímco se certifikát aplikace používá pro `own` složku v úložišti certifikátů. Některá zařízení už můžou podporovat nabízení serveru pro aktualizace certifikátů. Přečtěte si dokumentaci zařízení OPC UA.

### <a name="step-3-device-secured"></a>Krok 3: zabezpečené zařízení

Zařízení OPC UA je teď připravené ke komunikaci s jinými zařízeními OPC UA zabezpečenými certifikáty podepsanými certifikační autoritou bez další konfigurace.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak zabezpečit zařízení OPC UA, můžete:

> [!div class="nextstepaction"]
> [Spuštění zabezpečené služby správy certifikátů](howto-opc-vault-secure-ca.md)
