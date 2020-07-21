---
title: Konfigurace nastavení diagnostiky trezoru ve velkém měřítku
description: Nakonfigurujte nastavení diagnostiky Log Analytics pro všechny trezory v daném oboru pomocí Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 2400be15dcd46084e9a605076c00cf5c5ac92463
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498045"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurace nastavení diagnostiky trezoru ve velkém měřítku

Řešení pro vytváření sestav, které poskytuje Azure Backup, využívá Log Analytics (LA). Aby bylo možné odesílat data z daného trezoru do systému LA, je nutné pro tento trezor vytvořit [nastavení diagnostiky](./backup-azure-diagnostic-events.md) .

Často ruční přidání nastavení diagnostiky na trezor může být náročný úkol. Kromě toho musí mít všechny vytvořené nové trezory taky povolené nastavení diagnostiky, aby bylo možné zobrazit sestavy pro tento trezor.

Pro zjednodušení vytváření nastavení diagnostiky se škálováním (s LA jako cílovou) Azure Backup poskytuje integrovaný [Azure Policy](../governance/policy/index.yml). Tato zásada přidá nastavení diagnostiky LA do všech trezorů v daném předplatném nebo skupině prostředků. Následující části obsahují pokyny k použití této zásady.

## <a name="supported-scenarios"></a>Podporované scénáře

* Zásady je možné použít v jednom okamžiku pro všechny trezory Recovery Services v rámci konkrétního předplatného (nebo do skupiny prostředků v rámci předplatného). Uživatel, který přiřazuje zásadu, musí mít oprávnění Owner (Vlastník) k předplatnému, ke kterému je zásada přiřazená.

* Pracovní prostor LA zadaný uživatelem (ke kterému se budou posílat diagnostická data) může být v jiném předplatném než trezory, ke kterým je zásada přiřazená. Uživatel musí mít přístup čtenářů, přispěvatel nebo Owner k předplatnému, ve kterém zadaný pracovní prostor LA existuje.

* Obor skupiny pro správu se momentálně nepodporuje.

* Integrovaná zásada není v současnosti k dispozici v národních cloudech.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Přiřazení předdefinovaných zásad k oboru

Chcete-li přiřadit zásadu pro trezory v požadovaném oboru, postupujte podle následujících kroků:

