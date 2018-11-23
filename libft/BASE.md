# BASE

## Don't RE-CODE ! RE-USE !

### bzero

```c
#include "libft.h"

void	ft_bzero(void *s, size_t n)
{
	char	*str;
	size_t	i;

	str = (char*)s;
	i = 0;
	while (i < n)
	{
		str[i] = 0;
		i++;
	}
}
```

```c
#include "libft.h"

void	ft_bzero(void *s, size_t n)
{
	if (n != 0)
		ft_memset(s, '\0', n);
}
```

### memalloc

```c
#include <stdlib.h>

void	*ft_memalloc(size_t size)
{
	size_t	i;
	void	*ret;

	i = 0;
	if (!(ret = malloc(size)))
		return (NULL);
	while (i < size)
	{
		*(unsigned char*)(ret + i) = 0;
		++i;
	}
	return (ret);
}
```

```c
#include "libft.h"

void	*ft_memalloc(size_t size)
{
	void	*mem;

	if (!(mem = malloc(sizeof(void*) * size)))
		return (NULL);
	ft_bzero(mem, size);
	return (mem);
}
```

### isalnum

```c
int		ft_isalnum(int c)
{
	if ((c >= '0' && c <= '9') || (c >= 'A' && c <= 'Z') ||
			(c >= 'a' && c <= 'z'))
		return (1);
	return (0);
}
```

```c
#include "libft.h"

int	ft_isalnum(int c)
{
	if (ft_isalpha(c) == 1 || ft_isdigit(c) == 1)
		return (1);
	return (0);
}
```

### strclr

```c
void	ft_strclr(char *s)
{
	int		i;

	if (!s)
		return ;
	i = 0;
	while (s[i])
	{
		s[i] = '\0';
		++i;
	}
}
```

### itoa

**ft_itoa.c**
```c
static int		ft_int_lenght(int n)
{
	int		i;

	i = 0;
	if (n < 0)
		++i;
	if (n == 0)
		return (1);
	while (n)
	{
		n /= 10;
		++i;
	}
	return (i);
}

// [...]
```

**ft_intlen.c**
```c
int		ft_intlen(int n)
{
	int		i;

	i = 0;
	if (n < 0)
		++i;
	if (n == 0)
		return (1);
	while (n)
	{
		n /= 10;
		++i;
	}
	return (i);
}
```

## I hate magic values !

### itoa

```c
// [...]

char		*ft_itoa(int n)
{
	int		i;
	char	*ret;
	int		fl;

	fl = 0;
	i = ct_nb(n);
	if (n < 0)
	{
		if (n == -2147483648)
			return (ft_strdup("-2147483648"));
		++fl;
		n *= -1;
	}
	// [...]
	return (ret);
}
```

use `limit.h` !

## Use --Wconversion !

### itoa

```c
static unsigned int	numlen(int n)
{
	unsigned int size;

	// [...]
	return (size);
}

char				*ft_itoa(int n)
{
	char		*str;
	int			length;
	long		nb;

	length = numlen(n);
	// [...]
	return (str);
}
```

## CONST IS CONST !

```c
#include "libft.h"

int	ft_memcmp(const void *s1, const void *s2, size_t n)
{
	unsigned char	*str1;
	unsigned char	*str2;
	size_t			i;

	i = 0;
	str1 = (unsigned char*)s1;
	str2 = (unsigned char*)s2;
	// [...]
	return (0);
}
```

```c
#include <string.h>

int		ft_memcmp(const void *s1, const void *s2, size_t n)
{
	size_t				i;
	unsigned char const	*ptr1;
	unsigned char const	*ptr2;

	i = 0;
	ptr1 = s1;
	ptr2 = s2;
	// [...]
	return (ptr1[i] - ptr2[i]);
}
```

## About memdel

```c
void	ft_memdel(void **ap)
{
	free(*ap);
	*ap = NULL;
}
```

```c
void	ft_memdel(void **ap)
{
	if (ap != NULL)
	{
		free(*ap);
		*ap = NULL;
	}
}
```

## About memmove (and Black magic is bad)

```c
#include "libft.h"

void	*ft_memmove(void *dest, const void *src, size_t n)
{
	char	tmp[n][n];

	ft_memcpy(tmp, src, n);
	ft_memcpy(dest, tmp, n);
	return (dest);
}
```

```c
#include "libft.h"

void	*ft_memmove(void *dst, const void *src, size_t len)
{
	unsigned const char	*source;
	unsigned char		*dest;
	size_t				i;

	source = (unsigned const char*)src;
	dest = (unsigned char*)dst;
	i = 0;
	if (dst > src)
	{
		while (++i <= len)
			dest[len - i] = source[len - i];
	}
	else
	{
		while (i < len)
		{
			dest[i] = source[i];
			i++;
		}
	}
	return (dst);
}
```

use `memcpy` !

## str[i] is better than *(str + i)

_GRRRRRRRRR_

## We are safe !

