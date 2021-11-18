# angular tips and trick

## Visual Studio Code Tool You Must Have for Angular Development
https://aka.ms/ab-angular<br>
https://youtu.be/-g2Dat7z2WQ

# Angular Material Tips and Tricks
### Angular Material Table Sort Not Working
Tried all the tricks, still not able to figure out why sort is not working?

Try this out:
1) Make sure "matColumnDef" name is same as element name.
  
  ```
    <ng-container matColumnDef="title">
         <th mat-header-cell *matHeaderCellDef mat-sort-header> Title </th>
         <td mat-cell *matCellDef="let element"> {{element.title}} </td>
     </ng-container>
  
  ```
  
2) If you're fetching data from backend server, make sure, sort is initalized after data is received from subscription.
  ```
   getAllTitles() {
        this.uiService.loadingStateChanged.next(true);
        this.vendorTypeService.getAllTitles().subscribe(res => {
          this.dataSource = new MatTableDataSource(res)
          this.dataSource.sort = this.sort;
        });
      this.uiService.loadingStateChanged.next(false);
    }
  ```
  
  ### 
  # PUT, DELETE not working from angular application but working from Postman
  
  By default http return type is JSON, if backend is retruning some success message then you must specifiy response type as string.
  
  ```
    putUser(data: IUser, id: string) {
    return this.httpClient.put(this.apiURL + "/api/Users/" + id, data, {responseType: 'text'});
  }

  ```
  
  
  
  
