V tomto příkladu, můžu se ukazují, jak používat **SharePoint Online – když je vytvořena nová položka** trigger pro zahájení pracovní postup aplikace logiky, když je vytvořena nová položka v seznamu Sharepointu Online.

> [!NOTE]
> Výzva pro přihlášení ke svému účtu služby SharePoint, pokud jste ještě nevytvořili *připojení* k Sharepointu Online.  
> 
> 

1. Zadejte *sharepoint* do vyhledávacího pole v návrháři pro logic apps zvolte **SharePoint Online – když je vytvořena nová položka** aktivační události.  
   ![SharePoint online spouštěcí image ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. **Když je vytvořena nová položka** se zobrazí ovládací prvek.  
   ![SharePoint online spouštěcí image 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Vyberte **URL webu**. Toto je online webu služby SharePoint, kterou chcete sledovat pro nové položky pro aktivaci pracovního postupu.  
   ![SharePoint online spouštěcí image 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Vyberte **název seznamu**. Toto je seznam na Sharepointový web, který chcete monitorovat pro nové položky, které aktivují pracovní postup.  
   ![SharePoint online spouštěcí image 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

V tomto okamžiku byla nakonfigurována vaše aplikace logiky s triggerem, který se začne spustit z jiné triggery a akce v pracovním postupu. To proběhne pokaždé, když je vytvořena nová položka v seznamu Sharepointu Online, které jste vybrali.  

