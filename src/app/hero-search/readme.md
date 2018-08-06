### RxJS 的 Subject
angular的html标签的处理函数可以用`|`符号来添加管道：
```html
<li *ngFor="let Hero of heroes$ | async">
    <a routerLink="/detail/{{hero.id}}">{{hero.name}}</a>
</li>
```
ngFor使用了一个`heroes$ | async`的观察者数据源`async pipe` 可将Observable返回的数据带回到html。再来看Component内的代码：
```typescript
heroes$: Observable<Hero[]>;
private searchTerms = new Subject<string>();

constructor(private heroService: HeroService) { }

ngOnInit() {
    this.heroes$ = this.searchTerms.pipe(
        debounceTime(300),
        distinctUntilChanged(),
        switchMap((term: string) => this.heroService.searchHeroes(term))
    );
}

search(term: string): void {
    this.searchTerms.next(term);
}
```
searchTerms是一个Subject，即一个Observable的实现类，可以通过`next(value?: T): void`来发送数据到它的订阅者。在该component被加载的时候通过给searchTerms加pipe将其转化为Observable<Hero[]>赋值给heroes$。中间用debounce函数来延迟响应时间，用`distincrUtilChanged()`来触发搜索词改变时的下一次搜索。用switchMap来实现Observable的转变处理。