1. Přihlaste se k Azure Portal a přejděte na řídicí panel **zásad** .
2. V nabídce vlevo vyberte **definice** a získejte seznam všech předdefinovaných zásad napříč prostředky Azure.
3. Vyfiltruje seznam pro **kategorii = monitorování**. Vyhledejte zásadu s názvem **[Preview]: nasazení nastavení diagnostiky pro Recovery Services trezor pro Log Analytics pracovní prostor pro kategorie specifické pro prostředky**.

    ![Okno Definice zásad](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Klikněte na název zásady. Budete přesměrováni na podrobnou definici této zásady.

    ![Podrobná definice zásad](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Klikněte na tlačítko **přiřadit** v horní části okna. Tím vás přesměruje na okno **přiřadit zásadu** .

6. V části **základy**klikněte na tři tečky vedle pole **obor** . Otevře se okno správného kontextu, kde můžete vybrat předplatné, na které se má zásada použít. Volitelně můžete také vybrat skupinu prostředků, aby se zásady používaly jenom pro trezory v určité skupině prostředků.

    ![Základy přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. V části **parametry**zadejte následující informace:

    * **Název profilu** – název, který se přiřadí k nastavení diagnostiky, které vytvořila zásada.
    * **Log Analytics Workspace** – pracovní prostor Log Analytics, ke kterému má být přidruženo nastavení diagnostiky. Diagnostická data všech trezorů v oboru přiřazení zásad budou vložena do zadaného pracovního prostoru LA.

    * **Název značky vyloučení (volitelné) a hodnota značky vyloučení (volitelné)** – můžete zvolit, že se mají vyloučit trezory obsahující určitý název a hodnotu značky z přiřazení zásad. Například pokud **nechcete,** aby nastavení diagnostiky bylo přidáno do těch trezorů, které mají značku '. test ' nastaven na hodnotu ' yes ', je nutné v poli **název značky vyloučení** zadat '. ' a ' yes ' v poli **hodnota značky vyloučení** . Pokud jsou některá (nebo obě) z těchto dvou polí prázdná, zásada se použije na všechny příslušné trezory bez ohledu na značky, které obsahují.

    ![Parametry přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Vytvoření úlohy nápravy** – Jakmile se zásada přiřadí oboru, všechny nové trezory vytvořené v tomto oboru automaticky získají nastavená nastavení diagnostiky La (do 30 minut od doby vytvoření trezoru). Pokud chcete přidat nastavení diagnostiky do existujících trezorů v oboru, můžete aktivovat úlohu nápravy při přiřazení zásady. Chcete-li spustit úlohu nápravy, zaškrtněte políčko **vytvořit úlohu nápravy**.

    ![Náprava přiřazení zásad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Přejděte na kartu **Revize + vytvořit** a klikněte na **vytvořit**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Za jakých podmínek se úloha nápravy vztahuje na trezor?

Úloha nápravy se aplikuje na trezory, které nejsou kompatibilní podle definice zásady. Trezor není kompatibilní, pokud splňuje jednu z následujících podmínek:

* Pro trezor není k dispozici žádné nastavení diagnostiky.
* Pro trezor jsou k dispozici nastavení diagnostiky, ale žádná z těchto nastavení v přepínači nepovoluje **všechny** události specifické pro prostředky s možností La jako cíl a vybrané **prostředky** .

Takže i když má uživatel trezor s povolenou událostí AzureBackupReport v režimu AzureDiagnostics (který jsou podporované sestavami zálohování), úloha nápravy se u tohoto trezoru bude vztahovat i v případě, že konkrétní režim prostředků je doporučeným způsobem, jak vytvořit nastavení diagnostiky, a to až [dál](./backup-azure-diagnostic-events.md#legacy-event).

Pokud má uživatel navíc trezor s povolenou pouze podmnožinou všech šesti událostí specifických pro prostředky, bude úloha nápravy platit pro tento trezor, protože sestavy zálohování budou fungovat podle očekávání pouze v případě, že jsou povoleny všechny 6 událostí konkrétního prostředku.

> [!NOTE]
>
> Pokud má Trezor existující nastavení diagnostiky s povolenou **podmnožinou kategorií konkrétního prostředku** , která je nakonfigurovaná tak, aby odesílala data do konkrétního pracovního prostoru La, řekněme: "pracovní prostor x", a pokud je cílový pracovní prostor La v přiřazení zásad **stejný** jako pracovní prostor x, úloha nápravy selže (pro samotný trezor).
>
>Důvodem je, že pokud se události, které jsou povolené dvěma různými nastaveními diagnostiky u stejného prostředku, **překrývají** v některém formuláři, nemůže mít nastavení stejný pracovní prostor La jako cíl. Tuto chybu budete muset ručně vyřešit tak, že přejdete do příslušného trezoru a nakonfigurujete nastavení diagnostiky s jiným pracovním prostorem LA jako cíl.
>
> Všimněte si, že úloha nápravy **selže,** pokud stávající nastavení diagnostiky bude jako cíl povoleno pouze AzureBackupReport s pracovním prostorem X, protože v tomto případě se nebude překrývat mezi událostmi povolenými existujícím nastavením a událostmi, které jsou povoleny nastavením vytvořeným úlohou nápravy.

## <a name="next-steps"></a>Další kroky

* [Naučte se používat sestavy zálohování.](./configure-reports.md)
* [Další informace o Azure Policy](../governance/policy/index.yml)
* [K automatickému povolení zálohování pro všechny virtuální počítače v oboru udělte použít Azure Policy.](./backup-azure-auto-enable-backup.md)
