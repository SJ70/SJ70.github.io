# Avoid_Rects

화면을 배회하는 사각형들을 피하여 생존하는 게임

<br/>

## Index

> - [2023.01.02.](#20230102)
> - [2023.01.03.](#20230103)
> - [2023.01.06.](#20230106)
> - [2023.01.09.](#20230109)
> - [2023.01.10.](#20230110)
> - [2023.01.11.](#20230111)
> - [2023.01.15.](#20230115)

<br/>

### 기획

> HTML5 Canvas에 대하여 공부하고 싶어 유튜브 튜토리얼을 찾던 중 다수의 원이 화면을 배회하는 프로그램 강좌 영상을 찾았다.  
> 이를 통해 공부하던 중 이를 응용하여 게임을 만들 수 있겠다는 생각을 하였고 바로 실행에 옮겼다.

## 2023.01.02.

> 미적 개선를 위해 장애물의 도형을 원이 아닌 회전하는 사각형을 사용하였다.
>
> **문제**
>
> > 각 사각형을 회전 시 모든 도형과 캔버스가 동시에 회전하였다.
>
> **해결**
>
> > HTML5 캔버스의 개념에 대한 미숙함에 의해 발생한 문제였다.  
> > 각 도형들을 그릴 때마다 각 도형의 중심으로 캔버스 중심의 위치를 변경한 후 도형을 그리고 캔버스 중심의 위치를 복구한다.
> >
> > ```
> > ctx.save();
> > ctx.strokeStyle = 'hsla('+this.color+',100%,80%,'+this.opacity+'%)';
> > ctx.translate(this.x, this.y);
> > ctx.rotate(this.angle);
> > ctx.strokeRect(0 - this.current_size/2, 0 - this.current_size/2, this.current_size, this.current_size);
> > ctx.restore();
> > ```
>
> 시간이 지날수록 난이도를 높이기 위해 일정한 시간마다 도형을 추가한다.
>
> 도형의 갯수를 점수의 기준으로 삼는다.
>
> 해당 점수는 화면 중앙에 표기된다.
>
> 마우스의 위치에 따라 점수의 위치가 동적으로 변하게 하여 입체감을 표현하였다.

## 2023.01.03.

> **문제**
>
> > 프로그램에서 측정하는 마우스 위치와 실제 마우스 위치의 딜레이 차이가 있었다.  
> > 게다가 마우스가 화면 밖을 나갔다 들어오는 등 반칙적인 조작이 가능하였다.
>
> **해결**
>
> > 마우스를 따라오는 플레이어 오브젝트를 생성하였다.  
> > 충돌을 마우스의 좌표가 아닌 플레이어 오브젝트의 좌표로 확인을 한다.  
> > 마우스가 화면 밖을 나갔다 들어와 마우스의 좌표가 순간이동하더라도 플레이어 오브젝트는 이전 마우스 좌표부터 현재 마우스 좌표까지 이동하게 된다.
> >
> > **문제**
> >
> > > 플레이어 오브젝트가 마우스를 따라올 때 단순히 x값과 y값으로 연산하게 되면 대각 이동 속도가 빨라지게 된다.
> >
> > **해결**
> >
> > > 피타고라스의 공식을 응용하여 해결하였다.
> > >
> > > ```
> > > move(){
> > >   let dx = this.x_dest - this.x;
> > >   let dy = this.y_dest - this.y;
> > >   let d = Number(Math.sqrt( dx*dx + dy*dy ));
> > >
> > >   if(d < this.speed){
> > >       this.x = this.x_dest;
> > >       this.y = this.y_dest;
> > >   }
> > >   else{
> > >       this.x += (dx>0?1:-1) * this.speed * Math.sqrt(d*d - dy*dy);
> > >       this.y += (dy>0?1:-1) * this.speed * Math.sqrt(d*d - dx*dx);
> > >   }
> > > }
> > > ```
>
> 플레이어 오브젝트의 생성 모션을 제작하던 중, 이를 게임 시작 및 종료 애니메이션으로 응용하면 좋겠다고 생각하였다.
>
> 게임이 실행될 시 플레이어 오브젝트의 크기가 화면을 가득 채울만큼 커진다.  
> 게임이 시작되면 정해진 플레이어 오브젝트의 크기만큼 축소된다.
>
> 또한 게임 종료 시 플레이어 오브젝트가 확대되며 화면을 가득 채운다.
>
> **문제**
>
> > 해당 게임은 브라우저에서 실행되므로 창의 크기가 가변적이다. 이는 게임 플레이 중 큰 문제를 유발시킬 수 있다.
>
> **해결**
>
> > 화면의 크기가 변할 시 게임이 끝난 판정을 넣었다.  
> > 동시에 오브젝트의 크기 값들을 현재 화면 크기에 맞게 초기화한다.
>
> **문제**
>
> > 화면의 가장자리에서 생존이 극히 유리해진다.
>
> **해결**
>
> > 화면의 모서리를 순환하는 사각형을 추가하였다.

## 2023.01.06.

> avoid_rects의 score class를 Score.js로 모듈화하였다. [[자세히]](./essential.md#20230106) [[왜?]](./shoot_balls.md#20230106)  
> 이에 따라 이전에는 한 파일 내에서 작성하여 메소드에 무심코 사용하였던 전역변수들을 인수를 호출하는 방식으로 변경하였다.

## 2023.01.09.

> 플레이어 오브젝트를 Dot.js로 모듈화하였다. [[왜?]](./shoot_balls.md#20230109)
>
> 기존의 Rect와 RectEdge 또한 각각 RectInsider.js와 RectOutsider.js로 모듈화하였다.  
> RectInsider.js와 RectOutsider.js가 겹치게 되는 메소드는 Rect.js라는 class에 작성하였고 이를 상속받도록 하였다.
>
> **문제**
>
> > RectInsider.js와 RectOutsider.js의 생성자를 호출 시 오류가 발생하였다.
>
> **해결**
>
> > 부모 클래스의 생성자를 호출하지 않아 발생한 문제였다. 상속을 자주 사용하지 않아 인지하지 못 하였다.

## 2023.01.10.

> 게임 오브젝트 크기의 기준을 기존의 화면의 대각선에서 화면의 넓이로 변경하였다. [[연쇄]](./shoot_balls.md#20230110)
>
> **문제**
>
> > Dot.js가 화면 전환 역할 뿐만 아니라 Player.js와 함께 마우스 포인터의 역할을 함께 수행하고 있다는 것을 깨달았다.  
> > 각각 1개의 역할을 한 것이 아닌 2개가 모여야 2개의 역할을 할 수 있었던 것이다.
>
> **해결**
>
> > 화면 전환기의 기능만을 하는 CircleEffector.js를 작성하였다. [[자세히]](./essential.md#20230110)  
> > 마우스를 따라가는 오브젝트의 기능을 하는 MouseFollower.js를 작성하였다. [[자세히]](./essential.md#20230110)  
> > 이러한 변경에 따라 avoid_rects.js의 내용 또한 수정되었다. [[연쇄]](./shoot_balls.md#20230110)

## 2023.01.11.

> avoid_rects.js를 메소드화하였다. [[왜?]](./select_game.md#20230111)
> 게임 선택 화면으로 돌아가는 버튼을 추가하였다. [[자세히]](./essential.md/#20230111)  

## 2023.01.15.

> essential 클래스들의 생성자 및 인수호출 방식의 변화에 따라 일부 수정되었다. [[자세히]](./essential.md#20230115)  
> Rects와 RectInsider, RectOutsider 또한 같은 방식으로 수정되었다.
>
> **문제**
> 
> > 기존에는 사각형들의 테두리만 그렸다. 도형이 많아지게 되면 해당 사각형들의 경계를 파악하기 어렵게 된다는 단점이 있다는 것을 파악하였다.  
>
> **해결**
>
> > 사각형들의 내부 또한 해당 사각형의 투명도의 10%만큼 채우도록 하였다.