---
title: Konfigurace nastavení diagnostiky trezoru ve velkém měřítku
description: Nakonfigurujte nastavení diagnostiky Log Analytics pro všechny trezory v daném oboru pomocí Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 55461937381f7551c42714c835d4755ab65f175b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92171540"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurace nastavení diagnostiky trezoru ve velkém měřítku

Řešení pro vytváření sestav, které poskytuje Azure Backup, využívá Log Analytics (LA). Aby bylo možné odesílat data z daného trezoru do systému LA, je nutné pro tento trezor vytvořit [nastavení diagnostiky](./backup-azure-diagnostic-events.md) .

Často ruční přidání nastavení diagnostiky na trezor může být náročný úkol. Kromě toho musí mít všechny vytvořené nové trezory taky povolené nastavení diagnostiky, aby bylo možné zobrazit sestavy pro tento trezor.

Pro zjednodušení vytváření nastavení diagnostiky se škálováním (s LA jako cílovou) Azure Backup poskytuje integrovaný [Azure Policy](../governance/policy/index.yml). Tato zásada přidá nastavení diagnostiky LA do všech trezorů v daném předplatném nebo skupině prostředků. Následující části obsahují pokyny k použití této zásady.

## <a name="supported-scenarios"></a>Podporované scénáře

* Zásady je možné použít v jednom okamžiku pro všechny trezory Recovery Services v rámci konkrétního předplatného (nebo do skupiny prostředků v rámci předplatného). Uživatel, kterému se přiřadí zásada, musí mít přístup **vlastníka** k předplatnému, ke kterému je zásada přiřazená.

* Pracovní prostor LA zadaný uživatelem (do kterého budou odesílána diagnostická data) může být v jiném předplatném než trezory, ke kterým je zásada přiřazena. Uživatel musí mít přístup pro **čtenáře**, **přispěvatele** nebo **vlastníka** k předplatnému, ve kterém zadaný pracovní prostor La existuje.

* Obor skupiny pro správu se momentálně nepodporuje.

* Integrovaná zásada není v současnosti k dispozici v národních cloudech.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Přiřazení předdefinovaných zásad k oboru

Chcete-li přiřadit zásadu pro trezory v požadovaném oboru, postupujte podle následujících kroků:

