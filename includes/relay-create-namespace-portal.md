1. Přihlaste se na web [Azure Portal][Azure portal].
2. V nabídce vlevo vyberte **+ Vytvořit prostředek**. Pak vyberte **Integrace** > **Přenos**. Pokud se v seznamu **Přenos** nezobrazuje, vyberte v pravém horním rohu **Zobrazit vše**. 
3. V části **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
4. V poli **Předplatné** vyberte předplatné Azure, ve kterém chcete obor názvů vytvořit.
5. V poli [Skupina prostředků](../articles/azure-resource-manager/resource-group-portal.md) vyberte existující skupinu prostředků, do které se obor názvů umístí, nebo vytvořte novou.  
6. V části **Umístění** vyberte zemi nebo oblast, ve které by se měl váš oboru názvů hostovat.
   
    ![Vytvoření oboru názvů][create-namespace]
7. Vyberte **Vytvořit**. Systém vytvoří obor názvů a povolí ho. Po několika minutách systém zřídí prostředky pro váš účet.

### <a name="get-management-credentials"></a>Získání přihlašovacích údajů pro správu

1. Vyberte **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V části Obor názvů Relay vyberte **Zásady sdíleného přístupu**.  
3. V části **Zásady sdíleného přístupu** vyberte **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. V části **Zásady: RootManageSharedAccessKey** vyberte tlačítko **Kopírovat** vedle možnosti **Připojovací řetězec – primární klíč**. Tím se připojovací řetězec zkopíruje do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
   
    ![connection-string][connection-string]

5. Zopakujte předchozí krok, zkopírujte si hodnotu **primárního klíče** a vložte ji do dočasného umístění pro pozdější použití.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
