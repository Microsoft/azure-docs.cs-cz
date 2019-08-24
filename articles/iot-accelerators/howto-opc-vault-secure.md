---
title: Zabezpečení komunikace zařízení OPC UA pomocí trezoru OPC – Azure | Microsoft Docs
description: Postup registrace aplikací OPC UA a postup při vydávání podepsaných certifikátů aplikace pro zařízení OPC UA s úložištěm OPC
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996021"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>Jak používat službu správy certifikátů trezoru OPC

Tento článek vysvětluje, jak registrovat aplikace a jak vydávat podepsané aplikace certifikáty pro vaše zařízení OPC UA.

## <a name="prerequisites"></a>Požadavky

### <a name="deploy-the-certificate-management-service"></a>Nasazení služby správy certifikátů

Nejdřív je potřeba službu nasadit do cloudu Azure.
Přečtěte si článek popisující, jak [nasadit službu správy certifikátů trezoru OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Vytvoření certifikátu certifikační autority vystavitele

Pokud jste to ještě neudělali, vytvořte certifikát certifikační autority vystavitele.

Vyhledejte článek popisující, jak [vytvořit a spravovat certifikát vystavitele pro OPC trezor](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Zabezpečení aplikací OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Krok 1: Registrace aplikace OPC UA 

> [!IMPORTANT]
> K registraci aplikace je nutná role zapisovače.

1. Otevřete službu Certificate Service na `https://myResourceGroup-app.azurewebsites.net` adrese a přihlaste se.
2. Přejděte na `Register New` stránku.
1. Pro registraci aplikace musí mít uživatel alespoň přiřazenou roli zapisovače.
2. Formulář pro zadávání názvů řídí konvence pojmenování v OPC UA World. Například na obrázku pod nastavením pro ukázku [referenčního serveru OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) v OPC UA. Zobrazuje se zásobník NetStandard:

   ![Registrace referenčního serveru UA](media/howto-opc-vault-secure/reference-server-registration.png "Registrace referenčního serveru UA")

5. Kliknutím na `Register` toto tlačítko zaregistrujete aplikaci v databázi aplikace služby Certificate Service. Pracovní postup přímo provede uživatele k dalšímu kroku pro vyžádání podepsaného certifikátu pro aplikaci.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Krok 2: Zabezpečení aplikace pomocí certifikátu aplikace podepsaného certifikační autoritou

Aplikaci OPC UA lze zabezpečit vyvoláním podepsaného certifikátu založeného na žádosti o podepsání certifikátu (CSR) nebo žádostí o novou dvojici klíčů, která zahrnuje také nový privátní klíč ve formátu PFX nebo PEM. Dodržujte dokumentaci zařízení OPC UA, na které je metoda podporovaná pro vaši aplikaci. Obecně platí, že je doporučována metoda CSR, protože nevyžaduje přenos privátního klíče přes kabel.

- Pokud vaše zařízení vyžaduje vydání nové dvojice klíčů, postupujte prosím podle [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Pokud zařízení podporuje vystavení žádosti o podepsání certifikátu (CSR), sledujte prosím [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Krok 3a: Požádat o nový certifikát s novým souboru KeyPair

1. Přejděte na `Applications` stránku.
3. Spusťte pracovní postup žádosti o certifikát výběrem `New Request` možnosti pro uvedenou aplikaci.

   ![Požádat o nový certifikát](media/howto-opc-vault-secure/request-new-certificate.png "Požádat o nový certifikát")

3. Stisknutím tlačítka "požádat o nové souboru KeyPair a certifikát" si vyžádejte soukromý klíč a nový podepsaný certifikát s veřejným klíčem pro vaši aplikaci.

   ![Vygenerovat nový pár klíčů](media/howto-opc-vault-secure/generate-new-key-pair.png "Vygenerovat nový pár klíčů")

4. Do formuláře zadejte předmět, názvy domén a jako soukromý klíč vyberte PEM nebo PFX s heslem. Kliknutím na `Generate New Certificate` tlačítko vytvořte žádost o certifikát.

   ![Schválit certifikát](media/howto-opc-vault-secure/approve-reject.png "Schválit certifikát")

5. Krok schvalování vyžaduje, aby uživatel s rolí schvalovatel a s oprávněními pro podepisování v Azure Key Vault. V obvyklém pracovním postupu by měl být schvalovatel a role žadatele přiřazeni různým uživatelům.
6. Schvalte nebo odmítněte žádost o certifikát pro spuštění nebo zrušení skutečného vytvoření páru klíčů a operace podepisování. Nový pár klíčů se vytvoří a bezpečně uloží do Azure Key Vault, dokud ho nestáhnou žadatel o certifikát. Výsledný certifikát s veřejným klíčem je podepsaný certifikační autoritou. Dokončení těchto operací může trvat několik sekund.

   ![Zobrazit pár klíčů](media/howto-opc-vault-secure/view-key-pair.png "Zobrazit pár klíčů")

7. Výsledný privátní klíč (PFX nebo PEM) a certifikát (DER) si můžete stáhnout odsud ve formátu vybraném jako stažení binárního souboru. K dispozici je také verze kódovaná ve formátu base64, například pro zkopírování certifikátu do příkazového řádku nebo textového zadání. 
8. Jakmile je privátní klíč stažený a bezpečně uložený, dá se odstranit ze služby pomocí `Delete Private Key` tlačítka. Certifikát s veřejným klíčem zůstane k dispozici pro budoucí použití.
9. Vzhledem k použití certifikátu podepsaného certifikační autority by se tady měl také stáhnout certifikát certifikační autority a seznam CRL.
10. Nyní závisí na zařízení OPC UA, jak použít nový pár klíčů. Certifikát certifikační autority a seznam CRL se většinou zkopírují `trusted` do složky, zatímco veřejný a privátní klíč certifikátu aplikace se aplikuje `own` na složku v úložišti certifikátů. Některá zařízení už můžou pro aktualizace certifikátů podporovat nabízení serveru. Přečtěte si dokumentaci zařízení OPC UA.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Krok 3B: Vyžádání nového certifikátu pomocí CSR 

1. Přejděte na `Applications` stránku.
3. Spusťte pracovní postup žádosti o certifikát výběrem `New Request` možnosti pro uvedenou aplikaci.

   ![Požádat o nový certifikát](media/howto-opc-vault-secure/request-new-certificate.png "Požádat o nový certifikát")

3. Pokud chcete požádat o nový podepsaný certifikát pro vaši aplikaci, stiskněte tlačítko požádat o nový certifikát s žádostí o podepsání.

   ![Vygenerovat nový certifikát](media/howto-opc-vault-secure/generate-new-certificate.png "Vygenerovat nový certifikát")

4. Odešlete CSR tak, že vyberete místní soubor nebo ve formuláři vložíte do formuláře zástupce s kódováním base64. Kliknutím na `Generate New Certificate` tlačítko vytvořte žádost o certifikát.

   ![SCHVÁLIT CSR](media/howto-opc-vault-secure/approve-reject-csr.png "SCHVÁLIT CSR")

5. Krok schvalování vyžaduje, aby uživatel s rolí schvalovatel a s oprávněními pro podepisování v Azure Key Vault. Schvalte nebo odmítněte žádost o certifikát pro spuštění nebo zrušení skutečné operace podepisování. Výsledný certifikát s veřejným klíčem je podepsaný certifikační autoritou. Dokončení této operace může trvat několik sekund.

   ![Zobrazit certifikát](media/howto-opc-vault-secure/view-cert-csr.png "Zobrazit certifikát")

6. Výsledný certifikát (DER) je možné stáhnout odsud jako binární soubor. K dispozici je také verze kódovaná ve formátu base64, například pro zkopírování certifikátu do příkazového řádku nebo textového zadání. 
10. Jakmile je certifikát stažený a bezpečně uložený, dá se odstranit ze služby pomocí `Delete Certificate` tlačítka.
11. Vzhledem k použití certifikátu podepsaného certifikační autority by se tady měl také stáhnout certifikát certifikační autority a seznam CRL.
12. Nyní závisí na zařízení OPC UA, jak použít nový certifikát. Certifikát certifikační autority a seznam CRL se většinou zkopírují `trusted` do složky, zatímco se certifikát aplikace používá `own` pro složku v úložišti certifikátů. Některá zařízení už můžou pro aktualizace certifikátů podporovat nabízení serveru. Přečtěte si dokumentaci zařízení OPC UA.

### <a name="step-4-device-secured"></a>Krok 4: Zabezpečené zařízení

Zařízení OPC UA je teď připravené ke komunikaci s jinými zařízeními OPC UA zabezpečenými certifikáty podepsanými certifikační autoritou bez další konfigurace. Užijte si ji!


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak zabezpečit zařízení OPC UA, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Spuštění zabezpečené služby správy certifikátů](howto-opc-vault-secure-ca.md)