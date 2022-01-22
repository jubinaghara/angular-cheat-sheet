# angular tips and trick


## Visual Studio Code Tool You Must Have for Angular Development
https://aka.ms/ab-angular<br>
https://youtu.be/-g2Dat7z2WQ


## Angular Material Table Sort Not Working
Tried all the tricks, still not able to figure out why sort is not working?

Try this out:
Try-1) Make sure "matColumnDef" name is same as element name.
  
  ```
    <ng-container matColumnDef="title">
         <th mat-header-cell *matHeaderCellDef mat-sort-header> Title </th>
         <td mat-cell *matCellDef="let element"> {{element.title}} </td>
     </ng-container>
  
  ```
  
Try-2) If you're fetching data from backend server, make sure, sort is initalized after data is received from subscription.
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
  ## PUT, DELETE not working from angular application but working from Postman?
  
  By default, the http return type is JSON, if the backend is returning some success message in string format then you must specify the response type as a string.
  ```
    putUser(data: IUser, id: string) {
    return this.httpClient.put(this.apiURL + "/api/Users/" + id, data, {responseType: 'text'});
  }

  ```
  ## File upload and download
  ### Upload Component
  ```
  export class FileUploadComponent {

  public progress: number = 0;
  public uploaded: boolean = false;
  fileName: string = ""

  @Output() public onUploadFinished = new EventEmitter();
  imgURL: any;

  constructor(
    private fileUploadService: FileUploadService,
    private notificationService: NotificationService) { }


  public uploadFile = (files: any) => {
    if (files.length === 0) {
      return;
    }
    let fileToUpload = <File>files[0];
    const formData = new FormData();

    formData.append('file', fileToUpload, fileToUpload.name);
    this.fileName = fileToUpload.name;
   
    formData.append('file', fileToUpload, fileToUpload.name);
    this.fileUploadService.post(formData)
      .subscribe(event => {
   
        if (event.type === HttpEventType.UploadProgress) {
          this.progress = Math.round(100 * event.loaded / event.total!);
          console.log("progress: ", this.progress);
        }
        else if (event.type === HttpEventType.Response) {
          this.notificationService.showSuccess(`File "${this.fileName}" uploaded successfully.`)
          this.uploaded = true;
          let file = {
            fileName: this.fileName,
            filePath: event.body
          }
          this.onUploadFinished.emit(file);
        }
      });
  }
}

  
  ```
  
  
  ###  Download Component
  ```
 export class FileDownloadComponent implements OnInit {

  @Input() filename: string = "";
  public progress: number = 0;
  public uploaded: boolean = false;
  @Input() fileUrl: string = ""

  @Output() public onDownloadFinished = new EventEmitter();

  constructor(
    private fileUploadService: FileUploadService,
    private notificationService: NotificationService,
  ) { }

  ngOnInit(): void {
  }

  download(action: string) {
    console.log("download fn::")
    this.fileUploadService.download(this.fileUrl).subscribe((event: any) => {
      if (event.type === HttpEventType.UploadProgress)
        this.progress = Math.round((100 * event.loaded) / event.total);
      else if (event.type === HttpEventType.Response) {
        if (action === "download") {
          this.notificationService.showSuccess("File downalod success...")
          this.downloadFile(event);
        }
        if (action === "view") {
          this.viewFileIn(event);
        }
      }
    });
  }

  downloadFile(data: HttpResponse<Blob>) {
    console.log("blob", data, data.body?.type);
    // const downloadedFile = new Blob([data.body!], { type: data.body?.type });
    const downloadedFile = new Blob([data.body!], { type: 'application/pdf' });
    const a = document.createElement('a');
    a.setAttribute('style', 'display:none;');
    document.body.appendChild(a);
    a.download = this.fileUrl;
    a.href = URL.createObjectURL(downloadedFile);
    a.href = window.URL.createObjectURL(downloadedFile);
    a.target = '_blank';
    a.click();
    document.body.removeChild(a);
  }

  viewFileIn(data: HttpResponse<Blob>) {
    console.log("blob", data, data.body?.type);
    const downloadedFile = new Blob([data.body!], { type:  data.body?.type });
    let fileURL = window.URL.createObjectURL(downloadedFile);
    window.open(fileURL, '_blank');
  }


}
  ```
  
  ###  Service
  ```
export class FileUploadService {

  apiURL = environment.apiUrl

  constructor(private httpClient: HttpClient) { }

  post(data: any) {
    return this.httpClient.post(this.apiURL + "/api/FileUpload", data, { reportProgress: true, observe: 'events' })
  }

  download(path: string) {
    return this.httpClient.get(`${this.apiURL}/api/FileDownload?filePath=${path}`, {
      reportProgress: true,
      responseType: 'blob',
      observe: 'events'
    })
  }
  ```
  
  
  