```c
#include "libft.h"
#include <stdlib.h>

static size_t	ft_strlen_protect(const char *s)
{
	size_t	i;

	i = 0;
	while (s && s[i])
		i++;
	return (i);
}

static char		*ft_strcat_protect(char *s1, const char *s2)
{
	int		i;
	int		j;

	i = ft_strlen_protect(s1);
	j = -1;
	while (s2 && s2[++j])
		s1[i + j] = s2[j];
	s1[i + j] = '\0';
	return (s1);
}

static char		*ft_strcpy_protect(char *dst, const char *src)
{
	int		i;

	i = -1;
	while (src && src[++i])
		dst[i] = src[i];
	dst[i] = '\0';
	return (dst);
}

char			*ft_strjoin(char const *s1, char const *s2)
{
	size_t	size;
	char	*join;

	size = ft_strlen_protect(s1) + ft_strlen_protect(s2);
	if (!(join = (char*)malloc(sizeof(*join) * (size + 1))))
		return (0);
	return (ft_strcat_protect(ft_strcpy_protect(join, s1), s2));
}
```

```c
#include "libft.h"

char	*ft_strjoin(char const *s1, char const *s2)
{
	char *str;

	if (!(char*)s1 || !(char*)s2)
		return (NULL);
	if (!(str = ft_strnew(ft_strlen(s1) + ft_strlen(s2) + 1)))
		return (NULL);
	ft_strcat(str, s1);
	ft_strcat(str, s2);
	return (str);
}
```

## Don't CMD+C CMD+V !

```c
#include <stdlib.h>

static int		ct_i(char const *s)
{
	int		i;

	i = 0;
	while (s[i] == ' ' || s[i] == '\t' || s[i] == '\n' || s[i] == ',')
		++i;
	return (i);
}

static int		ct_j(char const *s)
{
	int		j;

	j = 0;
	while (s[j])
		++j;
	--j;
	while (s[j] == ' ' || s[j] == '\t' || s[j] == '\n' || s[j] == ',')
		--j;
	return (j);
}

char			*ft_strtrim(char const *s)
{
	char	*ret;
	int		i;
	int		j;
	int		k;

	if (!s)
		return (NULL);
	i = ct_i(s);
	j = ct_j(s);
	if (i < j)
	{
		if (!(ret = (char*)malloc(sizeof(char) * (j - i + 2))))
			return (NULL);
	}
	else
	{
		if (!(ret = (char*)malloc(sizeof(char))))
			return (NULL);
	}
	k = 0;
	while (i <= j)
		ret[k++] = s[i++];
	ret[k] = '\0';
	return (ret);
}
```

## Naming !

```
	ft_list_at.c
	ft_list_foreach.c
	ft_list_foreach_if.c
	ft_list_last.c
	ft_list_push_back.c
	ft_list_push_front.c
	ft_list_remove_if.c
	ft_list_reverse.c
	ft_list_size.c
	ft_lstadd.c
	ft_lstdel.c
	ft_lstdelone.c
	ft_lstiter.c
	ft_lstmap.c
	ft_lstnew.c
```

```
	ft_is_negative.c
	ft_is_prime.c
	ft_is_sort.c
	ft_isalnum.c
	ft_isalpha.c
	ft_isascii.c
	ft_isdigit.c
	ft_isprint.c
```

## includes in header
### yes
```c
#include <limits.h> // types limit
#include <string.h> // size_t, NULL ...
#include <stdint.h> // uint32_t ...
#include <stdbool.h> // boolean 
```
### nope
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
```

# Optimisation !

## Write is big a snail !

### putstr: Group the call !

```c
#include <unistd.h>

static void		ft_putchar(char c)
{
	write(1, &c, 1);
}

void			ft_putstr(char const *s)
{
	int		i;

	i = 0;
	while (s[i])
	{
		ft_putchar(s[i]);
		++i;
	}
}
```

```c
#include <unistd.h>

void	ft_putstr(char const *s)
{
	write(1, s, ft_strlen(s));
}
```

### putnbr: malloc > write

```c
void	ft_putnbr_fd(int n, int fd)
{
	if (n == -2147483648)
	{
		ft_putchar_fd('-', fd);
		ft_putchar_fd('2', fd);
		n = 147483648;
	}
	if (n < 0)
	{
		ft_putchar_fd('-', fd);
		n = -n;
	}
	if (n / 10 > 0)
	{
		ft_putnbr_fd((n / 10), fd);
		ft_putchar_fd((n % 10 + 48), fd);
	}
	else
		ft_putchar_fd((n + 48), fd);
}
```

```c
void	ft_putnbr_fd(int n, int fd)
{
	char *str;

	str = ft_itoa(n);
	ft_putstr_fd(str, fd);
}
```

## Reduce malloc call
_allign memory_

```c
	if (!(ret = (char**)malloc(sizeof(char*) * (ct_wrds(s, c) + 1))))
		return (NULL);
	i = 0;
	j = 0;
	while (s[i] && j < ct_wrds(s, c))
	{
		// [...]
		if (!(ret[j] = (char*)malloc(sizeof(char) * (ct_char(s, c, i) + 1))))
			return (NULL);
		// [...]
	}
```

## lst = cache miss

_vectors are better_

# Improvements

- vector
- btree
- matrix