---
title: 'Rychlý Start: nastavení ověření identity Azure pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak nastavit a nakonfigurovat poskytovatele ověření identity pomocí Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a8ab7195958a6c2d5a730c38232eb348d505deda
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593599"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Rychlý Start: nastavení ověření identity Azure pomocí Azure Portal

V tomto rychlém startu můžete začít s ověřením Azure. Naučte se spravovat poskytovatele ověření identity, podepsané zásady a zásady pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="attestation-provider"></a>Zprostředkovatel ověření identity

V této části vytvoříte poskytovatele ověření identity a nakonfigurujete ho buď pomocí nepodepsaných zásad, nebo u podepsaných zásad. Naučíte se také, jak zobrazit a odstranit poskytovatele ověření identity.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Vytvoření a konfigurace zprostředkovatele pomocí nepodepsaných zásad

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **ověření identity**.
1. V seznamu výsledků vyberte **Microsoft Azure ověření identity**.
1. Na stránce **Microsoft Azure ověření identity** vyberte **vytvořit**.
1. Na stránce **vytvořit poskytovatele ověření identity** zadejte následující vstupy:

   - **Předplatné:** Zvolte předplatné.
   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vyberte **vytvořit novou** a zadejte název skupiny prostředků.
   - **Název**: Zadejte jedinečný název.
   - **Umístění**: vyberte umístění.
   - **Soubor certifikátů podepsaných zásadou**: Nenahrávat soubor certifikátů podepsané zásadou ke konfiguraci poskytovatele s nepodepsanými zásadami.

1. Po zadání požadovaných vstupů vyberte **zkontrolovat + vytvořit**.
1. Pokud dojde k problémům s ověřováním, opravte je a pak vyberte **vytvořit**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Vytvoření a konfigurace zprostředkovatele s podepsanými zásadami

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **ověření identity**.
1. V seznamu výsledků vyberte **Microsoft Azure ověření identity**.
1. Na stránce **Microsoft Azure ověření identity** vyberte **vytvořit**.
1. Na stránce **vytvořit poskytovatele ověření identity** zadejte následující informace:

   - **Předplatné:** Zvolte předplatné.
   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vyberte **vytvořit novou** a zadejte název skupiny prostředků.
   - **Název**: Zadejte jedinečný název.
   - **Umístění**: vyberte umístění.
   - **Soubor certifikátů Signer Policy**: Nahrajte soubor certifikátů Signer zásad a nakonfigurujte poskytovatele ověření identity s podepsanými zásadami. [Podívejte se na příklady certifikátů podepisování zásad](./policy-signer-examples.md).

1. Po zadání požadovaných vstupů vyberte **zkontrolovat + vytvořit**.
1. Pokud dojde k problémům s ověřováním, opravte je a pak vyberte **vytvořit**.

### <a name="view-the-attestation-provider"></a>Zobrazit poskytovatele ověření identity

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1. Do pole Filtr zadejte název poskytovatele ověření identity a vyberte jej.

### <a name="delete-the-attestation-provider"></a>Odstranit poskytovatele ověření identity

Existují dva způsoby, jak odstranit poskytovatele ověření identity. Další možnosti:

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1. Do pole Filtr zadejte název poskytovatele ověření identity.
1. Zaškrtněte políčko a vyberte **Odstranit**.
1. Zadejte **Ano** a vyberte **Odstranit**.

Nebo můžete:

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1. Do pole Filtr zadejte název poskytovatele ověření identity.
1. Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1. V řádku nabídek vyberte **Odstranit** a vyberte **Ano**.

## <a name="attestation-policy-signers"></a>Podepsané zásady ověření identity

Podle kroků v této části můžete zobrazit, přidat a odstranit certifikáty podepisování zásad.

### <a name="view-the-policy-signer-certificates"></a>Zobrazit certifikáty podepisujících zásad

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1. Do pole Filtr zadejte název poskytovatele ověření identity.
1. Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1. V nabídce prostředků na levé straně okna nebo v dolním podokně vyberte **certifikáty Signing Policy** .
1. Vyberte **Stáhnout certifikáty podepisování zásad**. Tlačítko bude zakázáno pro poskytovatele ověřování identity vytvořené bez požadavku na podepsání zásad.
1. Stažený textový soubor bude mít všechny certifikáty ve formátu JWS.
1. Ověřte počet certifikátů a stažené certifikáty.

### <a name="add-the-policy-signer-certificate"></a>Přidat certifikát podepsané zásady

