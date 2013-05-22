# Perl 객체지향(OOP) 프로그래밍

## 알리는 말

이 문서는 일본 Hatena 사의 인턴교육용 문서인 Hatena-TextBook의 oop-for-perl 문서
https://github.com/hatena/Hatena-Textbook/blob/master/oop-for-perl.md 를 번역한 것입니다. 
필요에 따라 내용의 추가 및 약간의 수정이 가해졌습니다. 라이센스는 원문과 같이 Creative Commons license를 따릅니다.

(문서 번역에 도움을 준 [@JEEN_LEE](https://twitter.com/JEEN_LEE/)군과 Seoul.pm 여러분들께 감사드립니다.)


이 문서는 Perl OOP를 위한 핵심만을 굵직하게 짚어나가므로 부족한 점은 다음 문서를 추가로 참고하세요.

* [2시간 반만에 펄 익히기](http://qntm.org/files/perl/perl_kr.html)
* [거침없이 배우는 펄 - Learning Perl한글판 비평](https://github.com/aero/perl_docs/blob/master/Learning_Perl_5th_kor_review.md)


## 목차
* Perl 프로그래밍 핵심
* Perl 객체지향 프로그래밍
* 테스트를 쓰자
* 도움말
* 과제에 대해

# Perl 프로그래밍 핵심

## Perl의 좋은점
* CPAN
  * 하고자 하는 것이 대부분 이미 모듈화되어 있다.
  * 그건 CPAN에서 할 수 있어
* 표현력이 우수하다
  * TMTOWTDI (방법은 얼마든지 있다！)
* 이미 널리 사용되고 있다. 
  * DeNA/NHN/mixi등 
* 좋은 개발문화가 있다.

## 참고 싸이트
* http://perl.kr/ : 한국 Perl싸이트 모음
* https://metacpan.org/ : CPAN 검색

## Perl프로그래밍 핵심
* Perl의 주요특징을 설명
  * use strict;use warnings;
  * 데이터형식
  * 문맥/컨텍스트(Context)
  * 참조/레퍼런스(reference)
  * 패키지(package) 
  * 서브루틴

## use strict;use warnings;
* 파일 시작부분에 반드시 쓰세요

``` perl
use strict;
use warnings;
```

* 저것들을 쓰지 않는 기본 동작은 과거 호환성을 위해 제한사항이 약함.(예: one-liner)

## use strict; use warnings;을 써야하는 이유
``` perl
my $message = "hello";
print $messsage; # typo!
# => 오타를 내도 오류가 발생 안함!
$messagge = "bye"; # typo!
# => $messagge 오류 발생！
```
* Javascript도 최근 use strict 도입, Python, Ruby에서는 이런 장치가 없다. 
* 섬세한 동작은 perldoc참고

``` zsh
$ perldoc strict
$ perldoc perllexwarn
```

## 데이터형식
* 스칼라(scalar)
* 배열(array)
* 해시(hash)
* 파일핸들(file handle)
* 타입글로브(typeglob)

## 스칼라
* 1개의 값
* 문자열/숫자/레퍼런스
* $calar 로 기억합시다.

``` perl
my $scalar1 = "test";
my $scalar2 = 1000;
my $scalar3 = \@array; # 배열의 레퍼런스
```

## 배열
* @rray 로 기억합시다.

``` perl
my @array = ('a', 'b', 'c');
```

* Q:@array의 두번째 요소를 얻으려면?

## 배열 다루기
``` perl
print $array[1]; # 'b'
```

* 스칼라 값을 얻도록 $로 접근
* $array와 @array는 다르다.


## 배열 다루기
``` perl
$array[0]; # get
$array[1] = 'hoge'; # set
my $length = scalar(@array); # 길이
my $last_ids = $#array; # 마지막 인덱스 번호
my @slice = @array[1..4]; # 슬라이스 배열의 인덱스 1~4의 값
foreach my $e (@array) { # 모든 요소 순회
  print $e;
}
```
*  체크해두자！
  * 조작함수: push / pop / shift / unshift / map / grep / join / sort / splice
  * 관련모듈: List::Util / List::MoreUtils

## 해시
* %ash 로 기억합시다.(%는 key/value를 줄여놓은 모양?)

``` perl
my %hash = (
  perl  => 'larry',   # => 앞에는 일반적인 문자열일 경우에 인용부호가없는 문자열(= '"이 없는 bareword)이 허용된다. 
  ruby => 'matz',
);
#위는 my %hash = ( 'perl', 'larry', 'ruby', 'matz' ); 와 같다. =>을 쓰면 키,값사이 시각적 구별효과를 높혀줌.
```

* Q:hash의 key:perl에 해당하는 값을 얻으려면?

## 해시 다루기
``` perl
print $hash{perl}; # larry
print $hash{ruby}; # matz
my @values = @hash{'perl', 'ruby'};  #해시슬라이스 키 perl,ruby에 대한 값들 @와 { }에 주목
```

* 스칼라 값을 얻기위해 $을 사용
* $hash와 %hash는 다른 변수
* {} 안의 키지정에 일반적인 문자열일 경우에 인용부호가없는 문자열(= '"이 없는 bareword)이 허용된다. 

## 해시 다루기
``` perl
$hash{perl}; # get
$hash{perl} = 'larry'; # set
for my $key (keys %hash) { # 전 요소 키에대해 순회
  my $value = $hash{$key};
}
```
* 체크해두자！
  * 함수: keys / values / delete / exists / each

## 데이터형식 요약
* 스칼라 - $ /배열 - @ /해시 - %
* $val과 @val과 %val은 다른 변수

``` zsh
$ perldoc perldata
```

## 컨텍스트
* Perl에는 컨텍스트라는 것이 있다.
* 대표적인 특징
* 식이 평가되는 장소(= 컨텍스트)에 따라 결과가 변함
``` perl
my @x = (0,1,2);
my ($ans1) = @x;
my $ans2 = @x;
```

* 각각 무엇이 들어갈까?

## 컨텍스트
``` perl
my @x = (0,1,2);
my ($ans1) = @x; # => 0
my $ans2 = @x; # => 3

```
* 배열/리스트에 대한 할당의 우변은 리스트 컨텍스트
  * 0이 할당된다. 1,2는 버려짐(void context)
* 스칼라에 대한 할당의 우변은 스칼라 컨텍스트
  * 배열이 스칼라 컨텍스트에서 평가되면 길이를 반환

## 컨텍스트 퀴즈
* <여기>의 컨텍스트는 스칼라, 리스트중 어떤 것일까?

``` perl
sort <여기>;
length <여기>;
if (<여기>) { }
for my $i (<여기>) { }
$obj->method(<여기>);
my $x = <여기>;
my ($x) = <여기>;
my @y = <여기>;
my %hash = (
  key0 => 'hoge',
  key1 => <여기>,
);
scalar(<여기>);
<여기>;
```

## 해답

``` perl
sort <여기>;   # sort함수 뒤는 리스트컨텍스트
length <여기>; # length함수 뒤는 문자열이 오므로 스칼라컨텍스트
if (<여기>) { } # 참,거짓이 들어가는 곳은 불리언(boolen)컨텍스트라고 하며 스칼라컨텍스트의 일종이다.
foreach my $i (<여기>) { }  # 루프로 넘길 것들이 들어가므로 리스트컨텍스트
$obj->method(<여기>);  # 메소드의 인자들이 들어가므로 리스트컨텍스트
my $x = <여기>;  # 스칼라컨텍스트
my ($x) = <여기>; # 리스트컨텍스트
my @y = <여기>; # 리스트컨텍스트
my %hash = (
  key0 => 'hoge',
  key1 => <여기>, # 리스트컨텍스트
);
scalar(<여기>); #스칼라컨텍스트
<여기>; # 리스트컨텍스트
```


## 컨텍스트 정리
* 식/값이 계산되는 위치에 따라 결과가 변함
* 컨텍스트의 결정방법은 기억해야 한다.
  * 내장함수뒤의 인수 부분 주의 (length 등)
  * 내장함수 이외에도 prototype이라는 기능으로 지정 가능하므로 주의

``` zsh
$ perldoc perldata
$ perldoc perlsub # Prototype 참고문서
```


## 레퍼런스/참조
* 스칼라/배열/해시 등에 대한 레퍼런스
  * C++의 레퍼런스를 생각/Ruby등에서는 모든것이 레퍼런스
  * 데이터구조를 만들때 매우 중요함
  
## 데이터구조 요점1
* 행렬을 만들어 보자

``` perl
my @matrix = (
  (0, 1, 2, 3),
  (4, 5, 6, 7),
);
```

* 어떻게 될까?


## 이렇게 된다.
``` perl
my @matrix =
  (0, 1, 2, 3, 4, 5, 6, 7);
```

* 헐

## 데이터구조 요점2
``` perl
my %entry = (
  body => 'hello!',
  comments => ('good!', 'bad!', 'soso'),
)
```

* 어떻게 될까?

## 이렇게 된다.
``` perl
my %entry = (
  body => 'hello!',
  comments => 'good',
  'bad!' => 'soso',
);
```

* 헐

## 왜?
* ()안은 리스트 컨텍스트
* 리스트 컨텍스트에서 리스트는 풀려버림(flatten). Perl에서 괄호는 평가의 우선순위를 높이는 기능만 한다.

``` perl
my @matrix = (
  (0, 1, 2, 3),
  (4, 5, 6, 7),
);
```

## 레퍼런스 얻기/만들기(배열)

``` perl
my @x = (1,2,3);
my $ref_x1 = \@x;
# 주의!  my @x = \(1,2,3);는 (\1,\2,\3)이다. 위의 배열레퍼런스 \@x와는 다름 

# 줄이면
$ref_x2 = [1,2,3];

# 이렇게도
$ref_x3 = [@x]; # 이 경우는 @x가 [ ]안의 리스트컨텍스트에서 확장되어 @x의 레퍼런스가 아니라 내용이 복사되어 [1,2,3]이 된다.
```

## 디레퍼런스

레퍼런스를 일반적 자료형으로 되돌리는 작업

```perl
use 5.010;
my $scalar_ref = \5;
say $scalar_ref;      # SCALAR(0x1c6fc74)  레퍼런스가 위치한 메모리 주소가 찍힘
say ${$scalar_ref};   # 5      ${$scalar_ref}는 $$scalar_ref로 간략화 가능

my $array_ref = [2,3,4];
say $array_ref;       # ARRAY(0x70ba8c)
say @{$array_ref};    # 234  @{$array_ref}는 @$array_ref로 간략화 가능
say ${$array_ref}[0]; # 2      ${$array_ref}[0]는 $$array_ref[0]와 $array_ref->[0]으로 간략화 가능
```

* 형태와 규칙: `원하는변수형_디레퍼런스sigil { 레퍼런스 } 필요할경우_인덱스나 키`
 * 우선순위에 문제없을 경우 레퍼런스를 감싸는 { }는 생략가능(인덱스나 키랑 디레퍼런스sigil중 sigil이 먼저 결합한다.)
 * ${$array_ref}[index] 는 $array_ref->[index], ${$hash_ref}{key} 는 $hashref->{key}로 대체 가능
 * 인덱스나 키를 지정하는 배열이나 해시의 디레퍼런스의 경우 ->를 사용한 형태가 직관적이고 널리 사용됨


## 디레퍼런스 (배열)


``` perl
my $ref_x = [1,2,3];

my @x = map { $_ * 2 } @$ref_x;

print $ref_x->[0]; # 1   ${$ref_x}[0]과 같다. ${$array_ref}[인덱스] 는 $array_ref->[인덱스] 로 나타낼 수 있음

my @new_x = @$ref_x;
print $new_x[0]; # 1
```

## 레퍼런스 얻기/만들기(해시)

``` perl
my %y = (
  perl => 'larry',
  ruby  => 'matz',  # Perl에서는 뒤에 ,가 있어도 상관없다. 넣어놓으면 나중에 코드에 요소를 더 추가할 경우 편리함
);
my $ref_y1 = \%y;

# 줄이면
$ref_a2 = {
  perl => 'larry',
  ruby => 'matz',
}
```

## 디레퍼런스 (해시)

``` perl
my $ref_y = {
  perl => 'larry',
  ruby => 'matz',
};

my @keys = keys %$ref_y;

print $ref_y->{perl}; # larry ${$ref_y}{perl}과 같다. ${$hash_ref}{키} 는 $hash_ref->{키} 로 나타낼 수 있음

my %new_f = %$ref_y;
print $new_f{perl}; # larry
```

## 데이터구조 만들기
* 레퍼런스는 스칼라값이다.(레퍼런스는 메모리상에 위치한 주소를 나타냄을 상기) = 리스트 컨텍스트로 확장되지 않음

``` perl
my $matrix = [
  [0, 1, 2, 3],
  [4, 5, 6, 6],
];
# 5에 접근하려면? 차례로 까면 ${${$matrix}[1]}[1] 화살표 표기로 하면 $matrix->[1]->[1]
# $matrix->[1]->[1] 는 $matrix->[1][1] 로 가능하다.( 연속적인 인덱스, 키 지정 사이의 ->는 생략가능 )

my @matrix2 = (
  [0, 1, 2, 3],
  [4, 5, 6, 6],
);

# 5에 접근하려면? 차례로까면 ${$matrix2[1]}[1] 화살표 표기로 하면 $matrix2[1]->[1]
# 레퍼런스로 만든 행렬 $matrix, 배열로 만든 행렬 @matrix2의 차이점을 확인할 것
```

``` perl
my $entry = {
  body => 'hello!',
  comments => ['good!', 'bad!', 'soso'],
};
# good!에 접근하려면?  차례로 까면 ${${$entry}{comments}}[0] 화살표 표기로하면 $entry->{comments}->[0]

```


## 복잡한 레퍼런스
* 예: 레퍼런스를 반환하는 메소드의 반환값을 디레퍼런스

``` perl
my $result = [
    map {
        $_->{bar};  #각 배열요소(해시레퍼런스)를 디레퍼런스
    }
    @{ $foo->return_array_ref }  #배열레퍼런스 반환
     # ↑ 중괄호를 사용한다.
];
```

## 추천
* 어떤 변수로 쓰는 변수명은 그 이외 자료형 변수명으로 사용하지 말것
  * 구별이 어렵다.

``` perl
my @foo = (1, 2, 3);
my %foo = ( a => 1, b => 2, c => 3);
$foo[1], $foo{a}
# ↑ 얼핏보면 동일한 변수를 참조하는 것 같이 혼돈된다.
# 실제로는 다른 변수
```

``` perl
my $foo = [1, 2, 3];
my $foo = { a => 1, b => 2, c => 3};
# ↑ 같은 변수이므로 warning이 나온다.
```

## 추천
필요한 경우 디레퍼런스한다.

``` perl
my $list = [1, 2, 3];
push @$list, 4;  # push는 첫번째 인수로 배열을 받으므로
```

## 레퍼런스가 아닌 리스트/해시를 사용하면 편리한 때
* 서브루틴 인수처리
* 여러 값을 반환할때

``` perl
sub hello {
  my ($arg1, $arg2, %other_args) = @_;
  return ($arg1, $arg2);
}
my ($res1, $res2) 
  = hello('hey', 'hoy', opt1 => 1, opt2 =>2);
```

## 레퍼런스 정리
* 스칼라/배열/해시에 대한 참조
* 복잡한 데이터구조를 처리할 때 필수
* 표기법이 다소 복잡

``` zsh
$ perldoc perlreftut
$ perldoc perlref
```

## 패키지
* 네임스페이스/이름공간
* 모듈 로딩 방법
* 클래스(후에 설명)

``` perl
package Hoge;
our $PACKAGE_VAL = 10;
# $HOGE::PACKAGE_VAL == 10

sub fuga {
}
# &Hoge::fuga;

1;
```

## 모듈 로딩 방법
``` perl
use My::File;
# => My/File.pm 가 로드됨
```
* @INC(글로벌 변수)에 설정된 경로를 검색

``` perl
use lib 'path/to/your/lib';
$ perl -Ipath/to/your/lib;
```

* path/to/your/lib/My/File.pm 을 찾아서 있으면 로딩

## 서브루틴

``` perl
&hello # 서브루틴 정의부분 이전 괄호없이 호출하려면 &를 붙여야 한다.
sub hello {
  my ($name) = @_; # 호출시 인수가 있다면 @_에 자동으로 전달 인수들이 들어감
  return "Hello, $name";
}
hello(); # 괄호를 붙이면 앞뒤 상관없음(정석적인 방법)
hello; #정의 후 라면 괄호 없어도 호출됨
```

## 인수처리 관용구1
``` perl
sub func1 {
  my ($arg1, $arg2, %args) = @_;  #해시로 이름있는 인수를 흉내낼 수 있다.
  my $opt1 = $args{opt1};
  my $opt2 = $args{opt2};
}
func1('hoge', 'fuga', opt1 => 1, opt2 => 2);
```

## 인수처리 관용구2
``` perl
sub func2 {
  my $arg1 = shift; # 명시적으로 하면 shift @_;이나 @_를 생략해도 @_에서 차례로 가져오므로 관례적으로 쓰는 표현
  my $arg2 = shift;
  my $args = shift; # 여기는 익명해시레퍼런스가 들어온다.
  # 위 3줄은 my ($arg1, $arg2, $args) = @_; 로 해도 된다.
  my $opt1 = $args->{opt1};
  my $opt2 = $args->{opt2};
}
func2('hoge', 'fuga', {opt1 => 1, opt2 => 2});
```

## 인수처리 관용구3

``` perl
sub func3 { shift->{arg1} } # shift한 인수에 대해 바로 적용, func3({arg1 => 2}) 이런식으로 호출했을때?
```

``` perl
sub func4 { $_[0]->{arg1} } # @_ 의 첫째 요소는 $_[0](@a의 첫째요소는 $a[0]인 것과 같은 이치)
```

## 서버루틴의 네임스페이스
* 패키지내에 정의

``` perl
package Greetings;
sub hello { }
1;

# hello 는 &Greeting::hello(); 로 정의된다.
```

* 서버루틴 정의를 중첩해도 상위네임스페이스(패키지)에 정의됨. 주의

``` perl
package Greetings;
sub hello { 
  sub make_msg { }
  sub print {}
  print (make_msg() );
}
1;

# &Greeting::hello();
# &Greeting::make_msg();
# &Greeting::print();
```


## Perl에서 디버깅
* use Data::Dumper; 을 흔히 사용한다.

``` perl
use Data::Dumper;
print Dumper($value); # 스칼라값을 넣는게 좋다. 배열이면 \@array 이런식으로 스칼라값(레퍼런스)으로 만들어서
```
* 에디터의 매크로에 정의해 두고 쓰면 유용


# Perl 객체지향 프로그래밍

## 질문
* 객체지향 프로그래밍을 한적이 있습니까?
  * Perl이외 언어 포함

## 프로그래밍 추상화의 역사
* 추상화란

```
복잡한 자료, 모듈, 시스템 등으로부터 핵심적인 부분을 간추려 내는 것 (Wikipedia발췌)
```

* 한번에 생각하고 고려해야 되는 부분을 줄어준다.
  * 범위를 좁혀준다.
  * 유지보수성/재활용성을 높인다.

## 비구조적 프로그래밍의 시대
* goto프로그래밍
  * 제어의 흐름이 완전 자유로움
* Perl에서 goto프로그래밍 예(fizzbuzz)

``` perl
my $i = 1;
START:
    goto "END" if $i > 35;

    goto "PRINT_FIZZBUZZ" if $i % 15 == 0;
    goto "PRINT_FIZZ"     if $i %  3 == 0;
    goto "PRINT_BUZZ"     if $i %  5 == 0;
    goto "PRINT_NUM";

PRINT_NUM:_
    print $i;
    goto "PRINT_NL";

PRINT_FIZZ:_
    print "fizz";
    goto "PRINT_NL";

PRINT_BUZZ:_
    print "buzz";
    goto "PRINT_NL";

PRINT_FIZZBUZZ:_
    print "fizzbuzz";
    goto "PRINT_NL";

PRINT_NL:_
    print "\n";

    $i++;
    goto "START";

END:
```

## 비구조적 프로그래밍의 문제
* 제어의 흐름을 이해하기 힘들다.
  * 프로그램 전체를 한번에 이해하지 않으면 안된다.
  * 대규모 소프트웨어를 유지하기 힘들다.
* 코드 재사용이 힘들다.

``
EW Dijkstra(1968). Go to statement considered harmful
``

## 구조적 프로그래밍
* 순차, 선택, 반복표현
* 서브루틴에 의해 행위를 추상화

``` perl
sub fizzbuzz {
    my $i = 1;

    while ($i < 35) {
        if ($i % 15 == 0) {
            print "fizzbuzz";
        }
        elsif ($i % 3 == 0) {
            print "fizz";
        }
        elsif ($i % 5 == 0) {
            print "buzz";
        }
        else {
            print $i;
        }
        print "\n";
        $i++;
    }
}
fizzbuzz();
```

## 구조적 프로그램밍만 이용할 때 문제
* 행위와 데이터가 관계가 분리됨

``` perl
open my $fh, '<', $filename;
while (my $line = readline($fh)) {
  print $line;
}
close $fh;
```
* $fh에 대해 어떤 작업을 할 수 있는가?
* open/close/readline 으로 어떤 데이터를 조작할 수 있는가?
  * 데이터, 행위 각각의 범위가 넓다.

## 객체지향 프로그래밍
* 객체의 추상화
* 객체는
  * 프로그램의 대상이 되는 물건
  * 데이터 + 행위

* 프로그램은 객체간의 상호작용
* "어떻게"보다는 "무엇이 어떻게"에 주목

## 객체지향 프로그래밍이 좋은점
* 처리의 대상(데이터)와 처리내용(행위)가 연결되어 있다.
  * 객체가 코드를 이해한다.
  * 재사용이 가능하다.
* 객체라는 비유가 인간에게 익숙하다.
  * => 디자인하기 쉽다.

## 예

``` perl
use IO::File;
my $file = IO::File->new($filename, 'r');
while (my $line = $file->getline) {
  print $line;
}
$flie->close;
```
* $file에 대한 모든 행위가 메소드로 정의되어 있음.

## 객체지향 프로그램의 실현
* 객체 간의 상호 작용 / 메시지 전달
* 객체 간의 상호 작용을 표현하기 쉽도록 프로그래밍 언어 지원 
  * 클래스와 인스턴스
  * 캡슐화
  * 상속
  * 다형성
  * 동적 바인딩

## 주제

## Perl 클래스와 인스턴스

``` text
      클래스 : [데이터 구조 정의 + 행위 정의]
                       ↓　생성
인스턴스 : [{data} + 행위에 대한 레퍼런스(참조)] 
```

* 클래스: 패키지
* 메소드: 패키지에 정의 된 함수
* 객체: 특정 패키지에 bless() 된 레퍼런스

##  클래스 정의(클래스 이름)
* 과제로 나온 Sorter 클래스(간략 판)

아래를 Sorter.pm 이란 파일로 만들자

``` perl
# 패키지 절차를 정의
package Sorter; # 클래스 이름
use strict;
use warnings;

# 코드 계속
```

## 클래스 정의 (생성자 / 필드)

``` perl
# 생성자
# Sorter->new; 처럼 호출, 이렇게 호출하면 첫번째 인수로 Sorter가 넘어간다.
# Sorter->new;는 내부적으로 Sorter::new('Sorter') 처럼 호출된다. 첫번재 인자로 클래스 이름이 넘어가는 이유
sub new {
    my ($class) = @_; # 클래스이름(이 경우에 Sorter)이 들어감
    # 데이터를 준비한다.
    my $data_structure = {
        values => [],
    };
    # 행위(= 패키지)와 데이터를 연결하는
    my $self = bless $data_structure, $class; # $data_structure를 $class이름(Sorter)로 테깅하여 객체화
    return $self;    # $self는 다른 변수명으로 써도 되지만 python에서 처럼 관용적으로 self로 쓴다.                    
}
# 위에서 bless는 클래스 이름을 $data_structure에 테깅하여 그 레퍼런스를 리턴하므로
# 결국 bless된 $data_sturcture와 $self는 같다. 
# bless 하기전에 변수의 타입을 알아보는 ref 함수를 사용하여 print ref $data_structure 하면 HASH 라고 나오지만
# bless 해서 객체화 한 후에는 찍어보면 Sorter가 나온다.
# 위 코드를 다음과 같이 써도 똑같다. 원리를 이해하면 어떤 모양이든 혼돈안됨

# 가장 널리 쓰이는 형태
# sub new {
#     my ($class) = @_;
#     my $self = {
#         values => [],
#     };
#     bless $self, $class;
#     return $self;                     
# }

# 극도로 줄이면?
# sub new {
#     return bless { values => [] }, shift;
# }

# 코드 계속
```

## 클래스 정의 (메소드)

``` perl
# $sorter->set_values(0,1,2,3) 와 같이 호출
# 내부적으로는 Sorter::set_values($sorter,0,1,2,3)처럼 호출된다. 첫번째 인수에 $sorter가 들어가는 이유
sub set_values {
    my ($self, @values) = @_; # $self에는 $sorter가 들어감
    $self->{values} = [@values];
    return $self;
}

sub get_values {
    my ($self) = @_;

    return @{ $self->{values} };
} 

sub sort {
    my ($self) = @_;
   
    $self->{values} = [ sort { $a <=> $b } @{ $self->{values} } ];
}  

1; # 별도의 파일로 패키지 모듈을 만들경우 잘 로딩되었다고 알리기 위해 파일 끝에 참(true)값을 넣음
```
여기까지가 Sorter.pm

## 클래스의 사용

``` perl
use Sorter;

my $sorter = Sorter->new;
$sorter->set_values(5,4,3,2,1);
```

## 한 파일 안에서 클래스와 메인코드를 같이 넣어 테스트 해보려면?

```perl
package Sorter;
...

# 별도의 .pm 파일이 아니라 끝에 1;은 필요없고 다른 패키지가 선언되면 해당패키지 영역은 끝난다. 
package main;   # 스크립트 실행 코드가 실행시작되는 기본 패키지는 main패키지
# 이미 Sorter패키지가 한파일 안에 있어 로딩된 상태이므로 따로 use Sorter;를 해줄 필요는 없다.
my $sorter = Sorter->new;
$sorter->set_values(5,4,3,2,1);
```

패키지영역은 렉시컬범위 이므로 블럭 { }으로 묶어서 이렇게 해도 된다.
```perl
{
   use Sorter;
   ...
}
# { }에 의해 여기서는 명시하여 패키지를 바꾸지 않아도 main영역임
# 블럭으로 묶으면 서로 다른 패키지간의 렉시컬영역을 확실히 구분하여 내부변수등의 충돌 가능성이 줄어든다. 
my $sorter = Sorter->new;
$sorter->set_values(5,4,3,2,1);
```


## 생성자
* 생성자는 수동으로(관례적으로 new라는 이름으로) 정의
* 객체를 스스로 만듦
* new()
  * 레퍼런스를 클래스(패키지)이름으로 bless를 통해 테깅하여 반환
* bless은 데이터와 행위(메소드)를 묶는 작업

``` perl
  my $self = bless { field => 1 }, "Sorter";
```

## 클래스 및 인스턴스 메소드
* 문법적인 차이는 없다.
* 정의시: 제1인수를 $class로 할지 $self로 할지
* 호출시: 클래스에서 호출하거나 인스턴스객체에서 호출하거나.

``` perl
Class->method($arg1, $arg2);
&Class::method("Class", $arg1, $arg2);

$object->method($arg1, $arg2);
&Class::method($object, $arg1, $arg2);
```

## 필드
* 1인스턴스에 1데이터(레퍼런스)
* 여러 클래스 데이터를 가지고 싶다면 해시 레퍼런스를 bless 함.(드물게 쓰지만 스칼라 레퍼런스,배열 레퍼런스도 bless가능함)

``` perl
my $self = bless {
    filed1 => $obj1,
    field2 => [],
    field3 => {},
}, $class;
```

## 캡슐화
* 공개여부 지정(public/private등) 은 없다.
  * 모두 public
* 명명규칙으로 관례적으로 공개여부를 구별한다.(python 처럼)

``` perl
sub public_method {
  my $self = shift;
}

sub _private_method {
  my $self = shift;
}
```
* 필요하다면 완전히 숨기는 방법도 있긴하다.(클로저를 사용)

## 상속
* use parent; 사용(약간 동작이 다르긴 하지만 use base;도 씀)

``` perl
package Me;
use parent 'Father';
1;
```
* 부모클래스에 접근하기 위한 메소드
  * SUPER

``` perl
sub new {
  my ($class) = @_;
  my $self = $class->SUPER::new();  # 부모클래스의 생성자를 호출
  return $self;
}
```

## 다중상속
* Mixin같은 걸 하고싶을때 사용
* 남용하면 오히려 안좋음

``` perl
package Me;
use parent qw/Father Mother/; # 탐색순서 좌->우
1;
```
* 메소드 검색 알고리즘
  * Class::C3
  * Next

## 객체지향 정리
* 객체를 손수 만든다.
  * package 에 메소드를 정의
  * bless로 데이터와 결합
  * 객체를 만드는 클래스생성자도 직접 만든다. 
  * Dan kogai said "That's why you should learn perl if you want to learn OO! You can learn how to make an object system, not just how to use it." 

* 객체지향에 필요한 기능은 모두 갖추어져 있다.
* 추가 참고
  * [
새로운 Perl OOP로 개과천선 하기](http://www.docstoc.com/docs/23978407/%EC%83%88%EB%A1%9C%EC%9A%B4-Perl-OOP%EB%A1%9C-%EA%B0%9C%EA%B3%BC%EC%B2%9C%EC%84%A0-%ED%95%98%EA%B8%B0)
  * [perlobj - Perl object reference](http://perldoc.perl.org/perlobj.html)
  * [perlootut - Object-Oriented Programming in Perl Tutorial](http://perldoc.perl.org/perlootut.html)

## UNIVERSAL
* Java의 Object 같은것
* UNIVERSAL에 정의된 것은 어떤 객체에서도 호출할 수 있다.
* isa()

``` perl
my $dog = Dog->new(); 
$dog->isa('Dog');    # true 
$dog->isa('Animal'); # true 
$dog->isa('Man');    # false 
```
* can()

``` perl
my $bark = $dog->can('bark'); 
$man->$bark();
```

## AUTOLOAD
* 호출한 메소드가 My::Class에 없는 경우
  * My::Class::AUTOLOAD메소드 검색
  * 부모클래스의 AUTOLOAD메소드 검색
  * UNIVERSAL::AUTOLOAD 검색
  * 못찾으면 오류

* AUTOLOAD는 정의 되지않은 메소드를 호출할시 호출됨
* Ruby 의 method_missing

## AUTOLOAD
* 필드를 동적으로 정의할 수 있고
* 예측하기 힘든 행동을 만들 수 있으므로 되도록 사용하지 않는다.
  * 이런 구조를 가지는 건 이해해 두자

``` perl
package Foo;

sub new { bless {}, shift }

our $AUTOLOAD;
sub AUTOLOAD {
    my $method = $AUTOLOAD;
    return if $method =~ /DESTROY$/;
    $method =~ s/.*:://;
    {
        no strict 'refs';
        *{$AUTOLOAD} = sub {
            my $self = shift;
            sprintf "%s method was called!", $method;
        };
    }
    goto &$AUTOLOAD;
}

1;
```

## 연산자 오버로드
* 예측하기 힘든 행동을 만들 수 있으므로 되도록 사용하지 않는다.
  * 이런 구조를 가지는 건 이해해 두자
* URI

``` perl
my $uri = URI->new('http://exapmle.com/');
... do something ...
print "URI is $uri";
```

* DateTime

``` perl
$new_dt = $dt + $duration_obj;
$new_dt = $dt - $duration_obj;
$duration_obj = $dt - $new_dt;
for my $dt (sort @dts) {          # sort내에서 사용되는 <=> 연산자가 오버로드 되어있음
    ...
}
```

## 클래스 빌드
* Perl의 객체지향은 수동으로 만드는 느낌이 강하다.
  * new도 스스로 만든다.
  * 필드 접근자도 스스로 정의

* 번거롭기 때문에 자동화시켜주는 모듈들이 존재한다.

## Class::Accessor::Fast
* 생성자/접근자를 자동으로 정의해주는 모듈
* 요즘은 Moose, Moo, Mouse라는 모듈때문에 잘 쓰지 않으나, 간단한 용도로 많이 쓰였음.

### before

``` perl
package Foo;

sub new {
    bless {
        foo => undef,
        bar => undef,
        baz => undef,
    }, shift
}

sub foo {
    my $self = shift;
    $self->{foo} = $_[0] if defined $_[0];
    $self->{foo};
}

sub bar {
    my $self = shift;
    $self->{bar} = $_[0] if defined $_[0];
    $self->{bar};
}

sub baz{
    my $self = shift;
    $self->{baz} = $_[0] if defined $_[0];
    $self->{baz};
}

1;
```

### after

위 코드는 다음과 같이 줄일 수 있다.

``` perl
package Foo;

use parent qw/Class::Accessor::Fast/;
__PACKAGE__->mk_accessors(qw/foo bar baz/);  # Ruby의 attr_accessor와 비슷?

1;
```

## Class::Data::Inheritable
* 상속가능한 클래스 변수를 만들때

``` perl
use parent qw/Class::Data::Inheritable/;
__PACKAGE__->mk_classdata(dsn => 'dbi:mysql:dbname=foo');
1;
```

## 합쳐서 쓰이는 형태 예

``` perl
package UsualClass;
use parent qw/Class::Accessor::Fast Class::Data::Inheritable/;
__PACKAGE__->mk_classdata(hoge => "classdata");
__PACKAGE__->mk_accessors(qw(foo bar baz));

# 메소드 등 정의

1;
```

## 다른 클래스 빌드 모듈들.

### [Moose](https://metacpan.org/release/Moose)
* 현대적인 객체지향을 실현하는 모듈
* 유연하고 안전한 접근자를 생성
* 타입시스템 도입
* Role에 의한 인터페이스지향 설계

* 소규모에 쓰기엔 다소 덩치가 크고 복잡하다.

### [Mouse](https://metacpan.org/release/Mouse)
* Moose의 경량버젼
  * Moose의 초기로딩 시간에 좀 걸리는것에 비해 가벼움
  * Moose에 비해 기능은 일부 제한적

### [Moo](https://metacpan.org/release/Moo)
* Moose의 경량버젼
  * 가벼운 Moose를 원할 경우 요즘 Moose대신에 많이 쓴다.
  * Mouse만큼 가볍다.
  * Moose와 같이 사용시 필요시 Moose객체로 자동 업그레이드 해줌.

## 객체지향 프로그램의 작성요령

* 등장인물을 생각 = 객체
* 등장인물이 각각 어떠한 책임(responsibility)을 수행해야 하는지 생각한다.
* 책임에 맞추어 범위를 제한하도록 하기
  * "캡슐화에서 상속에서 다형성이...."등등 너무 복잡하게 생각할 필요는 없다.
  * 보다 좋은, 쉽게 문제를 모델링 하기 위한 수단

## 책임(Responsibility)이란？

* 객체의 이용자, 메소드를 호출하는 곳과의 약속
  * 책임이 없는 것은 하지 않아도 된다
  * 책임이 없는 것은 해서는 안된다
* 책임을 깔끔하게 나누는 것으로 깔끔하게 설계할 수 있다.

## 객체지향 정리
* 직접 만든 느낌이 물씬 나는 객체지향
  * package 에 메소드를 정의
  * bless 로 데이터(레퍼런스)를 묶는다
  * 생성자는 스스로 만든다
  * 객체지향적으로 호출하기 용이하도록 포장

# 테스트 코드를 쓰자

## 테스트는 중요하다

* 프로그램을 변경하는 두가지 방법 [레거시 코드 개선가이드로부터]
  * 고치고 기도한다
  * 테스트코드를 써서 보호하고 변경한다
  
## 왜 테스트코드를 쓰는 건가?

* 테스트가 없으면 프로그램이 제대로 움직인다는 것을 증명할 수 없다
* 대규모 프로젝트에서는 치명적
  * 옛날에 쓴 코드는 지금도 움직이고 있는가?
  * 새로운 코드와 옛 코드의 정합성은 가지고 있는가?
  * 제대로된 사양/의도가 무엇이었는지 기억하고 있는가?
* Perl 처럼 타입이 없는 동적인 언어에서는 특히 중요

* 기도하지 말고 테스트코드를 쓰자

## 무엇을 테스트할 것인가

* 정상적인 처리
* 비정상적인 처리
* 그 경계

* 100% 커버하는 것은 어렵다 
  * 명령문 망라(C0)/분기망라(C1)/조건망라(C2)
  * C2 는 정말 큰일
* 필요/위험하다고 생각되는 곳부터 쓰고 조금씩 보완해나간다.

## Perl 로 Test

* Test::More
* Test::Fatal
* Test::Class

## Test::More

``` perl
use Test::More;

subtest 'topic' => sub {
  use_ok    'Foo::Bar';
  isa_ok    Foo::Bar->new, 'Foo::Bar';
	ok        $something_to_be_bool;
	is        $something_to_be_count, 5;
	is_deeply $something_to_be_complicated, {
	    foo => 'foo',
	    bar => [qw(bar baz)],
	};
};

done_testing;
```

## Test::Fatal
``` perl
use Test::Fatal;

ok( exception{ $foo->method }, '예외를 던진다');
like( exception { $foo->method },  qr/division by zero/, '0 나눗셈 에러가 발생한다');
isa_ok( exception { $foo->method }, 'Some::Exception::Class', '예외 클래스가 던져진다');
```

## Test::Class
* 메소드로 테스트코드를 나눈다
* xUnit계열

* Sorter 의 테스트 코드를 봐!

## 그 외

### Test::Name::FromLine
* 이름이 붙지 않은 테스트를 보기 좋게 표시해준다

### Test::Most
* 편리한 테스트 모듈을 한꺼번에 use 해준다

## 테스트 실행
* 테스트 코드는 `t` 디렉토리에 `.t` 확장자를 붙여서 저장한다
  * t/hoge.t
* prove 커맨드(Test::More에 첨부)로 실행한다

``` zsh
$ prove -lvr t
t/hoge.t .. 
ok 1 - L8: is Hoge::hey(10), 100;
1..1
ok
All tests successful.
Files=1, Tests=1,  0 wallclock secs ( 0.02 usr  0.01 sys +  0.03 cusr  0.01 csys =  0.07 CPU)
Result: PASS
```

## 테스트를 쓰는 기술
* 우선, 어떤 방식으로 행동해야 된다라는 것을 테스트로 쓴다

``` perl
is_deeply( [numsort(2,3,4,0,1)], [0,1,2,3,4], '랜덤한 수열을 sort 하면 오름차순으로 정렬된다' );
```
* 다음으로 경계조건으로 행동을 검증하는 테스트를 쓴다

``` perl
is_deeply( [numsort()], [], '빈 배열을 sort 하면 빈값이 된다' );
is_deeply( [numsort(100)], [100], '한 요소 뿐이라면 그대로' );
```
* 예외 조건에 대해서도 확인한다 

``` perl
ok( exception { [numsort('hoge')] },'문자를 넘기면 예외발생' );
```

## Test::Hatena
* 하테나 사내에서 표준화 중인 테스트 프레임워크
* Test::More/Test::Exception 등의 편리한 함수
* 유닛테스트/결합테스트/부작용이 있는 테스트에 대해서 지원

## 리팩토링

* 리팩토링이란?
  * 프로그램의 행동을 변경하지 않고 구현을 변경하는 것 
* 테스트가 없으면 외부기능의 변경이 없다는 것을 증명할 수 없다
  * 테스트가 없으면 리팩토링이 아니다
* 레거시한 코드에 대해서는 어떻게 하는가?
  * 우선은 테스트 코드를 쓸 수 있는 상태로 만든다.

* 테스트 코드를 써서 리팩토링해서 항상 깔끔하게 유지보수하기 쉬운 코드를 쓰자

# 힌트

## 문서를 찾아보자
* perldoc perltoc 이 편리！
  * 미리 정의된 변수　$_ @_ $@ 
    * perldoc perlvars 를 본다
* 함수
  * perldoc -f open
  * http://perldoc.perl.org/ : perldoc 문서
* CPAN 모듈
  * https://metacpan.org/

## 좋은 책을 읽자
* Programming Perl
* Beginning Perl
* Perl Best Practice
  * Perl::Critic
* 모던 Perl 입문

## 프로젝트 코드를 쓸 때의 마음가짐
* 코드는 누군가가 읽을 수 있어야 한다는 것을 의식한다
  * 나중에 누군가 읽어도 알기 쉽게 쓴다
  * 암묵적인 룰을 안다 => 코드를 쉽게 이해한다
* 테스트를 써서 의도를 전달한다

## 정리
* Perl 프로그래밍 기초
* Perl 에 의한 객체지향 프로그래밍
* 테스트코드를 쓰자
* 힌트

* 오늘 써서 오늘 헤매자
  * 라고 말해도 시간이 그다지 없기때문에 무리는 하지 말고

# 과제

## 과제1
이 강의를 마치고, 사전과제로 작성한 Sorter 클래스를 개량/완성해주세요.

### 이하 재게시
아래처럼 인터페이스를 가지고, 정수값을 오름차순으로 정렬하는 정렬클래스를 Perl 을 사용해서 구현해주세요. 여유가 있는 사람은 알고리즘 별로 Sorter 서브 클래스를 구현해주세요.

``` perl
my $sorter = Sorter->new;
$sorter->set_values(5,4,3,2,1);
$sorter->sort;
$sorter->get_values # (1,2,3,4,5) 이 반환된다
```

구현에 대해서는 아래의 조건을 지켜주세요.

* Perl 내장 sort 함수나 정렬을 하는 CPAN 모듈을 이용하지 않는다
* 알고리즘은 퀵소트를 사용한다

## 과제2
객체지향 연결리스트 My::List 를 작성해주세요.(C언어에서 자주 사용되는 연결리스트 데이터 구조를 객체지향 Perl 로 만든 것이라고 생각해주세요)

리스트의 각 요소에는 임의의 데이터(스칼라, 객체, 레퍼런스 등) 을 저장할 수 있는 것으로 합니다. 연결 리스트는 객체지향 인터페이스를 가집니다. 리스트의 요소를 먼저 더듬어가면서 Iterator 를 준비해주세요.

My::List 의 프로그램 인터페이스는 아래처럼 될 겁니다. 아래의 예제는 리스트에  "Hello" "World" "2008" 라는 세가지 문자열을 저장하고, Iterator 로 그것들을 뽑아서 출력하는 코드입니다.

``` perl
my $list = My::List->new;

$list->append("Hello");
$list->append("World");
$list->append(2008);

my $iter = $list->iterator;
while ($iter->has_next) {
    print $iter->next->value;
}
```

리스트의 각 요소는 My::List 안에 배열로 저장하는 것이 아니라, 해쉬기반의 리스트 요소의 객체를 리퍼런스로 연결한 연결리스트로 가진다는 점에 주의해주세요.

* 연결리스트가 어떤 데이터 구조인지 알 수 없는 경우는 『定本 C프로그래머를 위한 알고리즘과 데이터 구조(SOFTBANK BOOKS)』 P.50 - 66 를 참고해주세요. (그리고, 연결리스트 등은 알고리즘과 데이터 구조의 기초이기에, 관련지식이 없는 경우는 이 책을 통독하기를 권합니다.)

* Iterator 에 대해서는 『증보개정판 Java언어로 배우는 디자인패턴 입문』의 첫번째장을 참고해주세요.

위 두 서적모두 회사의 책장에 있습니다.

## 과제3(옵션)
twitter와 비슷한 프로그램을 만들어보자.

다음 내용을 처리할 수 있는 짹짹이 객체를 구현해주세요.

* 짹짹이는 트윗할 수 있다
* 짹짹이는 다른 짹짹이를 follow 할 수 있다
* 짹짹이는 follow 하고 있는 짹짹이의 트윗을 볼 수 있다

여러가지 설계방법이 있겠습니다만, 깔끔하고 멋들어진 것을 생각해봅시다.
여유가 있다면, mentions(@기법)이나 unfollow 나 block, lists 등의 기능도 구현해주세요.

프로그램의 인터페이스는 자유입니다. 예를들어 아래와 같은 인터페이스를 생각할 수 있습니다. 아래의 예제에서는 SmallBird 클래스밖에 없지만, 트윗이나 전체를 관리하는 클래스가 있어도 좋겠네요. Observer 패턴을 사용해도 됩니다.

``` perl
use Bird;
my $b1 = Bird->new( name => 'jkondo');
my $b2 = Bird->new( name => 'reikon');
my $b3 = Bird->new( name => 'onishi');

$b1->follow($b2);
$b1->follow($b3);

$b3->follow($b1);

$b1->tweet('오늘은 덥네요！');
$b2->tweet('커피마시러 나왔습니다');
$b3->tweet('지금 여의도！');

my $b1_timelines = $b1->friends_timeline;
print $b1_timelines->[0]->message; # onishi: 지금 여의도!
print $b1_timelines->[1]->message; # reikon: 커피마시러 나왔습니다

my $b3_timelines = $b3->friends_timeline;
print $b3_timelines->[0]->message; # jkondo: 오늘은 덥네요!
```

## 과제에 들어갈 때의 주의점

* 가능한한 테스트스크립트를 쓴다
  * 적어도 돌려서 시험해볼 수 없다면 채점할 수 없습니다.
  * 과제의 본질적인 부분만 구현하면 CPAN 모듈로 쉽게 해도 됩니다.
  * 무엇이 본질적인가를 확인하는 것도 과제 중 하나
* 여유가 있다면 기능 추가해보세요.
* 과제에 대해서는 아래처럼 디렉토리를 작성해서 커밋해주세요.
  * push 하는 것을 잊지 말고!

``` text
intern/username/100802/exercise1
                      /exercise2
                      /exercise3
               /100803/exercise1
                      /exercise2
                      /  ...
                      /  ...
```
Perl의 습관으로써, 아래와 같은 디렉토리 구성으로 커밋하면 여러모로 좋습니다.

``` text
.
|-- lib
|   `-- MyClass
|       `-- Foo.pm
`-- t
    `-- 00_base.t
```
* 라이브러리를 놓는 디렉토리는 lib
* 테스트 파일을 놓는 디렉토리는 t
* 테스트 스크립트는 prove -Ilib t 로 실행합니다
* 또는 테스트 스크립트 하나를 perl -Ilib t/00_base.t 로 실행합니다.

## 과제의 채점기준
각 과제의 만점 = 과제1: 4점 + 과제2: 4점 + 과제3: 2점 = 10점

* 강의 또는 교과서의 이해도가 과제의 구현에 반영되었는가 
* 테스트용 스크립트가 실제로 동작하는 가 
* 설계/코드가 제대로 되어 있는가 
* 독자적으로 기능추가가 되어있는가

* 과제1과 과제2를 제대로 한 다음에 과제3에 도전해주세요.

## 과제의 해답

* 스스로 풀어보려고 노력해보세요~
* https://github.com/limitusus/Hatena-Textbook 에 해답 예시가 있습니다.

## 해야할 일
* git clone
* 과제


<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/2.1/jp/"><img alt="クリエイティブ・コモンズ・ライセンス" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/2.1/jp/88x31.png" /></a><br />이 작품은 <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/2.1/jp/">크리에이티브 커먼즈 표시 - 비명리 - 상속 2.1 일본 라이센스의 아래에서 제공됩니다.</a>
