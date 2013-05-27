#Perl을 사용한 테스트 작성법

## 알리는 말
Perl은 [Test-Infected Culture](http://modernperlbooks.com/mt/2009/04/a-test-infected-culture.html)를 가지고 있다고 할만큼 테스트에 철저하며 [모듈을 만들어 CPAN에 올리면 자동화된 절차와 테스트자원봉사자들에 의해 Perl이 포팅된 모든 펄버젼/플랫폼별로 모듈이 빌드되고 테스트되며](https://twitter.com/ithinkihaveacat/status/336456735835430912) 그 결과가 [cpantesters](http://static.cpantesters.org/)싸이트에 통보됩니다.(예: [JSON::XS 모듈의 테스트결과 리포트](http://static.cpantesters.org/distro/J/JSON-XS.html) ) 이것은 어떤 여타 언어도 가지지 못한 테스트인프라이며 Perl의 안정성과 신뢰성을 높혀주는 주요한 강점입니다. 

참고:[Why I Stuck With Perl](http://blogs.perl.org/users/jeffrey_kegler/2011/01/why-i-stuck-with-perl.html)

이 글은 이런 강력한 Perl Test의 기본을 소개하기 위해 일본 Hatena사의 인턴교육용 문서인 Perl을 사용한 테스트 작성법 https://github.com/hatena/Hatena-Textbook/blob/master/test-for-perl.md 을 번역한 문서입니다. 라이센스는 원문과 같이 Creative Commons license를 따릅니다.

번역에 도움을 준 네이버 일한번역기와 구글번역기에 감사드립니다. :)


##왜 테스트를 쓰는가?
코드를 쓰면, 테스트를 씁니다. 그건 왜죠?코드의 올바르게 돌아갈 것이라고 보장하기 때문입니다. 미래에 나 자신이나 남이 코드를 수정해도 테스트과정이 있다면 프로그램은 정상적으로 움직일 것을(어느 정도)보장하는 것이 테스트입니다.

<img src="http://serif.hatelabo.jp/images/cache/24ddb30ea651235a1bca4dad211c9dbdf249bd25/b1ca465c55db54eb1ea8dcb2f8e870daf5e8c57f.gif" />

##테스트를 작성하는 법
테스트의 기본은[Test::More](http://search.cpan.org/perldoc?Test::More)입니다. perldoc을 읽는 것이 좋지만, 아래 순서를 따라 설명합니다.

###기본은 ok()
일단 설명을 위해 테스트용 모듈을 추가합니다.

lib/Calc/Simple.pm

``` perl
package Calc::Simple;
use strict;
use warnings;
use base qw(Exporter::Lite);

our @EXPORT = qw(add);

sub add {
    $_[0] + $_[1];
}

1;
```

새로운 코드를 작성했으니 이 코드의 올바르게 돌아갈 것을 보장하는 테스트를 작성해야 한다. 아래에서 바로 테스트 스크립트를 작성합니다.
테스트 스크립트는 보통

* 디렉토리는 t/이하에 두고
* 확장명은 .t

다는 규칙을 따릅니다.

그래서 t/calc-simple.t 라는 이름을 붙여서 다음과 같은 파일을 만듭니다. 실체는 그냥 perl스크립트입니다.


``` perl
use strict;
use warnings;
use Test::More tests => 2;
use Calc::Simple;

ok add(2, 3) == 5, '2 + 3 should be 5'；
ok add(100, 0) == 100, '100 + 0 should be 100';
```

간단하네요.<code>use Test::More</code>의 행에서 테스트 갯수를 선언하고 테스트할 식을<code>ok()</code>에 전달할 뿐입니다. 마지막의 인수는 각 테스트의 설명입니다. 이것으로 일단 테스트를 작성했습니다.

테스트를 실행하려면 단순히 Perl스크립트처럼 돌려주면 됩니다.


``` text
% perl -Ilib t/calc-simple.t
1..2
ok 1
ok 2
```

###테스트 실행 ― prove
<code>prove</code>라는 명령([Test::Harness](http://search.cpan.org/perldoc?Test::Harness)모듈에 따라오는 것 같다.)을 사용하면 앞의 출력을 잘 꾸며서 테스트 결과를 보고 받으실 수 있습니다.

"perl"을"prove"으로 바꾸면.

``` text
% prove -Ilib t/calc-simple.t
t/calc-simple....ok
All tests successful.
Files=1, Tests=2,  0 wallclock secs ( 0.03 usr  0.05 sys +  0.01 cusr  0.05 csys =  0.14 CPU)
Result: PASS
```
테스트가 통과하고 있음을 확인하고 이 논리가 옳다고 확신을 얻습니다.

###테스트에 실패한다면
예를 들면 누군가가 Calc/Simple.pm에 손을 대서


``` perl
package Calc::Simple;
use strict;
use warnings;
use base qw(Exporter::Lite);

our @EXPORT = qw(add);

sub add {
    $_[0] - $_[1]; # 여기 여기
}

1;
```

수정했다면, prove의 결과는


``` text
% prove -Ilib t/calc-simple.t
t/calc-simple....1/2
#   Failed test at t/calc-simple.t line 6.
# Looks like you failed 1 test of 2.
t/calc-simple.... Dubious, test returned 1 (wstat 256, 0x100)
 Failed 1/2 subtests

Test Summary Report
                                    * 
t/calc-simple (Wstat: 256 Tests: 2 Failed: 1)
  Failed test:  1
  Non-zero exit status: 1
Files=1, Tests=2,  0 wallclock secs ( 0.04 usr  0.04 sys +  0.03 cusr  0.03 csys =  0.14 CPU)
Result: FAIL
```

테스트 스크립트의 6번째 줄


``` perl
ok add(2, 3) == 5;
```

에서 실패하고 있고. 이렇게 해서, Calc/Simple.pm에 손댄 사람은 자기가 수정한 부분이 틀렸음을 알 수 있습니다.


## 다른 테스트 함수 ― is, like, is_deeply, isa_ok, diag
기본 ok()는 어떤 식이 참인지 거짓인지 확인할 뿐이지만, 테스트를 실행할때 좀 더 상세한 설명을 원할 때도 있기 때문에 여러가지 함수가 준비되어 있습니다.

###  is()

``` perl
ok $got == $expected;
```

대신


``` perl
is $got, $expected;
```

라고 쓸 수 있다. 이 테스트가 실패할 경우


``` text
#   Failed test at t/calc-simple.t line 6.
#          got: '-1'
#     expected: '5'
# Looks like you failed 1 test of 2.
```

라는 형태의 친절한 메시지가 나와 기쁩니다.

###  like
정규식으로 체크. 정규식은 qr//로 전달.("m//"으로 쓰면 그 자리에서$_과의 매칭이 이루어지므로 예상과 다르게 작동합니다:See`perldoc perlop')


``` perl
like $_->res->content, qr/^[[:ascii:]]*$/, 'Content should be ASCII';
```

###  is_deeply
해시레퍼런스나 배열레퍼런스끼리 비교한다


``` perl
is_deeply $app->where, ['( foo = ? ) and ( bar != ? )', 1, 2];
```

###  isa_ok
어떤 클래스의 인스턴스인지 판별


``` perl
isa_ok $movies, 'DBIx::MoCo::List';
```

### diag
이것은 테스트 함수는 아니지만 소개. warn적인 것. warn보다는 이것을 사용한다.

ok()계의 함수는 실패한다고 거짓을 반환(아마두)하므로, 이런 사용법이 가능합니다


``` perl
ok ref $encode_guess && $encode_guess eq 'UTF-16LE', 'encoding is utf-16le'
    or diag YAML::Dump $encode_guess;
```
 
# Test::Class
여태 Test::More를 써 왔지만[Test::Class](http://search.cpan.org/perldoc?Test::Class)를 사용하면 더 편리하게 테스트할 수 있다. 이것을 이용한 테스트는


``` perl
package ThisTest;
use strict;
use warnings;
use base qw(Test::Class);
use Test::More;

sub sample_test : Test(2) {
    is add(2, 3), 5;
    is add(100, 0), 100;
}

__PACKAGE__->runtests; # 이것은 잊지말것

1;
```

같은 느낌의 Test::Class를 상속한 클래스를 만들어, Test(n)과 어트리뷰트를 붙인 메소드 속에 테스트를 넣는다. 물론 n은 테스트의 수. 그리고 마지막으로 runtests 한다.

###  Test(setup)어트리뷰트
"Test(setup)"라는 어트리뷰트가 있는 메소드는 각 테스트 메소드가 호출되기 전에 반드시 실행됩니다. 그래서 fixture(뒤에 설명)의 로딩등은 여기서 한다.

###  TEST_METHOD환경 변수
Test::Class의 편리한 점은 TEST_METHOD환경변수에 메소드 이름을 넣어 실행하면 해당 메소드만 테스트하는 것. 테스트 전체가 길지만, 일부만 테스트만 하고 싶다고 할 때 유용하다.

``` text
% TEST_METHOD="program" prove -lv -It/lib t/app/ds-hotmovies.t
```


##  prove 의 옵션

자세한 것은[perldoc prove](http://search.cpan.org/perldoc?prove)을 읽어보세요.

###  -l
 -Ilib 과 함께

###  -v
verbose이다.

###  --state={save,failed}
 --state=save 로 두면 테스트 결과를 ./.prove에 저장해 준다.
 
이후에 --state=failed를 지정하고 다시 테스트를 실행하면 지난번 실패한 테스트만 실행해 주므로 편리.

###  .proverc
위에서 쓴 옵션은  ~/.proverc에 넣어 두면 prove때 자동으로 적용한다.  motemen의 ~/.proverc는
``` text
--state=save -l -It/lib
```
입니다. 기본은 prove에서 테스트가 실패하면 성공할 때까지 prove --state=failed을 반복하는 식


## 테스트를 쓸 때 고려 사항
 여기까지 간단한 테스트 방법을 썼는데, 여기서 이야기는 좀 다르게 테스트를 쓸 때의 주의점에 대해 설명하려고 합니다.

 테스트라는 것은 안전해야 합니다(다만"안전"에 대해서는 경우에 따라 변하는 경우도 있습니다). 또 가능한 한 환경이 바뀌어도 같은 결과가 나와야 합니다.

 예를 들면 안전이라는 관점에서 말하면 다음과 같은 것이 있습니다.

* 외부에 접근하지 않고 영향를 주지 않는다

 또 결과가 환경에 의존하지 않는다는 부분에서 말하면

* 리소스를 전제로 하지 않는다
* 실행 중에 사용자의 개입이 발생하지 않는다

그 이외에도

* 실행시간이 오래걸리지 말 것

같은 것입니다.

### 외부에 액세스 하지 않고 영향을 주지 않는다
 외부에 접근하는 테스트를 쓰면 어떻게 될까요. 예를 들면 외부의 Web API를 테스트에서 접근한다고 하면 테스트의 요청이 그 서비스로 가게되고 그 서비스에 영향을 끼쳐 버립니다. 또 테스트에서 실서비스의 데이터베이스에 접근하고 하고 영향을 가하는 경우도 문제가 있습니다. 이외에도 외부에 의존하고 있다고 했을때 그 서비스가 다운되어 있으면서 동시에 테스트도 실패해버리는 일이 일어납니다.

  이렇게 외부로 접속하는 테스트를 쓰면"외부에 피해를 주거나""외부에 시험 결과가 영향을 받는"것 같은 문제가 일어날 수 있습니다. 그래서 최대한 테스트에서는 외부접근은 하지 않도록 합시다.
 외부이란 여러가지가 있는데, 예를 들면 이하와 같은 것이 있습니다.

*  localhost이외의mysql, memcached, MogileFS, etc...
*  Web API
*  이메일

 이런것에 접근하지 않으면서 테스트하려면 몇가지 방법이 있는데, 가령

* 테스트때만 DB의 dsn을 쓰고 싶다
  * dsn의 설정을 테스트용으로 한다
  * DBIx::RewriteDSN을 사용해 고쳐 쓴다
* 외부 API에 접근 하는 메소드를 쓰고 싶다
  * 타입글로브(Typeglob)를 고쳐 쓴다
  * Test::Mock::Guard를 쓴다

같은 방법이 있습니다.

 하지만 너무 여러가지로 테스트를 변경하면 테스트의 의미가 없어져 버리므로 가능한 한 변경 등은 최소한으로 되도록 합시다.

### 리소스를 전제로 하지 않는다

 테스트할 때 테스트 데이터가 DB에 들어 있다는 전제하에서 테스트를 하면 어떤 사람의 테스트가 통과하는데 다른 사람은 통과하지 않는  것 같은 일이 일어납니다. 그래서 가능한 리소스가 있다고 가정하지 않고, 테스트시에 데이터를 넣는 등 같이 하시면 좋습니다.

### 실행 중에 사용자의 개입이 발생하지 않는다

 실행 중에 사용자 개입이 발생하는 테스트를 작성한 경우 테스트 실행을 자동화하는 것이 어려워집니다. 그래서 테스트중에는 사용자의 조작을 필요로 하지 않도록 테스트를 작성하는 것이 좋습니다. input이 필요한 모듈을 테스트할 경우에는 그 부분만 바꾸는식으로 하면 좋을(예를 들면 STDIN을 바꾸는 등))지도 모릅니다.

### 실행시간이 오래걸리지 말 것

 실행 중에 시간이 걸리는 테스트의 경우 테스트 실행을 위해 개발이 중지되는 듯한 문제가 됩니다. 물론 자신의 PC뿐 아니라 CI도구 등을 이용해 계속적으로 테스트를 실행해야 하지만 그래도 1~2시간도 걸리는 테스트를 작성해 버린 경우 테스트 실행이 어려워집니다. 그래서 가능한 한 실행에 시간이 오래 걸리지 않도록 테스트를 작성하도록 유의합시다.

 테스트 시간이 걸리는 이유와 그 해법은 종종 있는데, 가령

* 내부에서 sleep을 많이 실행했다
  * Test::Time등을 이용하여 sleep자체를 대체한다
* 필요 이상으로 테스트 데이터를 넣고 있었다
  * 저장소 데이터로 정말 필요한 테스트만 데이터를 넣는다
  * 저장소에 들어 있는 데이터에 가능한한 연결되지 않는 클래스를 만든다(구현적으로)

등이 있습니다.


<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/2.1/jp/"><img alt="クリエイティブ・コモンズ・ライセンス" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/2.1/jp/88x31.png" /></a><br />이 작품은 <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/2.1/jp/">크리에이티브 커먼즈 표시 - 비영리 - 상속 2.1 일본 라이센스의 아래에서 제공됩니다.</a>