1.  Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1.  Do pole Filtr zadejte název poskytovatele ověření identity.
1.  Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1.  V nabídce prostředků na levé straně okna nebo v dolním podokně vyberte **certifikáty Signing Policy** .
1.  V horní nabídce vyberte **Přidat** . Tlačítko bude zakázáno pro poskytovatele ověřování identity vytvořené bez požadavku na podepsání zásad.
1.  Nahrajte soubor certifikátu Signer zásad a vyberte **Přidat**. [Podívejte se na příklady certifikátů podepisování zásad](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Odstranění certifikátů podepisování zásad

1.  Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1.  Do pole Filtr zadejte název poskytovatele ověření identity.
1.  Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1.  V nabídce prostředků na levé straně okna nebo v dolním podokně vyberte **certifikáty Signing Policy** .
1.  V horní nabídce vyberte **Odstranit** . Tlačítko bude zakázáno pro poskytovatele ověřování identity vytvořené bez požadavku na podepsání zásad.
1.  Nahrajte soubor certifikátu podepsané zásady a vyberte **Odstranit**. [Podívejte se na příklady certifikátů podepisování zásad](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Zásady ověřování identity

Tato část popisuje, jak zobrazit zásady ověření identity a jak nakonfigurovat zásady, které byly vytvořené pomocí a bez požadavku na podepsání zásad.

### <a name="view-an-attestation-policy"></a>Zobrazit zásady ověření identity

1.  Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1.  Do pole Filtr zadejte název poskytovatele ověření identity.
1.  Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1.  V nabídce prostředky na levé straně okna nebo v dolním podokně vyberte **zásady** .
1.  Vyberte preferovaný **typ ověření identity** a zobrazte **aktuální zásady**.

### <a name="configure-an-attestation-policy"></a>Konfigurace zásad ověření identity

Postupujte podle těchto kroků a nahrajte zásadu v JWT nebo textovém formátu, pokud se poskytovatel ověření identity vytvořil bez požadavku na podepsání zásad.

1. Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1. Do pole Filtr zadejte název poskytovatele ověření identity.
1. Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1. V nabídce prostředky na levé straně okna nebo v dolním podokně vyberte **zásady** .
1. V horní nabídce vyberte **Konfigurovat** .
1. Vyberte možnost **Formát** jako **JWT** nebo jako **text**.

   Pokud zprostředkovatel ověření identity byl vytvořen bez požadavku na podepsání zásad, může uživatel odeslat zásadu v typu **JWT** nebo v **textovém** formátu.

      - Pokud jste zvolili formát JWT, nahrajte soubor zásad pomocí obsahu zásady ve formátu **nepodepsaného/podepsaného JWT** a vyberte **Uložit**. [Viz Příklady zásad](./policy-examples.md).
      - Pokud jste zvolili textový formát, nahrajte soubor zásad s obsahem v **textovém** formátu nebo zadejte obsah zásady do textové oblasti a vyberte **Uložit**. [Viz Příklady zásad](./policy-examples.md).

   Pro možnost nahrání souboru se ve verzi Preview zobrazuje text a nedá se upravovat.

1. V horní nabídce vyberte **aktualizovat** , aby se zobrazila nakonfigurovaná zásada.

Pokud se poskytovatel ověření identity vytvořil s požadavkem na podepsání zásady, nahrajte pomocí těchto kroků ve formátu JWT zásady.

1.  Přejděte do nabídky Azure Portal nebo na domovské stránce a vyberte **všechny prostředky**.
1.  Do pole Filtr zadejte název poskytovatele ověření identity.
1.  Vyberte poskytovatele ověření identity a přejít na stránku Přehled.
1.  V nabídce prostředky na levé straně okna nebo v dolním podokně vyberte **zásady** .
1.  V horní nabídce vyberte **Konfigurovat** .
1.  Nahrajte soubor zásad v **podepsaném formátu JWT** a vyberte **Uložit**. [Viz Příklady zásad](./policy-examples.md).

    Pokud se poskytovatel ověření identity vytvořil s požadavkem na podepsání zásad, může uživatel odeslat zásady jenom ve **formátu podepsaného JWT**.

    Pro možnost nahrání souboru se ve verzi Preview zobrazuje text a nedá se upravovat.
    
1.  Kliknutím na **aktualizovat** zobrazíte nakonfigurovanou zásadu.

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Ověření SGX enklávy pomocí ukázek kódu](/samples/browse/?expanded=azure&terms=attestation)