1. Přihlaste se k Azure Portal a přejděte na řídicí panel **zásad** .
2. V nabídce vlevo vyberte **definice** a získejte seznam všech předdefinovaných zásad napříč prostředky Azure.
3. Vyfiltrujte seznam pro **kategorii = zálohování**. Vyhledejte zásadu s názvem **[Preview]: nasazení nastavení diagnostiky pro Recovery Services trezor pro Log Analytics pracovní prostor pro kategorie specifické pro prostředky**.

    ![Podokno definice zásad](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Vyberte název zásady. Budete přesměrováni na podrobnou definici této zásady.

    ![Podrobná definice zásad](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. V horní části podokna vyberte tlačítko **přiřadit** . Tím vás přesměruje do podokna **zásady přiřazení** .

6. V části **základy** vyberte tři tečky vedle pole **obor** . Otevře se pravé podokno kontextu, kde můžete vybrat předplatné, na které se má zásada použít. Volitelně můžete také vybrat skupinu prostředků, aby se zásady používaly jenom pro trezory v určité skupině prostředků.

    ![Základy přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. V části **parametry** zadejte následující informace:

    * **Název profilu** – název, který se přiřadí k nastavení diagnostiky, které vytvořila zásada.
    * **Log Analytics Workspace** – pracovní prostor Log Analytics, ke kterému má být přidruženo nastavení diagnostiky. Diagnostická data všech trezorů v oboru přiřazení zásad budou vložena do zadaného pracovního prostoru LA.

    * **Název značky vyloučení (volitelné) a hodnota značky vyloučení (volitelné)** – můžete zvolit, že se mají vyloučit trezory obsahující určitý název a hodnotu značky z přiřazení zásad. Například pokud **nechcete,** aby nastavení diagnostiky bylo přidáno do těch trezorů, které mají značku '. test ' nastaven na hodnotu ' yes ', je nutné v poli **název značky vyloučení** zadat '. ' a ' yes ' v poli **hodnota značky vyloučení** . Pokud jsou některá (nebo obě) z těchto dvou polí prázdná, zásada se použije na všechny příslušné trezory bez ohledu na to, jaké značky obsahují.

    ![Parametry přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Vytvoření úlohy nápravy** – Jakmile se zásada přiřadí oboru, všechny nové trezory vytvořené v tomto oboru automaticky získají nastavená nastavení diagnostiky La (do 30 minut od doby vytvoření trezoru). Pokud chcete přidat nastavení diagnostiky do existujících trezorů v oboru, můžete aktivovat úlohu nápravy při přiřazení zásady. Chcete-li spustit úlohu nápravy, zaškrtněte políčko **vytvořit úlohu nápravy**.

    ![Náprava přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Přejděte na kartu **Revize + vytvořit** a vyberte **vytvořit**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Za jakých podmínek se úloha nápravy vztahuje na trezor?

Úloha nápravy se aplikuje na trezory, které nejsou kompatibilní podle definice zásady. Trezor není kompatibilní, pokud splňuje jednu z následujících podmínek:

* Pro trezor není k dispozici žádné nastavení diagnostiky.
* Pro trezor jsou k dispozici nastavení diagnostiky, ale žádná z těchto nastavení v přepínači nepovoluje **všechny** události specifické pro prostředky s možností La jako cíl a vybrané **prostředky** .

Takže i když má uživatel trezor s povolenou událostí AzureBackupReport v režimu AzureDiagnostics (který jsou podporované sestavami zálohování), úloha nápravy se u tohoto trezoru bude vztahovat i v [případě, že](./backup-azure-diagnostic-events.md#legacy-event)je režim specifický pro prostředky doporučený způsob vytváření nastavení diagnostiky.

Pokud má uživatel navíc trezor, který má povolenou pouze podmnožinu šesti událostí specifických pro prostředky, bude úloha nápravy platit pro tento trezor, protože sestavy zálohování budou fungovat podle očekávání pouze v případě, že jsou povoleny všechny 6 událostí specifických pro daný prostředek.

> [!NOTE]
>
> Pokud má Trezor existující nastavení diagnostiky s povolenou **podmnožinou kategorií konkrétního prostředku** , která je nakonfigurovaná tak, aby odesílala data do konkrétního pracovního prostoru La, řekněme: "pracovní prostor x", a pokud je cílový pracovní prostor La v přiřazení zásad **stejný** jako pracovní prostor x, úloha nápravy selže (pro samotný trezor).
>
>Důvodem je, že pokud se události, které jsou povolené dvěma různými nastaveními diagnostiky u stejného prostředku, **překrývají** v některém formuláři, nemůžou mít nastavení stejný pracovní prostor La jako cíl. Tuto chybu budete muset ručně vyřešit tak, že přejdete na příslušný trezor a nakonfigurujete nastavení diagnostiky s jiným pracovním prostorem LA jako cíl.
>
> Všimněte si, že úloha nápravy **selže,** pokud stávající nastavení diagnostiky bude jako cíl povoleno pouze AzureBackupReport s pracovním prostorem X, protože v tomto případě se nebude překrývat mezi událostmi povolenými existujícím nastavením a událostmi, které jsou povoleny nastavením vytvořeným úlohou nápravy.

## <a name="next-steps"></a>Další kroky

* [Naučte se používat sestavy zálohování.](./configure-reports.md)
* [Další informace o Azure Policy](../governance/policy/index.yml)
* [K automatickému povolení zálohování pro všechny virtuální počítače v oboru udělte použít Azure Policy.](./backup-azure-auto-enable-backup.md)
