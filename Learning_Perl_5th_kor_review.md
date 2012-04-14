# 거침없이 배우는 펄(Learning Perl 5th 한글번역판) 비평

이 문서는 Learning Perl 5th( http://oreilly.com/catalog/9780596520113 )의 한글 번역판인 "거침없이 배우는 펄"( http://www.yes24.com/24/goods/4433208 )을 보충하고 이해를 도우며 약간은 최근의 경향과 맞지 않는 부분에 대한 가이드를 위한 목적으로 만들었습니다. (번역판은 원서를 충실하게 번역하였을 뿐이므로 여기서 말하는 건 번역서의 문제가 아니라 원문내용에 대한 비평인 셈이 됨)

## 2장 - 스칼라 데이터

### p27 - 문자열
문자열 내에 \n 같은 특수한 의미를 지니는 역슬래시 회피 문자나 변수같은 보간(Interpolation)되어야 하는 것이 없으면 " "대신에 ' '을 쓰는게 좋습니다. 그렇게 버릇을 들여 놓으면 문자열을 묶는 ", ' 만 보고도 이 문자열 내에는 보간되어야할 변수나 회피문자가 있는지 없는지 바로 알 수 있게 됩니다.

### p46 - undef
undef는 값이기도 하지만 어떤 변수를 초기화하는 함수이기도 합니다. 실제로 undef는 Perl에 정의된 함수( 참고:http://perldoc.perl.org/functions/undef.html )입니다.  p48을 보면 **$madonna = undef;** 로 스칼라값을 정의되지 않은 값으로 초기화 하고 있습니다. 이것은 undef라는 함수를 아무런 인자 없이 호출했을때 정의되지 않은 값을 리턴해주므로 그것을 $madonna에 넣어 변수를 정의되지 않은 값으로 초기화 시켜주는 겁니다. undef의 함수정의는 앞의 링크에서도 보다시피 **undef EXPR**입니다. EXPR은 표현식을 나타내며 EXPR 부분에는 나중에 배울 배열이나 해시가 와서 스칼라뿐만 아니라 배열이나 해시등의 초기화에도 사용합니다.

```perl
undef $scalar;  # 스칼라 초기화
undef @array;  # 배열 초기화, @array = () 로도 가능
undef %hash;  # 해시 초기화, %hash = () 로도 가능
undef $array[2]   # 배열의 특정 요소값 초기화
undef $hask{key}  # 해시의 특정 key에 대한 값(value) 초기화
```

제가 $madonna = undef; 를 추천하지 않는 이유는 저런 형태를 보고 배열이나 해시를 초기화 할때도 

```perl
@array = undef;
%hash = undef;
```

같은 말도 안되는 실수를 하기 때문이죠. 좌변이 스칼라를 의미하는 변수형태이면 문제없지만 배열이나 해시에 대해 저렇게 하면 의도와는 달리 @array는 undef값이 하나 들어간 배열 (undef)가 되며 %hash는 ( "" => undef ) 가 됩니다.  따라서 스칼라,배열,해시등의 변수를 초기화할때는 **undef 변수;** 형태로 하시는게 좋습니다.

##3장 목록과 배열
### p53 - 목록상수 
"목록 상수는 쉼표로 구분한 값의 목록을 괄호로 둘러싼 것입니다." 라고 되어있지만 괄호를 둘러싸는 것은 사실 필수가 아닙니다. 일례로
 `@a = (1..100);` 는 `@a = 1..100;` 으로 괄호를 둘러싸지 않아도 됩니다. 이유는 연산자 우선 순위에 의해서 ..가 =보다 우선 순위가 높기 때문에 1..100에 의해 먼저 목록(list)가 생성된 다음 배열변수 @a에 들어가기 때문이죠. 하지만 `@a = (1,2,3);` 은 ,가 =보다 연산자 우선 순위가 낮기 때문에 꼭 괄호를 둘러싸야 합니다. 그러지 않고 `@a = 1,2,3;` 이라고 하면 @a배열에는 1 하나만 들어가고 나머지는 void context라는걸로 그냥 무시됩니다. 따라서 "쉼표로 구분한 값의 목록"이 목록(list)이며 연산자 우선 순위에 문제가 있을 경우 괄호로 감싸준다고 생각하시면 됩니다.

다른 예로 문자열을 찍는 print 함수의 프로토타입은 print LIST 입니다. LIST는 목록을 말하죠. print문은 다음처럼

```perl
print 'hello world', "\n";
```

로 LIST인 'hello world', "\n" 를 괄호로 싸지 않아도 줄바꿈 문자열 까지 다 잘 출력합니다. 이것은 print 함수가 리스트를 구성하는 ,연산자 보다 우선순위가 낮기 때문에 굳이 LIST를 괄호로 묶지 않아도 print함수에 넘어갈 LIST가 먼저 구성되기 때문입니다. 그러면 다음과 같이 

```perl
print (1+2),4;
```

라고 하면 어떻게 될까요? 여기서는 1+2를 묶는 괄호를 print는 자신이 찍어야할 LIST를 묶는 괄호라고 생각하기 때문에 1+2의 결과인 3만 출력하고 뒤의 4는 무시해버립니다. 이럴 때는 LIST를 먼저 구성해주기 위해서는 

```perl
print( (1+2),4 );
```

라고 하면 3과4를 다 출력하게 됩니다. 함수 호출시 괄호에 대한 규칙은 나중에 다시 언급할 기회가 있을겁니다.

### p54 - qw 단축표현
전 개인적으로 qw( some text ) 라는 표현보다 기본적으로 qw/some text/ 라는 형태의 표현을 선호합니다. 책에도 설명이 나오지만 qw 같은 Quote like Operators( 참고: http://perldoc.perl.org/perlop.html#Quote-and-Quote-like-Operators 와 http://perltraining.com.au/tips/2005-04-22.html )를 둘러싸는 구분자는 다양하게 사용가능합니다. 하지만 초심자들이 qw( some text ); 형태로 쓰면 괄호를 보고 qw같은 연산자가 마치 인자를 받는 함수인냥 착각하는 경우를 많이 봤기 때문에 기본적으로 qw/some text/ 형태를 선호합니다. 뭐 알고 쓰면 문제없겠지만요.

### p55 - 목록할당
배열에는 배열슬라이스 문법이라는 것이 있습니다. 배열 @a에 많은 요소들이 있다고 할 때 첫번째,두번째,세번째 요소를 ($a[0], $a[1],$a[2])라고 하면 이것을 @a[0,1,2] 로 나타낼 수 있습니다. 이것은 [ ]안의 0,1,2가 연속적이므로  또 줄여서 @a[0..2] 로 나타낼 수도 있습니다.
그러면 

```perl
($betty[0], $betty[1]) = ($betty[1], $betty[0]);
```

은 간단하게

```perl
@betty[0,1] = @betty[1,0];
```

으로

```perl
($rocks[0], $rocks[1], $rocks[2], $rocks[3]) = qw/talc mica feldspar quartz/;
```

는  

```perl
@rocks[0..3] = qw/talc mica feldspar quartz/;
```

로 줄여쓸 수 있습니다. 

그리고 어떤 배열이나 리스트에서 첫요소는 스칼라변수에 다른 나머지는 어떤 배열변수에 담아 쓰고 싶으면 `($var,@array) = @data;` 처럼 하면 됩니다.

배열에 대해서 더 깊이 알고 싶으면 http://perl101.org/arrays.html 와 http://gypark.pe.kr/wiki/Perl/%EB%B0%B0%EC%97%B4 를 참고하세요.

## 4장 - 사용자 함수

### p71 - 함수 호출시 &
함수 호출시 &를 붙이는건 요즘은 특별한 경우를 제외하고는 잘 쓰이지 않는 오래된 관행입니다. 책에서는 혹시나 Perl에서 이미 제공하는 함수명과 같은 함수를 만들었을때 &를 붙이지 않으면 원래 Perl에 있는 함수가 호출될 수 있기 때문에 새로만든 함수를 호출하기 위해 &를 쓰라고 하지만. 그건 이미 사용자가 Perl 내부 함수들을 알고 알아서 중복되지 않도록 사용하는게 바른 방법입니다.

Perl에서 함수 호출시에는 다음과 같은 규약을 따르세요.

* Perl에서 기본으로 제공하는 함수(일명 Core함수)를 호출할때는 우선순위에 문제가 없다면 굳이 괄호를 쓰지 않는다.(가독성이 더 높음)
* Core함수가 아닌 함수는 무조건 괄호를 써서 호출한다.(Core 함수와 자연히 구별됨)  &my_func(1,2,3) -> my_func(1,2,3)
* 함수호출시 쓰는 괄호는 if,while뒤의 조건문이 들어가는 괄호와 구별하기 위해 조건문 뒤의 괄호는 항상 띄워서 쓰고 함수의 인자를 묶는 괄호는 항상 붙여 쓴다.

```perl
#☞ 우선순위에 문제가 있거나 혼돈이 있을 경우 경우 괄호를 사용
 print (1+2),4;    # 3만 찍힘 괄호를 인자들(LIST)을 묶는 괄호로 인식
 print( (1+2),4 ); # 3 4 찍힘
 
 print join '-', @a,"\n";   # @a,"\n" 이 하나의 리스트로 join함수에 전달
 print join('-', @a),"\n";  # @a를 join후 "\n"을 더한 리스트가 print에 전달
```

```perl
#Before
open( my $fh, "<", "file.txt" ) || "Can't open $!";
while(<$fh>) {
    my @words = split( ' ', $_);
    print( join( '-', @word), "\n" );
}
close($fh);

#After
open my $fh, '<', 'file.txt' or "Can't open $!";
while (<$fh>) {
    my @words = split ' ',$_;
    print join( '-', @word), "\n";
}
close $fh;
```

함수호출 규약과 &사용에 관련한 글은 https://www.socialtext.net/perl5/index.cgi?subroutines_called_with_the_ampersand 을 참고하세요

### p80 코드에서

```perl
sub max {
    my ($max_so_far) = shift @_;
```

라고 되어 있는데 p82의 마지막에 보면 설명되어 있지만 ( )로 묶으면 목록문맥(list context) 아니면 스칼라문맥(scalar context)가 됩니다. 위에서는 shift로 서브루틴에 넘어온 인자들이 담겨있는 `@_` 배열에서 값하나(스칼라 값)을 꺼내서 넣기 때문에 `my ($max_so_far) = shift @_;` 라고 하는 것 보다 shift로 스칼라값 하나를 꺼내서 할당하는게 확실하므로 다음과 같이 써주는게 좋습니다.

```perl
sub max {
    my $max_so_far = shift;
```

그리고 보통 `@_`배열에서 인자를 shift로 하나씩 꺼내 쓸때는 shift만 써도 기본적으로 `@_`배열에서 꺼내므로 굳이 `@_`를 명시적으로 쓰지 않는 것이 관행입니다. 위는 다음과 같이 써도 됩니다.

```perl
sub max {
    my ($max_so_far) = @_;
```

이건 `my  ($max_so_far)` 가 list context가 되기 때문에 `@_`배열의 첫 요소가 $max_so_far에 들어오게 됩니다. Perl코딩규약을 다루는 Perl Best Practices( http://oreilly.com/catalog/9780596001735 )라는 책에는 인자를 담고 있는 배열 `@_`에서 인자를 하나씩 꺼내면서 경우에 따라 값에 대한 채크를 해야 할 경우에만 shift를 사용하라고 추천합니다. ( 다음 처럼 어떤 인자를 받아서 채크하고 undefined면 어떤 기본값을 줄 때 )

```perl
sub my_func {
    my $var1 = shift;
    my $var2 = shift || 'some text' ;   # 없으면 'some text'
```
혹은 OOP 프로그래밍시 첫인자로 오는 객체 자신을 제외한 나머지 인자를 가지고 다른 메소드를 호출할때도 shift가 유용

```perl
sub delegated_method
{
    my $self = shift;
    say 'Calling delegated_method()'

    $self->delegate->delegated_method( @_ );
}
```


보통 하나의 인자만 받는 경우 `my $var = shift;` 형식을 종종 쓰기도 하지만 (실제로도 shift는 함수호출 스택을 수정하기 때문에 속도면에서도 느리다고 함 참고: http://makepp.sourceforge.net/2.0/perl_performance.html#functions ) 저 같은 경우 위 같이 인자를 하나씩 꺼내면서 어떤 채크가 필요 하지 않는 경우 인자의 목록이 한눈에 보이게 일관적으로 `my (...) = @_` 형식을 사용합니다.

```perl
sub my_func {
    my ($var) = @_;   # 하나의 인자를 받음
}

sub my_func2 {
   my ($var1, $var2) = @_;  # 두개의 인자를 받음
}
```

### p82 - 렉시컬(my) 변수에 대한 노트
코드에 `my ($square) = $_ * $_;` 도 굳이 괄호를 넣지 않고 `my $square = $_ * $_;` 라고 해주는게 더 적절하겠죠.


my같은 사설변수( lexical variable ), use strict 프래그마 같은 것에 대한 보충 설명은 "최신 스타일 Perl로 개과천선하기"( https://github.com/aero/perl_docs/blob/master/New_Style_Perl-Korean_Perl_Workshop_2008.md ) 를 읽어보세요.
**my, our, local 같이 변수의 영역과 생명주기를 결정짓는 키워드는 차이점이 뭔지 확실하게 이해해야 합니다.**

**현대 Perl에서는 기본적으로** 

```perl
#!/usr/bin/env perl
use strict;
use warnings;
```

**처럼 시작하는게 거의 표준이니 꼭 기억하고 넣도록 합시다. 이렇게 strict 프래그마를 쓰면 변수는 my,our,local 키워드중 하나로 명시적으로 선언하지 않으면 에러가 납니다. 이것을 넣어 자신의 코드에 에러가 난다면 당신은 옛날 Perl코드를 쓰고 있는 것입니다.  그렇다면 절대 이것을 빼서 문제를 해결하려 하지 말고, 자신의 코드를 현대 Perl에 맞게 고치시기 바랍니다.**  

### p86 - return에 관해
*"어떤 프로그래머들은 반환 값이 있을 경우 항상 return을 사용해서 그 값이 반환 값이 라고 마치 설명을 달듯이 명시하는 것을 좋아합니다. .... 하지만 많을 펄 프로그래머들은 return은 일곱 문자를 더 입력하는 것일 뿐이라고 믿고 있습니다."* 라는 말이 있는데 Perl 함수에서는 마지막으로 평가된 값이 자동으로 리턴값으로 사용되긴 하지만 이말은  무시하시고 함수에서 어떤 값을 리턴해야되면 명시적으로 return 문을 써서 하는게 좋습니다. Perl에서 함수의 처음과 끝을 감싸는 {  } 블럭과 마찬가지로 펄에서 쓰이는 여타 블럭들도 독립적인 렉시컬영역(lexical scope)를 가집니다. 그리고 그 블럭들은 블럭이 끝날 때 마지막에 평가된 값을 넘깁니다.

```perl
sub my_func {
      my ($var) = @_;
      return $var * $var; # 그냥 $var * $var 라고 해도 되지만 명시적으로 return 해주자!
}
my $str = do { local $/; <$fh> };  # $fh 파일핸들로 연 파일내용 모두가 넘어감
my @new = map { $_ =~ s/A/a/g; $_ } @old;  # @old배열에 담긴 스트링들에서 모든 대문자 A를 소문자로 바꾼 문자열이 차례로 넘어감
```

일단 do 블럭과 map 함수를 잘 모른다 쳐도 해당 블럭들의 마지막 평가값 <$fh>, $_ 가 결과값으로 넘어간다는 사실을 앞의 설명을 통해 짐작가능할 겁니다. 마지막 두줄과 같이 함수형태가 아닌 simple한 블럭에서는 블럭의 마지막 평가값을 넘겨주는 테크닉을 유용하게 쓰지만 일반적인 함수에서는 함수 중간 부분등에서도 return 할 수 있으므로 항상 명시적으로 return해주는 것이 나중에라도 함수의 어디에서 빠져나가는지 등의 함수의 구조를 파악하는데 도움이 됩니다.

## 5장 - 입력과 출력

### p111 - 파일 핸들 열기

```perl
open CONFIG, "<dino";
open LOG, ">>logfile";

print LOG "some text\n";
```

같은 형식의 bareword(인용부호로 감싸지 않은 문자열 - 위에서 CONFIG,LOG)파일핸들과 2인자 형식의 open문은 옛날방식입니다. 현대적 스타일은 lexical 변수 파일핸들에 3인자 형식 open을 쓰는 것입니다. 이것은 4장 리뷰 마지막에 링크건 문서에 자세히 설명되어 있습니다. ( 추가참고: https://www.socialtext.net/perl5/index.cgi?bareword_uppercase_filehandles , https://www.socialtext.net/perl5/index.cgi?two_argument_open )

```perl
open my $CONFIG, '<', 'dino';
open my $LOG, '>>', 'logfile';

print {$LOG} "some text\n";  # open한 $LOG 파일핸들에 찍기
```

### 여기서 bareword 를 더 자세히 알아봅시다.
bareword ( https://www.socialtext.net/perl5/index.cgi?bareword )를 더 자세히 설명하자면 `"`나`'`등의 인용부호로 감싸지 않은 벌거벗은? 문자열입니다. 하지만 여기에 규칙이 있습니다. "알파벳대소문자,`_` 로 시작하며 그 뒤는 알파벳대소문자,`_`,숫자의 3가지의 조합을 사용할 수 있다"

```text
abc1      O
_a_b      O
_1ab      O
1abc      X
_a1_      O
```

그러면 bareword가 왜 중요하냐? 

펄에서 변수이름이나 함수이름 등에서 이름을 식별자(identifier)라고 합니다. 식별자는 컴파일러가 어떤 프로그램의 구성요소를 구별하는 이름표같은 것이며 예를 들면 스칼라변수 `$my_var`에서 my_var가 식별자가 됩니다. `sub my_func { ...}`같은 함수에서는 my_func가 식별자가 되지요 perl은 스칼라변수는 식별자 앞에 `$`, 배열은 `@`, 해시는 `%` 같은(함수는 내부적으로 &를 앞에 붙인것으로 인식) sigil을 붙여서 변수라고 정의 합니다. 따라서 sigil이 없는 다른 언어들과 달리 Perl은 같은 식별자를 가지는 다른 형태의 변수와 함수( `$abc, @abc, %abc, sub abc { ... }` )가  동시에 존재가능합니다.

이제 정리하면 변수나 함수명은 sigil+identifier(식별자) 라고 할 수 있겠죠.  그런데 재미있는건 identifier의 이름규칙이 bareword 이름규칙과 같다는 겁니다. 변수명은 bareword와 같은 "알파벳대소문자,`_` 로 시작하며 그 뒤는 알파벳대소문자,`_`,숫자의 3가지의 조합을 사용할 수 있다"

```perl
$ab2   # O
$1bc   # X
@_a_   # O
%A__   # O
```

여기에서 주의해야 할 것은 identifier의 최대 길이는 251자 라는 겁니다. 이것을 다르게 말하면 251자가 넘어가는 변수명이나 함수명을 사용할 수 없다는 것이 되겠죠. 그렇게 사용할 일도 없겠지만요.

그리고 또 하나 주의해야 되는 것은 identifier가 나중에 해시라는 걸 배울때 해시키에 bareword를 사용할 때 규칙에도 똑 같이 적용된다는 겁니다. 이것은 해시에서 다시 다루겠습니다.

### p 115 - die로 치명적인 오류 발생시키기

p116에

```perl
if ( ! open LOG, ">>logfile" ) {
    die "Cannot create logfile: $!";
}
```

이런식으로 파일 open시 에러처리하는 건 어디서 나온 해괴한 코드인지 모르겠군요. 저것을 현대적 스타일과 에러처리 방법으로 바꾸면

```perl
open my $LOG, '>>', 'logfile' or die "Cannot create logfile : $!";
```

형식으로 씁니다. or 연산자는 우선 순위가 가장 낮은 연산자이기 때문에 or 앞쪽의 open문이 실패하면 뒤의 die가 실행됩니다. Perl 5.10 이상에서는 아래처럼 use autodie; 라는 프래그마( http://search.cpan.org/perldoc?autodie )를 쓰면 따로 예외처리 die를 해주지 않아도 open시 실패하면 자동으로 die하면서 에러메시지를 뿌려줍니다.

```perl
use autodie;

open my $LOG, '>>', 'logfile';
```

### 번외편
파일핸들에 어떤 문자열을 쓸때 `print $fh "some text\n";` 라고 해도 되지만 Perl Best Practices에서는 파일핸들에 { }를 묶어 `print {$fh} "some text\n";` 형식으로 씁니다. 만약 여러 파일핸들이 어떤 @fh 라는 배열에 담겨있다고 했을때 첫번째 파일핸들인 $fh[0]에 쓰겠다고 `print $fh[0] "some text\n";`라고 하면 원하는 대로 동작하지 않는데 이럴때는 임시 스칼라변수에 $fh[0]을 담아서 써도 되지만 바로 `print {$fh[0]} "some text\n";` 라고 하면 제대로 동작합니다. 이것은 print 문이 마치 간접객체 호출형식(indirect object notation)처럼 동작하기 때문인데 간접객체 호출형식은 [함수명] [객체] [인자] 형식으로 쓰면 객체의 함수명을 호출하는 것으로 객체의 위치에 오는 것이 bareword나 순수한 스칼라변수가 아닐 경우 { }로 묶어줘야 객체로 인식합니다. 따라서 print,printf,say문 등에 렉시컬 파일핸들을 지정해줄 경우 항상 { }로 묶어주는 것이 시각적으로도 구별이 쉽고 다양한 경우에 대해 일관성을 가질 수 있으므로 좋습니다.

## 6장 - 해시
### p129
해시값을 다음처럼 `$family_name{"fred"} = "flintstone";` 설정할때 { } 안의 해시키를 "fred" 처럼 큰따옴표로 묶었는데 여기는 bareword가 오면 자동으로 문자열로 인식하므로 굳이 " "나 ' '같은 인용부호로 묶을 필요가 없이 `$family_name{fred} = 'flintstone';` 처럼 하면 됩니다. 단 키로 쓰이는 문자열에 공백이 나 다른 의미로 오해될 수 있는 특수문자등이 포함된 문자열 예를 들면  $a같은 변수형태의 문자열을 보간(interpolation)된 변수 값이 아니라 키를 문자 그대로 키로 쓰고자 하면 다음 처럼 해야되겠죠

```perl
$hash{'text with space'} = 1;
$hash{'$a'} = 2;
$hash{'@_@'} = 3;
$hash{'^_^'} = 4;
$hash{'2.0'} = 5;  # $hash{2.0} = 5; 라고 하면 해시키는 문자열이나 숫자 2.0이 문자열화 되어 2가 되어 해시키가 문자열 2가 됨
```

이런 경우는 드물기 때문에 보통의 경우 굳이 키문자열을 일일이 인용부호로 감싸주는 건 불필요한 일입니다.

여기서 잠깐!  5장에서 설명한 bareword의 규칙이 해시키에서 bareword를 사용할 때도 그대로 적용된다고 말했습니다. 따라서 hash 키를 bareword로 사용할때 그 규칙에 어긋나는 경우도 인용부호로 꼭 감싸줘야 합니다. 마찬가지로 bareword로 지정하는 해시키의 최대 길이도 251자로 제한됩니다.( 인용부호로 묶은 해시키 문자열의 길이에는 이런제한이 없습니다.) 

```perl
$hash{1ab} = 1;   # 에러
$hash{'1ab'} = 1;   # 숫자로 시작하므로
```

대부분의 경우 해시를 사용하며 해시키를 수동으로 지정할 때 이러한 제약사항을 만나는 경우는 드물기 때문에 해시키를 특수한 경우가 아니면 bareword형태로 쓰는 것이 관행입니다.

### p130
첫번째 코드의 첫줄

```perl
foreach $person (qw< barney fred >) {
...
```

은 루프에 쓰이는 임시변수로 그냥 $person을 쓰는데 이렇게 루프에 한시적으로 쓰이는 변수는 앞에서 배운 my변수를 쓰는게 정석입니다. ( qw연산자와 같이 쓰는 구분자도 제가 기본적으로 선호하는 /로 바꾸면 )

```perl
foreach my $person (qw/barney fred/) {
...
```

이렇게 하면 루프 이전에 $person이라는 변수가 정의 되었다고 해도 루프내에서는 이전에 정의된 것과 상관없이 독립적인 변수를 사용하게 되므로 서로 전혀 간섭이 없고 영향도 미치지 않게 됩니다. 이것이 Python이나 Ruby에는 없는 my로 정의되는 렉시컬변수의 축복이죠 :)

### p133 큰화살표
`%some_hash = ( "foo", 35, "bar", 12.4 );` 같은 해시정의를 `=>`연산자를 사용하여 `%some_hash = ( foo => 35, bar => 12.4);`로 바꿔 쓸 수 있습니다. 책에서는 foo나 bar같은 해시키에  `%some_hash = ( "foo" => 35, "bar" => 12.4);` 처럼 인용부호를 감싸놨지만 `=>`연산자는 왼쪽에 오는 bareword는 인용부호로 감싸지 않아도 자동으로 문자열화 하기 때문에 앞에서 { }안에 bareword 해시키를 쓰는 경우 처럼 예외적인 특수한 경우가 아니면 굳이 인용 부호로 감쌀 필요가 없습니다. 다시 말하면 `=>`는 자신의 왼쪽에 오는 bareword를 자동으로 문자열화 한다는 것 빼고는 ,와 동일한 기능을 한다고 보면 됩니다. 예를들면  `print 'a','b','c','d';`는 이상하게 보일지 모르지만 `print a=>b=>c=>'d';`로 써도 똑같이 동작한다는 거죠.

그리고 Perl에서 `-`가 bareword 앞에 오면 둘이 결합된 문자열이 됩니다. 그래서 `(-width => 200, -height => 300)` 같은 형식의 키를 사용 할 수 있습니다. 이런 형식의 키는 드물게 CGI모듈이나 Tk 모듈등에서 인자를 나름대로 규칙에 의해 채크하기 위해서 함수의 인자로 해시형태의 리스트를 넘겨줄 때 사용되기도 합니다.

그리고 bareword 앞에 `+`를 붙이면 해당 bareword를 문자열이 아닌 해당 이름의 함수로 인식하여 함수호출의 결과 값을 취합니다.

```perl
my $var1 = $hash{shift};   #  shift는 문자열 그대로의 키이름
my $var2 = $hash{+shift}; #  my $var2 = $hash{shift()}; 과 같은 의미로 shift 함수가 기본으로 @_배열에서 shift한값을 키로 사용
```

* + 단항연산자의 또 다른 용법에 대해서는 http://aero.sarang.net/blog/2008/01/perl-unary-operator.html 을 참고

### p136
each 예제코드 첫줄

```perl
while ( ($key, $value) = each %hash ) {
...
```

도 루프내에서 한정적인 my 변수를 써서

```perl
while ( my ($key, $value) = each %hash ) {
...
```

형식으로 쓰는게 정석입니다. my변수를 가르쳐줘 놓고 후속되는 코드들에 왜 이런 형식으로 안써놨는지 개인적으로 불만이군요. 이후에도 계속 그런식의 코드들이 나오는데 **루프내에 한정되어 쓰이는 변수에는 꼭 my를 쓰도록 합시다.** 뭐 제가 따로 말 안해도 앞에서 말했던 `use strict;` 프래그마를 쓰면 알아서 에러날테지만요~

## 15장 - 똑똑한 일치와 given ~ when

### p295
Perl 5.10 부터 도입된 smart match 연산자 ~~ 는 너무 경우의 수가 많아 복잡하고 경우에 따라 일관성을 가지고 동작하지 못하는 부분이 있어
차후 deprecate 시키든지 다시 깔끔하게 정리하려고 하는 것 같습니다. 따라서 그때까지는 쓰지 말기를 추천하더군요.

참고:
 http://blogs.perl.org/users/brian_d_foy/2011/07/rethinking-smart-matching.html

그리고 C의 switch 같은 기능의 given ~ when 구문은 given과 같이 사용되는 lexical $_ 변수와 위의 내부적으로 given ~ when 구문의 비교에 있어 기본으로 사용되는 smart match연산자
와 얽힌 부분에 문제가 있어 역시 향후 기능과 동작이 다시 완벽해지기 전까지는 역시 사용하지 말 것을 추천하고 있습니다.

참고:
http://www.effectiveperlprogramming.com/blog/1333
http://www.xray.mpe.mpg.de/mailing-lists/perl5-porters/2011-05/thrd9.html#00768

http://blogs.perl.org/users/leon_timmermans/2011/10/why-do-you-want-new-major-features-in-core.html 에 보면

    Smartmatching? I think everyone agrees it is broken.
    given/when is even worse as it's almost impossible to predict if it will use smartmatching or not.
    Lexical $_? Mostly a new source of bugs, and the _ prototype is merely a hack to work around lexical $_ issues.

라고 말하고 있음.

( Programming Perl 4판에서는 given ~ when은 아직 시험적 상태로 보지만 스마트매칭이 적용되지 않는 명시적인 숫자,문자열 매칭 조건은 향후 어떻게 변하든 문제 없으니 안심하고 사용해도 괜찮다고 함)

switch 문 비슷한 기능은 기존의 문법을 조합하여 http://www.perlmonks.org/?node=How%20do%20I%20create%20a%20switch%20or%20case%20statement%3F 와 같은 방법을 사용해서도 가능함.

## 번외편
### 해시 슬라이스
책의 뒷쪽에서 다루는  고급주제지만 미리 설명드리면 앞에서 배운 배열 슬라이스와 비슷한 해시 슬라이스라는 것이 있습니다.
`my %hash = ( a=>1, b=>2, c=>3, d=>4);`에서 키 a,c,d에 대한 값을 뽑아내려면 `my @values = ( $hash{a}, $hash{c}, $hash{d} );` 라고 할 수 있을 겁니다. 이것을 해시 슬라이스로 줄여쓰면 `my @values = @hash{'a','c','d'};` 라고 할 수 있습니다. 이건 `my @values = @hash{qw/a c d/};`라고 해도 되는 건 센스가 좀 있으시면 별 무리없이 이해되실듯..

해시에 대해 좀 더 자세히 알고 싶으시면 http://perl101.org/hashes.html 을 보세요

# 이 책에 없는 것
Learning Perl 5판은 좋은책이지만 Perl을 제대로 사용하려면 필요하고 이해해야하는 레퍼런스( http://perldoc.perl.org/perlreftut.html , http://perldoc.perl.org/perlref.html )와 복잡한 자료구조( http://perldoc.perl.org/perldsc.html ), 모듈의 원리, 객체지향(OOP) 등에 대해서 다루지 않습니다. 따라서 이 책을 다 봤다고 Perl을 다 공부했다고 생각하시면 안됩니다. 원래 이 책 다음으로 보도록 기획된 책이 Intermediated Perl ( http://oreilly.com/catalog/9780596102067 ) 이란 책입니다. 이 책은 약간 오래됐고( 개정판을 준비중이라는 소문이 있음) 번역서가 없으니 영문을 보셔야 하며, 개인적으로 Learning Perl 5판에서 다루지 않은 Perl 전반을 다루는 책으로 저는 무료로 공개된 Beginning Perl( http://www.perl.org/books/beginning-perl/ ) 과 최근에 나온 무료책 Modern Perl ( http://www.onyxneon.com/books/modern_perl/index.html )을 보시길 추천 드립니다. 그리고 효율적인 Perl프로그래밍을 고민하신다면 Effective Perl Programming ( http://www.amazon.com/gp/product/B003LL2TP4/ 저자블로그: http://www.effectiveperlprogramming.com/ ) 도 추천합니다. 결국에 모든 정답은 perldoc( http://perldoc.perl.org/ )문서에 있다는 것도 명심하시고~

일명 Camel Book으로 유명한 Programming Perl은 Perl의 거의 모든 부분을 커버하는 참고서이지만 현재 3판은 오래된 내용으로 2012년 2월 개정 4판( http://shop.oreilly.com/product/9780596004927.do ) 이 발매될 예정이니 새로운 버젼이 나오면 보시길 추천드립니다.

# 추가 참고 문서
## 영문
 * Learn Perl in about 2 hours 30 minutes http://qntm.org/files/perl/perl.html
 * Learning Perl http://learn.perl.org/
 * Perl Beginners' Site http://perl-begin.org/
 * Perl Elements to Avoid http://perl-begin.org/tutorials/bad-elements/
 * Perl Training Australia - Course Notes http://perltraining.com.au/notes.html
 * Perl Training Australia - Perl Tips http://perltraining.com.au/tips/
 * Fundamentals of Perl http://szabgab.com/fundamentals_of_perl.html
 * Welcome to Perl Programming - MIT IAP http://stuff.mit.edu/iap/perl/slides/slides.html
 * Perl Monks Tutorial http://perlmonks.org/?node=tutorials
 * Steve's Perl Tutorial http://www.resoo.org/docs/perl/perl_tutorial/tutorial.html
 * Perl 101 - Things Every Perl Programmer Should Know http://perl101.org/
 * What Every Perl Programmer Should Know http://houston.pm.org/talks/2006talks/0604Talk/tiddlywiki_weppsk.html
 * Perl object reference https://metacpan.org/module/SHAY/perl-5.15.5/pod/perlobj.pod
 * Object-Oriented Programming in Perl Tutorial https://metacpan.org/module/SHAY/perl-5.15.5/pod/perlootut.pod

## 한글
 * gypark님의 Wiki의 Perl섹션 http://gypark.pe.kr/wiki/perl
 * "Perl의 안 좋은 코딩 습관" 요약 번역 http://cafe.naver.com/perlstudy/655
 * 새로운 Perl OOP로 개과천선하기 https://docs.google.com/viewer?a=v&pid=explorer&chrome=true&srcid=1bUdjm4YzxlLhopigzO0drUOiGYm7SxjuiSn0FJCaS-AYXuaF5Uvqcpv-BAUk&hl=ko
 * Perl5편(일한번역기를 통해서 봄) http://j2k.naver.com/j2k_frame.php/korean/www.geocities.jp/ky_webid/perl5/index.html
 * 샘플 코드에 의한 Perl 입문 - 일본어 싸이트(일한번역기를 통해서 봄) http://j2k.naver.com/j2k_frame.php/korean/d.hatena.ne.jp/perlcodesample/20080229/1204271923
 * Perl강좌 - 일본어 싸이트(일한번역기를 통해서 봄) http://j2k.naver.com/j2k_frame.php/korean/www.rfs.jp/sb/perl/
 * perldoc의 일본어번역(일한번역기를 통해서 봄) http://j2k.naver.com/j2k_frame.php/korean/perldoc.jp/

# Feedback
보시고 궁금하시거나 하고 싶은말이 있으시면 https://github.com/aero/perl_docs/issues 에 남겨주세요.

